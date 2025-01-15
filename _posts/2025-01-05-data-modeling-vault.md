---
layout: post
title: Data Modeling + The modern Data Vault Stack
categories: [Data Engineering]
---

Today I'm taking a break from Microsoft Fabric.
On the menu : revisiting data modeling, and some reading about the modern data vault stack.

## Data Modeling : Star Schema meets the medallion architecture 

### Introduction
Data Modeling transcends more technologies than anything you'll learn.
In essentially every programming language, you'll first learn about the (data) type system, and later how to create complex types. These ideas of how to:
- have discussions with people to decide which sliver of reality we want to capture, and design the data structures to represent it in our code
- implement these models, and only THEN can we start to write code that does useful things with our data structures.

This applies to software engineering in all popular and modern languages, as well as data engineering and the entire systems integration and analytics ecosystems. 
If you're doing integration - you're moving DATA, and the models of what you're moving are always the critical thing to agree upon. 
If you're doing analysis, the entire success or failure depends on how well your models align to the business and the models of the originating systems.

It's data modeling and data model transformation everywhere, and you'll rarely find a project where these skills and techniques don't help.
The ability to fluently model data quickly, collaborate on those models, and then support an endless output spectrum - if that's the process that we can streamline, then we scale in a truly non-linear ways as team.

A picture might be worth 1,000 words, but a good model is worth 1,000,000 lines of code.

The Star Schema has long been a cornerstone of traditional data warehousing, offering an intuitive and efficient way to organize analytical data. 
On the other hand, the Medallion Architecture, popularized in modern data lakes, focuses on structured data refinement through bronze, silver, and gold layers to ensure reliability and usability at scale. 
But what happens when we combine these two powerful paradigms?

### Star Schema
The star schema simplifies data retrieval for analytics and reporting by organizing data into fact tables and dimension tables. 
Its name comes from its resemblance to a star, where the fact table is at the center, and the dimension tables radiate outward like star points.
Star schema is commonly used for OLAP (Online Analytical Processing) systems where data is primarily queried rather than updated.

Fact Table:
- Central table in the schema that contains quantitative data or metrics that are analyzed (e.g., sales amount, revenue, units sold).
- These tables typically include foreign keys to connect to dimension tables.
- These tables often have columns for numeric measures or granularity defining the level of detail (e.g., sales per transaction or per day).

Dimension Tables:
- Surround the fact table and provide context for the data.
- Contain descriptive attributes (e.g., product name, customer location, time period).
- Typically, denormalized for simplicity and faster querying.

The key caracteristics of a star schema are :
- Denormalization: Dimension tables are often denormalized, meaning they include redundant data to reduce the number of joins required for queries.
- Ease of Use: Star schemas are intuitive and simple to understand, making them ideal for non-technical users.
- Performance: Optimized for read-heavy operations such as reporting and analytical queries.

| Advantages                                                                       | Disadvatanges                                                                                                     |
| -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Simpler Queries: Easy to write SQL queries for analytics                         | Redundancy: Denormalization can increase data storage requirements                                                |
| Faster Query Performance: Reduced joins due to denormalized dimensions           | Scalability: May not perform well with very large dimensions or frequent updates                                  |
| Ease of Maintenance: Straightforward structure simplifies updates and management | Limited Flexibility: Star schemas may not support all use cases, especially those requiring complex relationships |

### Medallion Architecture

Medallion Architecture is a modern data architecture pattern used primarily in data lakes and cloud data platforms (such as Databricks and Azure Synapse Analytics). 
It organizes the flow of data through multiple layers, each designed to improve data quality, optimize performance, and facilitate efficient analytics. 
The architecture is typically structured into three layers, often referred to as the Bronze, Silver, and Gold layers. 
These layers represent stages of data processing and refinement, from raw ingestion to curated, highly structured data ready for analysis.

The key components of a medallion architecture are :


