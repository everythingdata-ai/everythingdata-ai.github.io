---
layout: post
title: Manage GCP architecutre with Terraform
categories: [Data Engineering, GCP]
---

One of the most important advances in the data engineering world in the recent years is the embracing of DevOps practieces in data projects, a.k.a DataOps.
And one of the main aspects of that is defining the infrastructure as code, in contrast to just setting up everything by hand in the Google Cloud Console for example.
Terraform is present in almost any data engineering job posting, in this article, you will find the steps to set-up Google Cloud Storage buckets and a Cloud SQL instance using Terraform.


### 1 - What is Terraform ?

Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces (APIs). Providers enable Terraform to work with virtually any platform or service with an accessible API.

The core Terraform workflow consists of three stages:

- Write: You define resources, which may be across multiple cloud providers and services. For example, you might create a configuration to deploy an application on virtual machines in a Virtual Private Cloud (VPC) network with security groups and a load balancer.
- Plan: Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration.
- Apply: On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. For example, if you update the properties of a VPC and change the number of virtual machines in that VPC, Terraform will recreate the VPC before scaling the virtual machines.

![image](https://github.com/user-attachments/assets/7416704a-89d8-4969-b998-674d4f54a501)

### 2 - Create a GCS bucket 

First let's see how we can manage a previously created GCS bucket using Terraform.

Go to [GCS](https://console.cloud.google.com/storage/) and click Create :
Choose a name for your bucket : gigawatt-test
![image](https://github.com/user-attachments/assets/4ba1198d-1e3c-47e1-85d3-9d1e7ab041f1)

Choose a region for your bucket : europe-west9
![image](https://github.com/user-attachments/assets/ac816f02-b201-4da7-a701-4ed3c15b7066)


Choose a storage class for your data : Standard
![image](https://github.com/user-attachments/assets/5724abfe-060f-458d-96e0-a2da71d41159)


Choose the control access to the bucket 
![image](https://github.com/user-attachments/assets/cb2f0f92-039f-49ff-9740-09c56bbe541d)


Choose how to protect the data and click Create
![image](https://github.com/user-attachments/assets/40b0d1dc-9671-4a5c-bfa0-9e4c5266fecd)


Prevent public access to the bucket
![image](https://github.com/user-attachments/assets/ccafbe42-a217-455d-9044-76d9b91de877)


### 3 - Create a Service Account for Terraform

Go to [IAM > Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts)
Click on Create Service Account and define the name
![image](https://github.com/user-attachments/assets/23e460ea-b42b-4aef-a66f-9cda3ddd322e)


Then click on Continue and Done, we will define the roles through the console.

Install the gCloud CLI [here](https://cloud.google.com/sdk/docs/install) and after initializing it do the [authentication](https://cloud.google.com/docs/authentication/set-up-adc-local-dev-environment) :

```shell
gcloud auth application-default login --impersonate-service-account terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com
```

You can find your service account email in the IAM Service Accounts or by using this command :

```
gcloud iam service-accounts list
```

Give this Service Account the necessary Roles :

```
gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/storage.admin"
```

Create an access key for your account  and save it to a JSON file :

```
gcloud iam service-accounts keys create terraform-key.json --iam-account=terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com
```

### 4 - Set up the project

Start a new project in VSCode, and create a folder called Terraform and switch to it :

```bash
mkdir terraform
cd terraform
```

Copy the previously created key json file into this folder.

Terraform only reads those files that have .tf or .tfvars extensions. 
There are some files that need to be named in a specific way, whereas others are just resource abstraction and will be executed in the no order unless you mention the dependencies using depends_on. 
Main.tf, variables.tf and terraform.tfvars are the files that need to be created with the exact names.


Create a new file called main.tf containing the following code :

```yaml
provider "google" {
  credentials = file("terraform-key.json")
  project     = "fourth-walker-449914-t1"
  region      = "europe-west9"
}

terraform {
  backend "gcs" {
    bucket  = "gigawatt-test"
    prefix  = "terraform/state"
    credentials = "terraform-key.json"
  }
}
```

Initialize the terraform project :

```
terraform init
```

![image](https://github.com/user-attachments/assets/fe04f956-7be9-4deb-a98c-9e785af4448a)


To test that everything is working correctly, let's push a test file to the GCS bucket.

Create a new file called test.tf with the following code :

```yaml
resource "google_storage_bucket_object" "test" {
  name    = "test-file"
  bucket  = "gigawatt-test"
  content = "test"
}
```

Check the execution plan :

```
terraform plan
```

Execute the plan, when prompted, type yes :

```
terraform apply
```

To avoid typing Yes each time you apply, you can use :

```
terraform apply -auto-approve
```

![image](https://github.com/user-attachments/assets/d36e6645-e27a-43ff-bfa8-d6c2da3ecbe1)


A test file should be created in your bucket :

![image](https://github.com/user-attachments/assets/d0a8bc3b-c5ad-4d44-854c-71eabf386c32)

You can also create Bucket directly thourgh Terraform, instead of the GCP console, by using google_storage_bucket in the main.tf file :

```yaml
resource "google_storage_bucket" "gigawatt_new_files" {
  name          = "gigawatt-raw-data-test"
  location      = var.region
  project       = var.project


  uniform_bucket_level_access = true
  lifecycle_rule {
    condition {
      age = 90
    }
    action {
      type = "SetStorageClass"
      storage_class = "COLDLINE"
    }
  }
}
```

The variables can be defined in a variables.tf file :

```yaml
variable "project" {
  type        = string
  default     = "fourth-walker-449914-t1"
  description = "GCP Project ID"
}

variable "region" {
  type        = string
  default     = "europe-west9"
  description = "GCP Region"
}
```

When dealing with sensitive variables, like passwords, you can first add the variable to your variables.tf file without its value :

```
variable "database_password" {
  description = "The root password for the PostgreSQL instance"
  type        = string
  sensitive   = true
}
```

And then saving the value in the terraform.tfvars file :
```
database_password = "abcABC123!"
```

Do not confuse _terraform.tfvars_ files with _variable.tf_ files. Both are completely different and serve different purposes.

variable.tf are files where all variables are declared; these might or might not have a default value, while variable.tfvars are files where the variables are provided/assigned a value.

### 5 - Create a Cloud SQL instance and connect it to Terraform

Go to [Cloud SQL](https://console.cloud.google.com/sql/instances) and create a new Postgres Sandbox instance :

![image](https://github.com/user-attachments/assets/3c2e0d1d-3651-40bb-8de0-d1f7e84878c5)


You can see the available tiers list using the gCloud CLI command :

```
gcloud sql tiers list
```

Give the Service Account Admin access to Cloud SQL :

```
gcloud projects add-iam-policy-binding fourth-walker-449914-t1 --member="serviceAccount:terraform-test@fourth-walker-449914-t1.iam.gserviceaccount.com" --role="roles/cloudsql.admin"
```

Define your database in the main.tf file :

```yaml
resource "google_sql_database_instance" "postgres_gigawatt_test" {
  name             = "postgres-gigawatt-test"
  region           = var.region
  database_version = "POSTGRES_16"
  root_password    = "abcABC123!"

  settings {
    tier = "db-f1-micro"
    edition = "ENTERPRISE"
    # edition = "ENTERPRISE_PLUS"
    password_validation_policy {
      min_length                  = 6
      reuse_interval              = 2
      complexity                  = "COMPLEXITY_DEFAULT"
      disallow_username_substring = true
      password_change_interval    = "30s"
      enable_password_policy      = true
    }
    deletion_protection_enabled = true
  }

  # set `deletion_protection` to true, will ensure that one cannot accidentally delete this instance by
  # use of Terraform whereas `deletion_protection_enabled` flag protects this instance at the GCP level.
  deletion_protection = true

}
```

You can find all the attributes [here](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/sql_database_instance)

#### 6 - Cloud SQL Password policies

Password policies set define governance in terms of complexity, expiry and reusability. It enforces users to follow a set of standards for password as minimum requirement or rules.Well defined password policies about minimum length, complexity and expiration enforce an additional layer of security for database users using built-in authentication.

Recently CloudSQL for PostgreSQL [launched](https://cloud.google.com/sql/docs/postgres/built-in-authentication#built-in_authentication_for) enforcement of password policy at the instance level as an additional security measure. It is primarily enforce for built-in authentication i.e. authenticate with passwords for database logins.

![image](https://github.com/user-attachments/assets/34bf8096-bc77-4448-8453-aedf374879e8)

