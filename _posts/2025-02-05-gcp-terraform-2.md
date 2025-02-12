---
layout: post
title: Manage GCP architecutre with Terraform - Part 2
categories: [Data Engineering, GCP]
---

After the first part of the series about managing GCP infrastructure with Terraform, today I will continue with more use cases.
In this article, we can see how to setp up a Cloud Run Function, and AlloyDB instance and a Data Fusion Instance using Terraform.

### Cloud Run Function through the UI
First let's create a Cloud Run Function through the GCP user interface to understand how it works.

Enable the necessary APIs in GCP :
- Cloud Build API
- Artifact Registry API
- Cloud Logging API
- Cloud Run Admin API
- Cloud Functions API

[Go to the Cloud Run functions Overview page](https://console.cloud.google.com/functions/list) and click on Create function

![image](https://github.com/user-attachments/assets/963707e8-1f7e-4bee-b664-d7e756acf1f8)

Set a trigger to launch the function when a new object is successfully created in a GCS bucket :

![image](https://github.com/user-attachments/assets/2816ba23-39e6-43e0-81cd-ed4fd05359fc)

In the Source code tab, choose Python and use this code that converts an XML file to JSON format in the main.py :

```python
import functions_framework
from google.cloud import storage
import xml.etree.ElementTree as ET
import json
import os
from urllib.parse import urlparse

# Initialize the Cloud Storage client
storage_client = storage.Client()

def xml_to_dict(element):
    """Convert XML to dictionary recursively."""
    result = {}
    
    # Handle attributes
    for key, value in element.attrib.items():
        result[f"@{key}"] = value
    
    # Handle child elements
    for child in element:
        child_data = xml_to_dict(child)
        if child.tag in result:
            if not isinstance(result[child.tag], list):
                result[child.tag] = [result[child.tag]]
            result[child.tag].append(child_data)
        else:
            result[child.tag] = child_data
    
    # Handle text content
    if element.text and element.text.strip():
        if not result:
            result = element.text.strip()
        else:
            result["#text"] = element.text.strip()
    
    return result

@functions_framework.http
def convert_xml_to_json(request):
    """Cloud Function to convert XML from HTTP URL to JSON with fixed output location"""
    try:
        # Fixed input URL
        url = "https://storage.cloud.google.com/gigawatt-raw-data-test/17X100A100A0001A_R15_17Y100A100R0629X_CRAE_0324_01707_00001_00001.xml"
        
        # Fixed output location
        output_bucket_name = "gigawatt-processed-data-test"
        output_file_name = "test.json"
        
        # Parse the URL to get bucket and object name
        parsed_url = urlparse(url)
        path_parts = parsed_url.path.strip('/').split('/')
        bucket_name = path_parts[0]
        object_name = '/'.join(path_parts[1:])
        
        # Get the file directly from GCS
        bucket = storage_client.bucket(bucket_name)
        blob = bucket.blob(object_name)
        
        # Download to temporary file
        temp_input_path = '/tmp/input.xml'
        blob.download_to_filename(temp_input_path)
        
        # Parse XML and convert to dictionary
        tree = ET.parse(temp_input_path)
        root = tree.getroot()
        xml_dict = xml_to_dict(root)
        
        # Convert to JSON
        json_data = json.dumps(xml_dict, indent=2)
        
        # Upload JSON to output bucket
        output_bucket = storage_client.bucket(output_bucket_name)
        output_blob = output_bucket.blob(output_file_name)
        output_blob.upload_from_string(json_data, content_type='application/json')
        
        # Clean up temporary files
        os.remove(temp_input_path)
        
        return {
            'message': 'Conversion successful',
            'input_url': url,
            'output_file': f'gs://{output_bucket_name}/{output_file_name}'
        }
        
    except Exception as e:
        return f'Error: {str(e)}', 500
```

For the requirements.txt file, put these libraries : 

```
functions-framework==3.*
google-cloud-storage==2.*
cloudevents==1.*
cloud-sql-python-connector==1.*
psycopg2-binary==2.*
Use this trigger event to test the function :
```

```json
{
  "input_bucket": "gs://gigawatt-raw-data-test",
  "input_file": "17X100A100A0001A_R15_17Y100A100R0629X_CRAE_0324_01707_00001_00001.xml",
  "output_bucket": "gs://gigawatt-processed-data-test",
  "output_file": "test.json"
}
```

Now every time you drop an XML file in the specified bucket, it will be automatically converted to JSON and uploaded to another bucket.

### Cloud Run Function through Terraform

After getting an idea on how Cloud Run Functions work, let's deploy the same function but using Terraform only.

First, add the necessary role to your Terraform Service account :

```bash
gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/cloudfunctions.developer"
```
Next create a folder for your functions called functions, and inside it create a folder for the xml-to-json coversion, containing a main.py and requirements.txt files and copy the same code from before.

And in the terraform folder, create a new file that I will call gcf.tf (for Google Cloud Functions).
Inside it, put the following code :

- First a code to compress your main.py and requirements.txt files into a zip file :

```yaml
# Cloud Run Functions requires a zip file of the function source code
data "archive_file" "function-source" {
  type        = "zip"
  output_path = "../functions/xml-to-json-converter/function-source.zip"
  source_dir = "../functions/xml-to-json-converter/"
}
```

- Second the code to upload that zip file to GCS bucket :
 
```yaml
# Upload the zip file to the functions bucket
resource "google_storage_bucket_object" "xml_to_json_converter_function_source" {
  name   = "xml-to-json-converter/function-source.zip"
  source = "../functions/xml-to-json-converter/function-source.zip"
  bucket = "gigawatt-functions"
}
```

- And finally the configuration of your Google Cloud Run Function :
 
```yaml
# Create the Cloud Run Function
resource "google_cloudfunctions2_function" "xml_to_json_function" {
  name        = "xml-to-json-converter"
  location    = var.region
  description = "Function to convert XML files to JSON"

  build_config {
    runtime     = "python310"
    entry_point = "convert_xml_to_json"
    source {
      storage_source {
        bucket = "gigawatt-functions"
        object = "xml-to-json-converter/function-source.zip"
      }
    }
  }

  service_config {
    max_instance_count    = 1
    available_memory      = "256M"
    timeout_seconds      = 60
    service_account_email = "terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com"
  }

  # Trigger the function when a new file is uploaded to the raw data bucket
  event_trigger {
    trigger_region = var.region
    event_type    = "google.cloud.storage.object.v1.finalized"
    event_filters {
      attribute = "bucket"
      value    = "gigawatt-raw-data"
    }
  }
} 
```
Now all you have left to do is run `terraform apply` and your function will be deployed to GCP in no time.

