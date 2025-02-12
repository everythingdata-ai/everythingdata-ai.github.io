---
layout: post
title: GCP Storage options
categories: [Data Engineering, GCP]
---

Todat I am starting a new job, and my first task is to define the architecture of the project.
If you're lost between all the services that GCP offers, then you're inthe right place.
In this article, I will list the different storage options, and which one to choose based on your needs.

### File Storage
First, let's start with storing your unstructured or semi-structured files.
When working locally, your hard drive acts as your file storage, whether it's to store your code, your images/videos or any other type of file.
When working on the cloud things are pretty much the same, you need a place to store all those file.

There are three types of storage options offered by most cloud providers: block storage, file storage, and object storage

These are the services that GCP offers for storage needs :

#### Block Storage

Imagine you have a large bookshelf with many shelves, and each shelf can hold a specific number of pages from a book.
Now, let's say you have a collection of books, but they are all different sizes. To efficiently store these books, you decide to divide them into smaller uniform pieces, called blocks, that fit nicely on the shelves.
Each shelf can only store 100 pages from a book. The Great Gatsby has about 200 pages, so each shelf can only store half the book. 
Therefore, this single book will be stored in two separate shelves, with half of the book in one location and the other in another location.

Hard disk drives (HDD) and solid state drives (SSD) that are attached to a computer physically or via a network are examples of block storage devices.
The block storage service offered by GCP is Persistent Disk.
Block storage is ideal for applications that require high performance and low latency storage like databases, high performance computing applications and ETL (Extract Transform Load), among other applications.
You can also store an operating system on your Persistent Disk.


#### File Storage

Block storage is the lowest level abstraction of storage. It provides a low-level interface where you can read from or write to individual blocks of data. 
But it does not inherently understand the concept of files, directories, or the hierarchical structure typically associated with file systems.
File storage is an abstraction built on top of block storage. It introduces the concept of files, directories, and a hierarchical structure for organising and managing data.

Using our bookshelf analogy, with file storage, all you interact with are files and their hierarchies, just like how a book store can organise their books in a structured way to make it easier for customers to find and browse through the selections. 
Books can be arranged alphabetically by author name, by genre, or a combination of both.

This hierarchical file structure is just an abstraction. Behind the scenes, the operating system abstracts away the underlying block storage and instead gives the appearance of a file cabinet with a folder-like structure. 
This simplifies access to applications trying to read or write files on the disk.
Applications don’t need to know the underlying block address to retrieve the files, which makes it easier for the application to interact with the files. 
This ease of interaction comes with a performance cost, which is acceptable for some use cases.

The GCP service for file storage is filestore.

#### Cloud Storage

This is the newest form of storage on the cloud. Object storage stores all data as objects in a flat structure. 
There is no hierarchy, unlike in file storage. But an artificial folder-like hierarchy is imparted to block storage to give it the appearance of having a structure.

Objects can be any file. It could be video, audio, image, text file, Excel file, word document, HTML, CSS, XML, JSON, and so on.

Object storage is highly durable – that is, there is a very low probability that any object stored there will be lost.
Object storage offered by cloud providers usually provides 99.999999999% durability over a year. This is colloquially referred to as 11 nines of durability.
Durability is defined as the probability of not losing an object. A storage system that has a durability of 99.999999999% has a 0.000000001% chance of losing a single object in a year. 
This means that even if you have a million objects stored in object storage, you are likely to only lose a single object in 100,000 years.

The GCP service for object storage is Cloud Storage.

#### When to use each storage type 