| Layer                                 | Purpose                                                                                                                                   | Characteristics                                                                                                                                                                                                                                                                                                                                                                                                   | Use Cases                                                                                                                                    |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Bronze (Raw Data)                     | Holds raw, untransformed data as it is ingested from source systems                                                                       | (1) Data is collected in its original form, which could include logs, files, databases, and other data sources. (2) Typically stores data in a parquet, ORC, or similar file format for efficient storage and processing. (3) It may include both structured and unstructured data, including JSON, CSV, or raw log files.                                                                                        | Provides a historical record of raw data and is ideal for auditing, troubleshooting, and running initial exploratory analyses                |
| Silver (Cleaned and Transformed Data) | Holds cleaned and structured data that has undergone some level of transformation, such as filtering, deduplication, or basic aggregation | (1) Refined data through cleansing operations to remove errors, handle missing values, and ensure consistency. (2) Can be in a normalized format (tables) where related data from multiple sources is joined, making it easier to use for analysis. (3) It may include transformations such as standardizing date formats, removing outliers, or merging data from multiple tables into single, more usable forms | Typically used by data engineers or data scientists for exploratory analysis, machine learning model building, and intermediate reporting    |
| Gold (Business-Ready Data)            | Contains highly refined, business-ready data that has been aggregated and optimized for end-user analytics and reporting                  | (1) Data is curated, often with the final business logic applied (e.g., aggregation, summarization, and enrichment with external data). (2) Structured for performance, usually through indexing, partitioning, or implementing star schema for ease of use in business intelligence tools. (3) Optimized for specific use cases (e.g., dashboards, reports, KPIs, etc.).                                         | Typically accessed by business analysts, decision-makers, or BI tools for generating dashboards, reporting, and deriving actionable insights |

The benefits of using a medallion architecture include : 
- Data Quality: By passing data through progressively cleaner and more structured layers (Bronze to Gold), the quality of the data improves at each stage. This results in more reliable and accurate data for analysis.
- Scalability: The architecture is designed to handle **large volumes of data** efficiently. Storing raw data in the Bronze layer ensures that there is always a historical record of the data, while transformations in the Silver and Gold layers help ensure that queries are optimized.
- Separation of Concerns: The distinct layers allow for a clear separation between raw data ingestion, intermediate data processing, and final business analytics. This makes it easier to manage data transformations, apply data governance, and debug issues at each stage.
- Flexibility: The architecture allows for different use cases to be served by different layers. Raw data (Bronze) can be leveraged for **data science experiments**, while the Gold layer supports **high-performance reporting** for business users.
- Performance Optimization: By organizing data in stages and applying transformations incrementally, Medallion Architecture optimizes query performance, especially in the Gold layer, where data is ready for high-speed analytics.

However these come at the price of these challenges :

- Data Movement: Transferring data from one layer to the next requires careful orchestration, especially when dealing with large-scale data sets.
- Storage Costs: Storing data in multiple layers (raw, cleaned, and aggregated) can increase storage requirements.
- Complexity in Management: Managing a data pipeline with multiple layers and transformations requires robust tooling and governance to ensure data quality and consistency.

### Integrating Star Schema with Medallion Architecture

The star schema typically resides in the Gold Layer of the medallion architecture, where data is structured for analytical queries.
The benefits of combining these 2 approaches are :

- Scalability and Flexibility: Medallion architecture allows you to manage large-scale, raw data efficiently.
Star schema ensures that the data in the Gold Layer is optimized for analytics.

- Data Lineage and Quality: With the medallion layers, you maintain visibility into the transformations applied to the data.
The structured star schema in the Gold Layer ensures consistent reporting and analytics.

- Support for Multiple Use Cases: Bronze and Silver layers support diverse data needs, including machine learning or ad-hoc exploration.
The Gold Layer with the star schema is tailored for business intelligence and reporting.

- Performance: By structuring the final Gold Layer as a star schema, you reduce query complexity and improve performance for end-users.

Here is a concrete example for an e-commerce website :

- Bronze Layer: Stores raw logs of customer clicks, purchases, and inventory data.
- Silver Layer: Cleans and structures the data into transactional tables (e.g., orders, products, customers).
- Gold Layer: Constructs a star schema: (1) Fact Table: Sales (quantities, revenue). (2) Dimension Tables: Customer, Product, Time, Region.

### Conclusion
By combining the star schema model with a medallion architecture, you achieve a robust pipeline that ensures data availability, quality, and performance for analytics and reporting.

Here are some best practices that you can try to follow while implementing both of these together :
- Use automation and orchestration tools (e.g., Apache Airflow, Databricks) to manage data movement across layers.
- Clearly define business use cases to design the star schema in the Gold Layer effectively.
- Continuously monitor and validate data quality at each layer to ensure trust in analytics.

