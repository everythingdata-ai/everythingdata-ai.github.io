---
layout: post
title: Manage GCP architecutre with Terraform - Part 3 - Cloud Composer
categories: [Data Engineering, Terraform]
---

After having worked with Terraform in a previous project and being reunited with it recently, I thought it might be time to get certified.
I checked their website and there are two certifications, one of them seems to be easier to get : the Terraform Associate (003)
In the upcoming days I will be checking various Terraform courses to deepen my knowledge and fill any potential gaps, for eventually getting certified sometime this year.

These are my notes from the [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=SPcwo0Gq9T8)

### 1 - Infrastructure as Code (IaC) concepts

#### What is Iac ?

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


#### Infrastructure lifecycle

IL is having a number of clearly defined and distinct work phases which are used by DevOps Engineers to plan, design, build, test, deliver, maintain and retire cloud infrastructure.
While the lifecycle is well defined for software engineering (SDLC) no one agreed on a cloud infrastructure lifecycle norm.
One that is common is Day 0, Day 1 and Day 2 :
Day 0 : Plan and Design
Day 1 : Develop and iterate
Day 2 : Go live and maintain
IaC starts on Day 0.
#### Advantages of Infrastructure Lifecycle

- Reliability : IaC makes changes idempotent (no matter how many times you run IaC, you will always end up with the same state that is expected), consistent, repeatable and predictable.
- Manageability : enable mutation via code, revised with minimal change
- Sensibility : avoid financial and reputational losses to even loss of life (considering government and military dependencies on infrastructure)

#### Provisioning vs Deployment vs Orchestration

- Provisioning : 
Prepare a server with systems, data and software, and make it ready for network operation.
You can provision a server using Configuration Management tools like Puppet, Ansible, Chef, Bash scripts, PowerShell or Cloud-Init.
When you launch a cloud service and configure it you are "provisioning"

- Deployment : 
The act of delivering a version of your application to run a provisioned server.
Deployment could be performed via AWS CodePipeline, Harness, Jenkins, Github Actions, CircleCI.

- Orchestration :
The act of coordinating multiple systems or services. It's a common term when working with microservices, containers and Kubernetes

#### Configuration drift

Configuration drift is when provisioned infrastructure has an unexpected configuration change due to : 
- team members manually adjusting configuration options
going unnoticed, this could lead to a loss or breach of cloud services and residing data, or- malicious actors
- side effect from APIs, SDK or CLIs

If  result in interruption of services or unexpected downtime.

You can detect configuration drift through :
- A compliance tool (AWS Config, Azure Policies, GCP Security Health Analysis)
- Built-in support for drift detections (AWS CloudFormation Drift Detection)
- Storing the expected state (Terraform)

You can correct it through :
- A compliance tool that can remediate misconfiguration (AWS Config)
- Terraform refresh and plan commands
- Manually correcting the configuration (not recommended)
- Tearing down and setting up the infrastructure again

You can prevent it by :
- Immutable infrastructure (always create and destroy, never reuse). Servers are never modified after they are deployed (baking AMI images or containers via AWS Image Builder, or a build server like GCP Cloud Run)
- Using GitOps to version control our IaC, and peer review every single change to infrastructure through Pull Requests

#### What is GitOps ?

GitOps is when you take IaC and use a git repository to ontroduce a formal process to review and accept changes to infrastructure code, once that code is accepted, it automatically triggers a deploy.

![image](https://github.com/user-attachments/assets/6a182736-a48b-4285-afd6-1afd9047f42f)

### 2 - Terraform basics

#### HashiCorp products
- Boundary : secure remote access to systems based on trusted identity
- Consul : provdes a full-features service mesh for secure service segmentation across any cloud or runtime environment, and distributed key-value storage for application configuration
- Nomad : scheduling and deployment of tasks across worker nodes in a cluster
- Packer : tool for building VM images for later deployment
- Terraform : IaC software for privisioning and adapting virtual infrastructure
- Terraform Cloud : a place to store and manage IaC in the cloud or with teams
- Vagrant : building and maintenance of reproducible software-development environments via virtualization technology
- Vault : secrets management, identity-based access, encrypting application data 
- Waypoint : modern workflow to build, deploy and release across platforms

#### What is Terraform

An open-source and cloud-agnostic IaC tool.
TF uses declarative configuration files written in HashiCorp Configuration Language (HCL)
Some notable feature of TF include : installable modeuls, plan and predict changes, dependency graphing, state management, Terraform Registry with +1000 providers

Terraform Cloud is a SaaS for remote state storage, version control integrations and collaborate on infrastructure changes in a single unified web portal (generous free-tier for the first 5 users)

Here is the Terraform lifecycle : 

![image](https://github.com/user-attachments/assets/4b5f60bb-a8f7-4de4-9b64-8d7aa270a3d9)


#### Change automation

Change Management is a standard approach to apply change and resolve conflicts brought about by change.
Change Automation is a way of automatically creating a consistent, systematic and predictable way of managing change request via controls and policies.
TF uses Change Automation in the form of Execution Plans and Resources graphs to apply and review complex changes.