![image](https://github.com/user-attachments/assets/7c205d3f-9aa8-4a95-9e19-831212949c46)

Let’s imagine a simple scenario where you would need to use block, file, and object storage together.
Imagine you have been tasked with designing the cloud architecture for a law firm. You need to store a large number of evidence files in different formats (audio, video, image, text, Excel, JSON, and so on).
You need to processes these files, extract the useful information, and make it available for further processing by different people. 
You also need to make it available for direct use to a team of lawyers in different parts of the world.

From a high level, how could you design such a solution?

First, the raw files could be stored using either object storage or file storage. 
Object storage is preferable because it is lower cost. It is also ideal for low cost archival storage if you need to keep files for several years.
To process the files, you can run multiple applications on an EC2 instance that uses block storage. The other alternative is to use file storage.
Block storage is preferable because these processing tasks may include things like transcribing audio files, extracting text from images, improving and stabilising video files, a database that extracts data in JSON format and stores it in a relational database, and so on. 
These are all tasks that require higher performance, which is an ideal use case for block storage.
The processed files are then stored in S3 again before they are loaded into a file system with several instances mounted on it.
The up-to-date processed files must be available for further processing or for direct use by a team of lawyers. Block storage is not ideal here because block storage cannot be shared across multiple instances. 
Object storage is also not ideal because its not mountable (can’t be attached to a compute instance).
In this case, file storage is ideal because it has non of these constraints – it can be shared across multiple instances and it is mountable.

### Database storage

After processing raw files into relational data, they need to be stored in a database.
Here are the available relational data storage services on GCP:

#### Oracle Database
The project I'm working on is the migration of an existing Oracle Database.
For simplicity purposes, it is possible to build on top of the existing project by keeping an Oracle database but moving it to the cloud.

Since September 2024, GCP offers the option to run an Oracle database on GCP through Oracle Database@Google Cloud.
Another option is to create an Oracle database on a virtual machine using Compute Engine.

Here’s a quick comparison of the two options:


| Oracle Database@Google       | Compute Engine                                |
| ---------------------------- | --------------------------------------------- |
| Pre-configured and optimized | Full control over configuration               |
| More expensive               | Better benefit-cost ratio                     |
| Automated backups            | Use of existing Oracle licenses               |
| Direct Oracle support        | Requires expertise in database administration |

Given the size of the data of this project which is rather small (500 Gb), using Compute Engine is recommended. However, it requires a dedicated database administrator.  

Using an Oracle database is one of the most expensive options, mainly due to the license. Here's an estimate of the costs for 500GB of data:
- Compute Engine VM (e2-standard-4): ~97€/month
- 1TB Persistent SSD: ~170€/month
- Oracle License - Bring Your Own License: 0€ or Pay-as-you-go: ~380€/month for the Standard Edition  
    Total cost: 267-647€/month depending on the license.  
    For the Database@Google option, it will cost a few thousand euros per month.

#### Cloud SQL

This service allows you to have a classic relational database, fully managed by GCP, on the cloud. You can choose between three database management systems: MySQL, PostgreSQL, and SQL Server.  
Cloud SQL allows automatic data backup and offers the ability to replicate the database across two different zones (within the same region), as well as a Read-Only version in a different zone.  
It is one of the least expensive options for hosting data. Here's an estimate of the costs for 500GB of data:

- Cloud SQL Instance (db-standard-2) with 2 vCPUs / 7.5GB RAM: ~130€/month
- 500GB of storage: ~100€/month
- High availability: 130€/month
- Automated backups: included  
    Total cost: ~230€/month for a single instance or ~360€/month in HA.

#### AlloyDB

Similar to Cloud SQL, AlloyDB is a fully managed database service providing access to an enhanced and more powerful version of PostgreSQL.  
Applications can connect to AlloyDB through standard PostgreSQL connectors, and queries are made using PostgreSQL syntax.  
This service can handle larger workloads than Cloud SQL, including hybrid transactional and analytical processing, with response times up to 4 times faster.  
What differentiates AlloyDB from Cloud SQL is mainly continuous automatic analysis of query structure and frequency, which allows it to suggest schema improvements or apply optimizations automatically. AlloyDB also offers a columnar data engine, boosting performance for analytical queries.  
The cost of AlloyDB is a bit higher than Cloud SQL, but with the additional features, it remains a good cost/benefit ratio. Here's an estimate of the costs for 500GB of data:

- AlloyDB instance with vCPU (4 cores): 134€/month
- Memory (16GB): 66€/month
- 500GB storage: 120€/month  
    Total cost: 320€/month

#### Cloud Spanner

Cloud Spanner is the most advanced database on GCP, combining relational, graph, key-value, and search workloads. It easily integrates with AI functionalities (LangChain, Vertex AI).  
It is suitable for large amounts of data (+100,000 reads/writes per second).  
Cloud Spanner is also the most expensive option, with a cost of ~2000€ per month for 500GB of storage and 3 compute nodes.

#### BigQuery

Arguably the most used option, BigQuery is GCP's data warehouse service for analytics, allowing real-time analysis on large amounts of data.  
Since BigQuery is serverless, no infrastructure needs to be configured or managed, unlike AlloyDB for example. With the web console, it is one of the easiest services to use on GCP. Another advantage of BigQuery is the tools it offers, such as Dataflow (the equivalent of dbt) and Data Studio.  
BigQuery has a low cost, with 10GB of storage and 1TB of queries available per month for free.

#### When to use each database
Since a picture is worth a thousand words, here is a graphic that will help you decide which database to use :

![image](https://github.com/user-attachments/assets/11696e65-e1cf-44a8-8b6d-bf5500f33d1c)

#### Cloud SQL vs AlloyDB

In the current project I'm working on, the data is mainly transactional, so I was planning to choose Cloud SQL for it, but then AlloyDB cought my attention.
This can be a tough decision. Both are powerful PostgreSQL-based database services on Google Cloud Platform (GCP), but they cater to different use cases and have their own strengths and weaknesses. Here’s a detailed comparison to help you decide:

- Use Cases:

Cloud SQL for PostgreSQL: Ideal for smaller to medium-sized workloads, web applications, content management systems (CMS), and general-purpose database needs.
AlloyDB: Designed for demanding workloads, large datasets, hybrid transactional and analytical processing (HTAP), and data warehousing.

- Performance and Scalability:

Cloud SQL for PostgreSQL: Offers good performance for common workloads but can struggle with large datasets and complex queries. Scalability is limited to horizontal scaling.
AlloyDB: Delivers significantly higher performance and scales up and down seamlessly to handle varying workloads. Offers both horizontal and vertical scaling options.

- Features:
 
Cloud SQL for PostgreSQL: Provides basic database features with built-in high availability and disaster recovery (HA/DR) functionalities.
AlloyDB: Offers advanced features like automatic data tiering, analytics acceleration, and built-in machine learning for optimizing performance and managing complex workloads.

- Cost:

Cloud SQL for PostgreSQL: Generally less expensive than AlloyDB, especially for smaller deployments.
AlloyDB: Can be significantly more expensive due to its advanced features and high performance capabilities.

- Ease of Use:
 
Cloud SQL for PostgreSQL: Simpler to manage with Google handling the underlying infrastructure. Familiar interface for PostgreSQL users.
AlloyDB: Requires more hands-on configuration and management due to its advanced features. Steeper learning curve for those new to PostgreSQL or AlloyDB.

- Overall:
  
Cloud SQL for PostgreSQL: Best for cost-effective, simple, and familiar PostgreSQL experience for smaller to medium-sized workloads.
AlloyDB: Best for demanding workloads, large datasets, high performance, advanced analytics, and tight integration with other GCP services.

I recommend exploring both options further and comparing their features and pricing to find the best fit for your specific requirements. 
As for me, I think I will be choosing AlloyDB to have the best of both worlds : a transactional database with the possibility of having analytics tables.

That was a quick overview of most of the storage options available on Google Cloud Platfom.
In the next post I will give you an overview of the tools to process data in GCP.