## The modern Data Vault stack

For decades data analytics has been delivered in a standard pattern of zones and layers, each with a special purpose and governed by repeatable patterns and standardisation. 
Whether the pattern is deployed as a data warehouse, data lake or data lakehouse. 
The zones and layers will vary but the purposes remain consistent, we need to deliver reliable data business users need in the form they want for data analytics and artificial intelligence.

While the Star Schema is a widely used model, it's not the only one.
Data Vault is a powerful data modeling methodology widely recognized for its efficiency in managing long-term historical data from multiple operational systems.
How does a modern data vault stack (MDVS) measure up? It is not that different from the other architecture patterns, only that a data vault is extremely resilient to business architecture evolution at the enterprise and business unit level. 

Here is the architecture of a data vault : 

![Data Vault architecture](/images/posts/data-vault-stack.png)

- Curated zone (bronze zone) : like deploying crawlers over your operational data products, repeatable patterns of data curation and staging are applied to your data before attempting to integrate that data in the next zone. Two layers are presented in parallel, landing (operational data store- ODS) and inbound shares

- Coherence zone (silver zone) : data integration into repeatable patterns for the three table types passively integrated by the business key, the unique identifier of business objects your organization’s business capabilities is based on. To adopt and integrate more and more connected data you need a data model that is non-destructive to change and flexible enough to support your evolving corporate memory your analytics is historized on. The primary integration occurs in a source-aligned layer (SAL) and you may also choose to utilise a common access layer (CAL) to promote an easily accessible presentation of that integrated data already enriched with sprinklings of reference data

- Intelligence zone (gold layer) : Consumer domains each entrusted to consumer domain owners who may extend the existing shared integration model with their own model extensions in a business access layer (BAL)

## Some other models worth mentionning

- Unified start schema : The USS is an extension of the Kimball star schema that addresses some of its limitations, such as the fan trap and the impossibility to query more than one fact at a time without the risk of a chasm trap.

- Inmon design style : The Inmon design style, created by Bill Inmon, emphasizes building an enterprise-wide, integrated, and interoperable data warehouse as a single source of truth for an organization. Its core idea is to model business concepts (entities and processes) and normalize the data into Third Normal Form (3NF) to minimize redundancy and capture complex relationships.

Advantages
1) resilience: adaptable to changes in business processes or source systems.
2) comprehensive: captures rich concepts and relationships for broad reporting capabilities

Drawbacks
1) complexity: requires highly skilled modelers and significant management commitment
2) auditability: original data is hard to trace due to heavy transformations
3) effort-intensive: maintenance and updates for new reporting needs are resource-heavy

- Data Mesh : Data mesh is a modern data architecture approach aimed at solving data ownership issues by decentralizing data management.
Instead of a centralized data platform, it organizes data into domain-specific platforms, each owned and maintained by business units familiar with the data’s semantics and use cases.
Inspired by microservice architecture and DevOps practices, data mesh emphasizes ‘data products’ — well-defined interfaces, applications, or data marts enabling seamless interaction across domains.

While it aligns business and IT goals, improves data quality, and empowers teams with end-to-end data ownership, it introduces complexity and requires advanced skills in managing distributed systems and APIs. 
Adoption is challenging in less tech-savvy industries, but insights like treating data as products and assigning data ownership to business units can still be applied. 
Tools like dbt facilitate collaboration and documentation in this framework.

- The Pragmatic Data Platform : PDP combines the best aspects of various data modeling styles and architectures to create a modern, efficient data platform while minimizing complexity and expertise requirements.
PDP adopts key principles from Data Vault (DV) and Inmon, such as:
1) separating ingestion from business rules
2) using insert-only ingestion of full history
3) organizing data around business concepts and clear BK definitions

However, it avoids DV modeling due to its complexity, accepting a trade-off of reduced resilience for simplicity. 
Data delivery is typically through data marts with star schemas but can also include wide, denormalized tables for specific use cases like ML/AI.
PDP emphasizes practical solutions, balancing advanced techniques (like using hashes) with accessibility to a broader audience. 
This pragmatic approach leverages the full capabilities of dbt while simplifying implementation.


That's it for today.
Tomorrow I'll finally finish the DP600 certification course.
Also I've came across some good news, the DP700 is offered with the discount so I'll be getting into that shortly after.
