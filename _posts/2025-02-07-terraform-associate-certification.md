---
layout: post
title: Manage GCP architecutre with Terraform - Part 3 - Cloud Composer
categories: [Data Engineering, Terraform]
---

After having worked with Terraform in a previous project and being reunited with it recently, I thought it might be time to get certified.
I checked their website and there are two certifications, one of them seems to be easier to get : the Terraform Associate (003)
In the upcoming days I will be checking various Terraform courses to deepen my knowledge and fill any potential gaps, for eventually getting certified sometime this year.

These are my notes from the [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=SPcwo0Gq9T8)

### 1 - Understand infrastructure as code (IaC)

Manually configuring your cloud infrastructure is a quick and easy way to start using new services but it comes with downsides : 
- mis-configure a service through human error
- hard to transfer configuration knowledge to other team members
- hard to manage the expected state of configuration for compliance

IaC solves those problems by allowing you to write a configuration script to automate creating/updating/destroying cloud infrastructure, as well as sharing/versioning it. 

There are 2 types of Iac tools :
- Declarative : what you see is what you get (explicit), more verbose but zero chance of mis-configuration, uses scripting languages (json, yaml, xml) 
ARM Templates, Azure Blueprints, CloudFormatio, Cloud Deployment Manager, Terraform

- Imperative : you say what you want and the rest is filled in (implicit), less verbose so you could end up with mis-configuration, uses programming languages (Python, Ruby, JavaScript)
AWS Cloud Development Kit, Pulumi

Terraform has the best of both words :

![image](https://github.com/user-attachments/assets/976219c2-d128-49f5-9664-bd53ebc39021)
