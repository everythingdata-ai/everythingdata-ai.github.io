---
layout: post
title: Introduction to Data Engineer / BigQuery omptimization
categories: [Data Engineering, GCP]
---

For the last post of the year, it will be a two-part entry :
- Introduction to Data Engineering concepts from the [IBM course on Coursera](https://www.coursera.org/learn/introduction-to-data-engineering) 
- In french : Optimization techniques for Bigquery from a lecture by [Jean-Baptiste Braun](https://www.linkedin.com/feed/update/urn:li:activity:7169052278300094465/?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28V2%2Curn%3Ali%3Aactivity%3A7169052278300094465%29)

# Introduction to Data Engineering

We tend to forget what we learn fairly quickly, so it's important to remind ourselves of the basics every once in a while.


## What is Data Engineering ? 
Collecting / Processing / Storing / Making data available to users securely
Intersection between Software Engineering and Data Science


## What's the difference between a Database and a Datawarehouse ?
DW is a columnar DB (indexed according to column values in contrast to the row-oriented storage used by databases)


## Types of Data repositories 
There are 2 types of Data Repositories : 
- Transactional - OLTP (Online Transaction Processing System) : Store high volume day to day operational data
Ex : Google Cloud SQL
- Analytical - OLAP (Online Analytical Processing System) : Conduct complex Data Analytics
Ex : Google Big Query


## Architecting the Data Platform

### Data Igestion/Collection : 
Connecting to the source system, transfer data from the source to the data platform (either in streaming or batch modes), maintain collection metadata
	Example Tools : Data Flow, IBM Streams, Amazon Kinesis, Apache Kafka
	
### Data Storage and Integration : 
Store for processing and long-term use, transform and merge extracted data 
	Example Tools : SQL Server, MySQL, Oracle, PostgreSQL / Amazon RDS, SQL Azure
	
### Data Processing : 
Read Data and apply transformations (structuring, normalization, denormalization, cleaning)
	Example Tools : Spreadsheets, Google DataPrep, R, Python
	
### Analysis & User Interface : 
Deliver processed Data to consumers (BI Analysts, Stakeholders, Data scientists)
	Example Tools : Tableau, Power BI, Cognos



## Factors for selecting and designing Data Stores : 
A repository can be a database, data warehouse, data mart, big data store, or a data lake.
Some of the primary considerations for designing a data store : 

### The type of data you want to store :
Relational databases are best used for structured data, which has a well-defined schema and can be organized into a tabular format. 
Non-relational databases, or NoSQL, are best for semi-structured and unstructured data.

There are 4 types of NoSQL Data bases : 
Key-Value
Document : Not the best option if you're looking to run complex search queries and multi-operation transactions
Column
Graph.: Not the best option if you need to process high-volume transactions


### Volume of data :
Data Lake : large volumes of raw data in its native format straight from the source
Big Data Store : when you're dealing with Big Data, that is data that is not only high-volume but also high-velocity, of diverse types, and needs distributed processing for fast analytics

### Intended use of data :
The number of transactions, frequency of updates, type of operations performed on the data,  response time, and backup and recovery requirements all need to be provisioned for in the design of a data store.

Transactional systems, that is systems used for capturing high-volume transactions, need to be designed for high-speed read, write, and update operations. 
Analytical systems, on the other hand, need complex queries to be applied to large amounts of historical data aggregated from transactional systems. 
They need faster response times to complex queries. 
Schema design, indexing, and partitioning strategies have a big role to play in performance of systems based on how data is getting used.

### Storage considerations :
Performance - Throughput and latency, that is, the rate at which information can be read from and written to the storage and the time it takes to access a specific location in storage. 

Availability - Your storage solution must enable you to access your data when you need it, without exception. There should be no downtime. 

Integrity - Your data must be safe from corruption, loss, and outside attack.  

Recoverability - Your storage solution should ensure that you can recover your data in the event of failures and natural disasters.
 
### Privacy, security, and governance needs :
Access control, multizone encryption, data management, and monitoring systems. 
Regulations such as GDPR, CCPA, and HIPAA restrict the ownership, use, and management of personal and sensitive data. 
Data needs to be made available through controlled data flow and data management by using multiple data protection techniques. 
This is an important part of a data store design. 
Strategies for data privacy, security, and governance regulations need to be a of a data store's design from the start. Done at a later stage it results in patchwork.


## Data Wrangling 
Raw data has to undergo a series of transformations and cleansing activities in order to be analytics-ready. 

Data wrangling, also known as data munging, is an iterative process that involves data exploration, transformation, validation, and making data available for a credible and meaningful analysis.
	Example Tools : GSheet, Open Refine, Google DataPrep, Watson Studio Refinery, Trifacta, Python (NumPy / Panda), R (Dplyr, Data.table, Jsonlite)


## Data Governance
Data Governance is a collection of principles, practices, and processes that help maintain the security, privacy, and integrity of data through its lifecycle.

Personal Information and Sensitive Personal Information, that is, data that can be traced back to an individual or can be used to identify or cause harm to an individual, needs to be protected through governance regulations.

General Data Protection Regulation, or GDPR, is one such regulation that protects the personal data and privacy of EU citizens for transactions that occur within EU member states. Regulations, such as HIPAA (Health Insurance Portability and Accountability Act) for Healthcare, PCI DSS (Payment Card Industry Data Security Standard) for retail, and SOX (Sarbanes Oxley) for financial data are some of the industry-specific regulations.

Compliance covers the processes and procedures through which an organization adheres to regulations and conducts its operations in a legal and ethical manner.

Compliance requires organizations to maintain an auditable trail of personal data through its lifecycle, which includes acquisition, processing, storage, sharing, retention, and disposal of data.

Tools and technologies play a critical role in the implementation of a governance framework, offering features such as:

-   Authentication and Access Control.
-   Encryption and Data Masking.
-   Hosting options that comply with requirements and restrictions for international data transfers.
-   Monitoring and Alerting functionalities.
-   Data erasure tools that ensure deleted data cannot be retrieved.

## Data Transformation Techniques
- Typing : casting to appropriate types (float, string, int...)
- Structuring : converting from one type to another (XML/CSV to Database table...)
- Anonymizing/Encypting : ensure privacy and security
- Cleaning : remove duplicates / missing values
- Normalizing : converting to commin units
- Filtering, sorting, aggregating, binning
- Joining/merging

## What is a pipeline ?
Any set of processes that are connected to each other sequentially (the output of one process is passed along as input to the next process in a chain) 

# Optimization techniques for Bigquery

- Eviter SELECT *

- Partitions : comme des tables indépendantes 
petite cardinalité 1000 / less than 10 Go par partition - souvent la date 
https://cloud.google.com/bigquery/docs/partitioned-tables

- Clusters : comme des arbres binaires ou un index (blocs mémoire différents) - 100Mb minimum par cluster
Pour des grosses quantités de données on peut partitionner et clusteriser en même temps, par exemple patition sur la date et cluster sur l'heure

- Cache : rétention de 10Go pour 24 heures, rapide d'accès et gratuit 
Contraintes : il faut exécuter exactement la même requête

- BigQuery BI Engine : fonctionnalité de cache élaboré

- BQ Workload tester : moduler la concurrence 

- Types : filtrer avec WHERE/JOIN sur BOOL, INT, FLOAT ou DATE plutôt que STRING

- Data Skew : distribution non-équilibrée = surcharge d'un worker

- Broadcast join : si on fait un join sur une table relativement petite (150Mb), diffuser cette table en RAM à tous les workers au lieu de faire du shuffling de données sur disque

- Dénormalisation : pour éviter les jointures

- Utiliser une modélisation en Array


