---
layout: post
title: Create a CI/CD pipeline using Terraform Cloud
categories: [AI]
---

In the previous posts, I have been using Terraform to manage my GCP infrastructure.
The version I was using was Terraform Community Edition : a free, downloadable tool that you interact with on the command line. 
It lets you provision infrastructure on any cloud provider and manages configuration, plugins, infrastructure, and state.

Terraform also has a Cloud based solution, which is HCP Terraform : a SaaS application that runs Terraform in a stable, remote environment and securely stores state and secrets. 
It includes a rich user interface that helps you better understand your Terraform operations and resources, allows you to define role-based access controls, and offers a private registry for sharing modules and providers.
The free-tier of HCP Terraform offers up to 500 resources per month free of charge, so it's a great companion to your Terraform CLI.   

### Github project 

I will create a new Github project for this blog.

If this is your first time using Github, here is how to connect to your Github account using SSH :

Open a Git Bash window and create an SSH key using ```ssh-keygen -t ed25519 -C "your-email@gmail.com"```
Click Enter 3 times for the default filename and passphrase, or choose your own.

Copy your new SSH key using ```clip < ~/.ssh/id_ed25519.pub``` or by manually opening the file.

Go to your Github account, click on your icon on the top right > Settings > SSH and GPG keys > New SSH Key
Paste your SSH key and click on Add SSH key.

![image](https://github.com/user-attachments/assets/bc11c4be-9826-43c3-90a9-f0aa0b510e07)

You are now connected to your Github account, and can clone your newly created project using ```git clone git@github.com:your-username/your-project.git```

### HCP Terraform 

First let's login at [app.terraform.io](https://app.terraform.io/) using GitHub :

![image](https://github.com/user-attachments/assets/6afc0ba7-4861-4716-80f9-ae7ed44077be)

Next create your organization, and a new Workspace, I will be choosing a Version Control Workflow :

![image](https://github.com/user-attachments/assets/f8be70f7-b319-4b16-b4ff-ed02baf9faaa)

Next, open a prompt window and type ```terraform login```

A new window will open on your browser to generate a token.

![image](https://github.com/user-attachments/assets/4004101f-7f18-4b2d-96f9-4784f8a12fcb)

Click on Generate token, and copy your token on the prompt window.

![image](https://github.com/user-attachments/assets/80f19402-fe9f-431f-825a-e2d6b6c0db43)

In your project, create a ```terraform``` folder, and inside it your main.tf file to configure HCP Terraform as the backend : 

```yaml
terraform { 
  cloud { 
    
    organization = "data-engineer" 

    workspaces { 
      name = "gcp-test" 
    } 
  } 
}
```

And run ```terraform init``` :

![image](https://github.com/user-attachments/assets/0be306ea-c2da-46f8-8dca-f17fce9e99c5)

And finally run ```terraform apply``` to start the first run for this workspace.
You can find your run on your HCP Terraform UI, in the Runs tab :

![image](https://github.com/user-attachments/assets/6bca87cf-d8d0-4e0d-98a8-902270950e40)

In your workspace settings, set your Terraform Working Directory to the Terraform folder you created :

![image](https://github.com/user-attachments/assets/eaee8a27-1ee0-40ee-ba79-e1a4eca75e27)

Now everytime you push any changes to your Github project, Terraform will run automatically.

### GCP Credentials

In Terraform documentation for the GCP provider, the authentication is done by pointing to the location of the JSON key file, which is not a suitable approach for Terraform Cloud.

We can set the GCP credentials by creating a variable named gcp-creds in a variables.tf file :

```yaml
variable "gcp-creds" {
  default     = ""
}
```

The main.tf file will look like this : 

```yaml
provider "google" {
  credentials = var.gcp-creds
  project     = var.project
  region      = var.region
}
```

Then we create a variable in the Terraform Cloud UI named gcp-creds and we populate it with the content of JSON key file as its value :

![image](https://github.com/user-attachments/assets/5257f440-31b9-48dc-ad0e-e9339412af45)

Please don't forget to set up the variable as sensitive.

The runs will then go from this : 

![image](https://github.com/user-attachments/assets/9b3103f7-d920-4fcd-9f70-5e8310fa8526)

To this :

![image](https://github.com/user-attachments/assets/89c54b94-1c7e-4807-a295-34560c01bd9a)
