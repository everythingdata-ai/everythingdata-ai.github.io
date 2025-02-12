---
layout: post
title: Manage GCP architecutre with Terraform - Part 3 - Cloud Composer
categories: [Data Engineering, GCP, Terraform]
---

One of the most used services in GCP is Cloud Composer, which is Google Cloud Composer is a fully managed Apache Airflow service that helps you create, schedule, monitor and manage data pipelines/workflows.
In this third part of the Terraform series, you will be able to create a Cloud Composer instance in GCP.
If you need a refresher about Apache Airflow, you check out [Discovering Apache Airflow](https://everythingdata-ai.github.io/airflow-introduction/)

Let's dive right into it :

### Setting up 

Give the Terraform Service Account the necessary roles.
This time, instead of using the Terraform Service Account to manage the instance, I will only be using to create a specific Service Account for Cloud Composer, hence the extra roles (serviceAccountAdmin, projectIamAdmin...)

```
gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/serviceusage.serviceUsageAdmin"

gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/composer.admin"

gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/iam.serviceAccountAdmin"

gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/resourcemanager.projectIamAdmin"
```

Now let's create a new Terraform file, that I called gcc.tf (for Google Cloud Composer).
In this file, you should :

- Enable the required APIs :

```yaml
resource "google_project_service" "composer_apis" {
  for_each = toset([
    "composer.googleapis.com",
    "compute.googleapis.com",
    "artifactregistry.googleapis.com",
    "cloudbuild.googleapis.com",
    "cloudresourcemanager.googleapis.com"
  ])
  service = each.key
  disable_on_destroy = false
}
```

- Create a Service Account for composer : 

```yaml
resource "google_service_account" "composer_sa" {
  account_id   = "composer-service-account"
  display_name = "Service Account for Cloud Composer"
  depends_on   = [google_project_service.composer_apis]
}
```

- Grant that SA the necessary permissions :

```yaml
resource "google_project_iam_member" "composer_roles" {
  for_each = toset([
    "roles/composer.worker",
    "roles/logging.logWriter",
    "roles/monitoring.metricWriter",
    "roles/monitoring.viewer",
    "roles/storage.objectViewer"
  ])
  project = var.project
  role    = each.key
  member  = "serviceAccount:${google_service_account.composer_sa.email}"
}
```

- Create a Composer environment :

```yaml
resource "google_composer_environment" "composer" {
  name   = "gigawatt-composer"
  region = var.region
  
  config {
    software_config {
      image_version = "composer-3-airflow-2.10.2"
      
      env_variables = {
        ENVIRONMENT = "test"
      }
    }

    node_config {
      network    = "default"
      subnetwork = "default"
      service_account = google_service_account.composer_sa.email
    }

    workloads_config {
      scheduler {
        cpu        = 0.5
        memory_gb  = 1
        storage_gb = 1
        count      = 1
      }
      web_server {
        cpu        = 0.5
        memory_gb  = 1
        storage_gb = 1
      }
      worker {
        cpu        = 0.5
        memory_gb  = 1
        storage_gb = 1
        min_count  = 1
        max_count  = 3
      }
    }

    environment_size = "ENVIRONMENT_SIZE_SMALL"
  }

  depends_on = [
    google_project_service.composer_apis,
    google_project_iam_member.composer_roles
  ]
}
```

- Optionally, you can output the Airflow web UI URL :

```yaml
output "airflow_uri" {
  value = google_composer_environment.composer.config[0].dag_gcs_prefix
  description = "The URI of the Apache Airflow web UI"
}
```

Finally run `terraform apply -auto-approve` to put everything in place.
It takes approximately 25 minutes for your Cloud Composer environment to be up and running :

![image](https://github.com/user-attachments/assets/a98ee571-f701-41a3-a444-bff418d75d43)

Basically, an environment corresponds to an instance of Airflow, with a name and a version. 
Each Environment is made of a set of Google Cloud services which usage incurs some cost. 
For instance, the Airflow metadata database is available inside each Environment as a Cloud SQL instance and the Airflow Scheduler is deployed in each Environment as a Google Kubernetes Engine Pod.

You Cloud Composer environment should now be ready to use :

![image](https://github.com/user-attachments/assets/0ab9cf23-64dc-480d-82fd-df086764683b)

Please note that unlike Cloud Run Functions for example, that are only called when needed, Cloud Composer runs 24/7, even when it's not used.
Unfortunately, there is no way to stop or disable a Cloud Composer environment. So make sure to delete it once you're done testing, to not wake up to a spicy GCP bill.

![image](https://github.com/user-attachments/assets/4625fe47-1c84-4b07-9e85-7779a49187d2)

The only plausible way to reduce the costs related to Cloud Composer, especially in development environments, is to use [Environments Snapshots](https://cloud.google.com/composer/docs/composer-3/save-load-snapshots).
Although not very practical, you can set-up a pipeline that destroys the Composer Environment using these 3 steps:
- Save a snapshot of the Environment
- Copy the tasks logs to a backup Cloud Storage bucket
- Delete the Environment and its bucket

The environment snapshot can then be loaded to restore the Environment to the state when the snapshots were created.
