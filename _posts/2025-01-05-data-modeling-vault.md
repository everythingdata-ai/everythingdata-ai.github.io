---
layout: post
title: Data Modeling + The modern Data Vault Stack
categories: [Data Engineering]
---

Today I'm taking a break from Microsoft Fabric.
On the menu : revisiting data modeling, and some reading about the modern data vault stack.

## Data Modeling 

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

For decades data analytics has been delivered in a standard pattern of zones and layers, each with a special purpose and governed by repeatable patterns and standardisation. Whether the pattern is deployed as a data warehouse, data lake, data lakehouse or a [data fairhouse](https://www.youtube.com/watch?v=YXnoBUQP258) (time will tell if this new term catches on at all). The zones and layers will vary but the purposes remain consistent, we need to deliver reliable data business users need in the form they want for data analytics and artificial intelligence.

How does a **modern data vault stack (MDVS)** measure up? It is not that different from the architecture patterns mentioned above that have been proposed only that a data vault is extremely resilient to business architecture evolution at the enterprise and business unit level. To that end, this article will delve into the zones and layers proposed in the diagram below. You should not consider this diagram as a physical implementation but rather a logical one and use this as a guide for your own data vault delivery patterns.

  

![](https://media.licdn.com/dms/image/v2/D5612AQHLDyYAX-fffQ/article-inline_image-shrink_1000_1488/article-inline_image-shrink_1000_1488/0/1732766009790?e=1742428800&v=beta&t=ODaTZfjEHpRN_T_oW1rGt9uBRO-x1_pYSa3Ttp6AILU)

Data Vault stack with a superimposed flowchart

In brief, data will flow top-down using nomenclature expected of architecture today.

- **Curated zone** (bronze zone) — like deploying crawlers over your operational data products, repeatable patterns of data curation and staging are applied to your data before attempting to integrate that data in the next zone. Two layers are presented in parallel, landing (operational data store- ODS) and inbound shares.
- **Coherence zone** (silver zone) — data integration into repeatable patterns for the three table types passively integrated by the business key, the unique identifier of business objects your organization’s business capabilities is based on. To adopt and integrate more and more connected data you need a data model that is non-destructive to change and flexible enough to support your evolving corporate memory your analytics is historized on. The primary integration occurs in a source-aligned layer (SAL) and you may also choose to utilise a common access layer (CAL) to promote an easily accessible presentation of that integrated data already enriched with sprinklings of reference data.
- **Intelligence zone** (gold layer) — Consumer domains each entrusted to consumer domain owners who may extend the existing shared integration model with their own model extensions in a business access layer (BAL).

These zones should represent the minimum required layers you should consider as a baseline to your implementation of a modern data vault stack.

## Curated Zone (Producer Domain)

Data that is of value is expected to be fetched / pushed to a zone to be processed further downstream by your analytics. Not all data is of equal value and data that is valuable should be curated with the appropriate data quality, categorisation and classification to further process this data safely. How data gets into this zone may be a combination of,

- **Batch data** that arrives as snapshots of source data or applications (this is where the concept of applied date comes from — it’s the date of the state of the source application applied at a point in time), incremental feeds like change data capture streams, pushed or pulled into this zone.
- Near-realtime **streaming data** is still landed somewhere to do analysis on. You need to choose whether committing to streaming is necessary based on your analytical needs because the cost of analytics should not outweigh the cost of getting that data. Stream processing _could_ also be applied while the data is in motion whether certain semantics need to be considered for accuracy, late arriving data and data that arrives out of sequence means this data should be treated like batch data.

o Streaming data is often supplied in a semi-structured form and thus at this juncture you must decide whether you should schematize your data which some data platforms can natively support and can make data consumption cheaper.

- **Shared data** (real time) a concept pioneered by Snowflake where data is not being copied or streamed at all and instead is available in place through a process authenticating one platform domain’s data objects to another in place.

  

![](https://media.licdn.com/dms/image/v2/D5612AQEd51fTg23qqw/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1732766062227?e=1742428800&v=beta&t=vqeEw4pFplhwPnX6FIwl4UoqnRHLOvCkiS7b1BHknfo)

Decisions in a Curated Zone

Under strategic design principles (defined under domain driven design — DDD), the relationship between application domain owners (source-systems) and data engineers as consumers of application data is scoped into three core context mappings as:

- **Customer-supplier pattern** where the application owning team is the supplier and open to taking application change requests and updating data contracts, this is typically seen when the software is developed in-house. Pushing changes to the source-application is ideal (first prize).
- **Anti-corruption layer** may be adopted either when upstream software is legacy or a vendor (as above) but we instead adopt this pattern to minimise downstream impacts from change through pre-staging (second-prize).
- **Conformist pattern** is a pattern when little can be done to change an upstream software application. Either the software application is owned by a vendor who must cater for their own global customer base, or the in-house application is too complex/costly to change. Likely this may require business vault artefacts to manage this technical debt (last prize).

To learn more, see: [bit.ly/3KMPSGS](http://bit.ly/3KMPSGS)

Data from the producer domain is not yet ready to be loaded into an integrated and auditable data store. This is the opportune moment to implement several curation measures before loading data into an auditable data store, in data vault we refer to these treatments as **hard business rules**.

- _“Garbage in, garbage out”_ — grade incoming data by applying data integrity rules and decide that if that data fails a certain threshold whether those records (or the entire batch) will be sent back to an application to be reprocessed and re-fetched?
- Schema evolution must be pre-emptively detected in order to ensure the purpose of the data hasn’t evolved beyond its original intent and if something is broken upstream as dictated by a data contract.
- Identifying and tagging identifying, quai-identifying and sensitive data through automatic data classification that should be periodically run. See [bit.ly/4coN7HR](http://bit.ly/4coN7HR)
- Identifying business key columns and recorded relationships, interactions and transactions between business keys which in turn serves to model the raw vault hub, link and satellite tables these columns will _map_ to.
- Identifying how attribute data should be split according to the above reasons; descriptive data should be mapped to the hub or link satellite table structure condensing records to the appropriate level of **true changes**. This shift left modelling thinking reduces downstream processing costs and you should not include source-system metadata.
- Determine if pre-staging is necessary in order to resolve business key assignment in data or to accomplish vertical and horizontal assignment of business key collision code mappings. As a part of profiling and understanding the underlying data being ingested, relationships between business keys must be analysed to understand the underlying business process as the source application has automated it.
- Staging is applied to add record-level metadata defined as SQL views over the underlying table. Execution of hash functions to populate surrogate hash keys and record digests (HashDiffs) are only executed once during processing the data loads to the target raw vault hub, link and satellite tables. For guidance on what metadata columns to expect see [bit.ly/3GMnT8O](http://bit.ly/3GMnT8O)

Data classified and tagged at this level is curated and ready to be loaded into the next layer (raw vault) and once loaded what are we to do with the content in the curated zone? My suggestion here is data in this zone must enter a retention period of 30 days as dictated by your contractual, regulatory and legal obligations. Data older than 30 days is removed and the reason for this is that this process should limit your exposure to data breaches and potentially if you have identifying and sensitive data loaded into this zone. How this is managed by your inbound share data is something you need to negotiate with data share providers.

The usual disclaimer:

  

![](https://media.licdn.com/dms/image/v2/D5612AQGxuLitXqyScA/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1732766114443?e=1742428800&v=beta&t=Wy-J3bmv2-n6E5QevV_395-xFhiv8ypCHEAMIX_kMJk)

### Inbound Data, layer: Landing

LIB_${ENV}_ODS.${src-sys}(_${src-db}_${src-sch})

- ENV — environments = {DEV, SIT, UAT, PRD}
- src-sys — YRD (Yardi), SAP, SIE (Siebel), MDM (Master Data Management)…
- src-db — Finance database
- src-sch — Transaction schema

### Inbound Data, layer: Inbound Share

LIB_${ENV}_SHR${##}.${src-sys}(_${src-db}_${src-sch})

- ENV — environments = {DEV, SIT, UAT, PRD}
- src-sys — YRD (Yardi), SAP, SIE (Siebel), MDM (Master Data Management)…
- src-db — Finance database
- src-sch — Transaction schema

## Coherent Zone (Aggregation Domain)

  

![](https://media.licdn.com/dms/image/v2/D5612AQHZKqqLVH2ufg/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1732766203480?e=1742428800&v=beta&t=s3mlht2LbvinKtQ1JP2KoouxOxy2SSdVrJV_uP1paVk)

Decisions in the Coherence Zone

Within SAL we model and **integrate** raw data into the three things every business care about their data,

- **Business objects**, how to uniquely identify them (business key) and how to describe them (business glossary terms). Modelled as raw vault **hub** tables promoting passive integration across source applications.
- What other business objects or entities do your business objects have a recorded **relationship**, **interaction** or **transaction** with. Modelled as raw vault **link** tables.
- What the state information describing those business objects and interactions recorded as either hub or link **satellite** tables as true changes.

Within SAL you will also find the following extensions to passive integration:

- Business vault **link** tables modelled to represent the business process as the business sees it which may differ to how the source application has depicted it, see an example of how this can happen here [bit.ly/3DJrRhO](http://bit.ly/3DJrRhO)
- Business vault **satellite** tables recording changed the descriptive outcome of soft business rules applied within the analytics platform. Where lightweight soft rules are applied and little or no business rule change will occur, apply these business vault satellites as views.

In both bullets the data is persisted in an auditable form (just like raw vault link and satellite tables) and carry the same persisted metadata columns we described earlier. Because business vault artefacts are **sparsely modelled** we simply extend the raw vault schema with these table types. Use whatever transformation language you prefer in your business logic, but the outcome must follow these principles to ensure the outcomes support the same scalability as the rest of the platform:

- Business logic is **domain owned**, **defined once**, and shared across domains and made discoverable by users through declaring it Public.
- Business logic must be **idempotent**. If business logic is executed multiple times on the same data with the same parameters, it must produce the same result. Idempotence of business logic will aid in managing business logic change, rebuilding, or refreshing datasets, resolving potential orchestration failures or issues, etc.
- Business logic must be **versioned** to manage the evolution of business logic, and track and audit changes over time. Versioning will support reconstruction and replay ability of business logic outcomes. Every business vault artefact will record the version change in the record-source metadata column.
- Business **logic is iterative** and **will evolve**.
- Business logic will act **autonomously** with dependencies between business logic only introduced when required to do so. Aggregations on Aggregations should be avoided to reduce complexity and reduce change management and correction effort. Similarly changes to a discrete piece of business logic should not impact downstream business processes that utilise the logic unless it is intended to do so.
- **Maximise the reuse** of Business logic; the closer logic is to source, the greater its reuse. Logic needs to be abstracted out of pre-aggregated tables.
- **Maximise work not done** through preparing data for business logic by embracing shift-left modelling.
- Promote agile information delivery through **logic virtualisation** in the information mart layer.

Other peripheral satellite tables extend the data vault even further recording metadata necessary to understand your corporate memory even further as:

- Record tracking satellite table (RTS) — tracking the occurrence of every record.
- Status tracking satellite table (STS) — recording status changes to a snapshot business key or relationship.
- Effectivity satellite table (EFS) — tracking changes between driver key and non-driver key components of a relationship. See more here, [bit.ly/47BEBVn](http://bit.ly/47BEBVn).
- Extended record tracking satellite table (XTS) — a special purpose extension of RTS that is used to dynamically correct out of sequence loads and/or can be used the state of a record whether it is expired, archived and if it reappears in the case of GDPR article 17. See more here, [bit.ly/4dWwzry](http://bit.ly/4dWwzry)

As an extension within the coherence zone, you will also find the following:

- Reference data centrally managed in the coherence zone in its own reference schema
- Query assistance tables (the famous PITs & Bridges) and information marts built into a common access layer (CAL) combining SAL and reference content. Data vault often requires SQL-savvy engineers / analysts to bring the related data together and this can be done on their behalf and exposed in this layer for exploration. Learn more on how to implement PIT tables here, [bit.ly/48LtppT](http://bit.ly/48LtppT) and Bridge tables here, [bit.ly/3MFnXZr](http://bit.ly/3MFnXZr)

### Integrating data, layer: LIB_${ENV}_EDW.SAL

- ENV — environments = {DEV, SIT, UAT, PRD}
- hub_${business-object}
- lnk_${relationship}
- sat_${src-badge}_${src-filename} for raw vault

Raw vault satellite attributes are not changed from what you get from the source application. Hub business key column name should be conformed and the data type _always_ cast to string (no exceptions), see [bit.ly/3iEiHZB](http://bit.ly/3iEiHZB).

- lnk_bv_${relationship} for business vault
- sat_bv_${src-badge}_${src-filename} for business vault

Business vault also implies applying modelling naming standards to your attributes like.

  

![](https://media.licdn.com/dms/image/v2/D5612AQFezKriAfG0CA/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1732766257069?e=1742428800&v=beta&t=1SpzelriLcP-98-U9PHwRy31r8JCMbaovNnxC8jpigk)

Find guidance for your data vault naming your standards here: [bit.ly/3GMnT8O](http://bit.ly/3GMnT8O)

### Integrating data, layer: LIB_${ENV}_EDW.CAL

- ENV — environments = {DEV, SIT, UAT, PRD}
- PIT_${business-object|relationship}_${cadence}
- BRDG_${business-object|relationship}_${cadence}
- Information mart / Kimball modelling standards

Find guidance for your data vault naming your standards here: [bit.ly/3GMnT8O](http://bit.ly/3GMnT8O)

## Intelligence Zone (Consumer Domain)

Domain-centric information delivery sets a repeatable pattern for the expected layers within the intelligence zone.

  

![](https://media.licdn.com/dms/image/v2/D5612AQHIyOTK7lDb0Q/article-inline_image-shrink_1000_1488/article-inline_image-shrink_1000_1488/0/1732766317248?e=1742428800&v=beta&t=JskN-ofWtG4RjRvkYTdQxtLYsiW3N8FPpZxO3LszxjQ)

Decisions in the Intelligence Zone

Let’s debrief you on the layers we have now added,

- Business access layer (BAL) extends the model from SAL and CAL with data vault patterns for business vault, query assistance and information delivery. Because this extension is within a consumer’s domain it is considered a private business vault applying soft rules in the language of your choosing but should still follow the business rule principles we described in the previous section.
- Business presentation layer (BPL) is data ready for outbound sharing should the consumer domain choose to.
- Business reporting layer (BRL) contains information marts in the form utilised by various business intelligence reporting tools and dashboards.
- Reference data, a layer with a consumer domain’s own enrichment data available for BPL and BRL.
- Lab area for running data science experiments and developing private business rules and designing features.
- Subdomains, schemas a consumer domain is free to create and mould to support any engagement data products.

### Information Deliver: LIB_${ENV}_${BU}.${BL}

- ENV — environments = {DEV, SIT, UAT, PRD}
- BU — Business Unit = {FIN-finance, INS-insurance, MKT-marketing}
- BL — Business Layer = {BAL, BPL, BRL, LAB, REF, DP-data products}

## Knowledge Zone (Metadata Domain)

Just as crucial as the data itself is the data about the data, here we will differentiate between passive, active and proactive metadata and highlight the automation patterns that complement your MDVS.

  

![](https://media.licdn.com/dms/image/v2/D5612AQHxa2lE09dYRg/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1732766410789?e=1742428800&v=beta&t=In2FocVN1-eM-HaRqdYS4q9k4whr8Q3Jtp-5iSIIbu4)

### Passive

Static elements that are classified as passive metadata include data dictionaries, schema information and system documentation, _static elements._ By extension ownership of data products is stipulated as well as the semantics on how to use them.

### Active

Measure [everything](https://medium.com/the-modern-scientist/what-is-the-shape-of-your-data-0a146c6eb145), at a minimum a framework is utilised to capture from run times, record counts to tracking non-identifying aggregates for anomaly detection. By utilising a simple count for business keys, relationships and state data changes we can already infer business metrics around customer growth, interactions and state changes and seasonality, _dynamic elements_. Active metadata is essentially about what is happening.

In the data governance frame a useful metric is recording what data objects were accessed and if other data objects were created from those objects down to the column level. Data provenance here will help development efforts to analyse upstream and downstream change impacts.

### Proactive

The first element of proactive metadata are the efforts designed and executed before passive and active metadata is even recorded. In this context we are discussing data governance and security to protect your data before it is even accessed.

To accomplish this a robust proactive metadata management is a framework of:

- Role-based access control (RBAC) based on a Policy of least privilege (PoLP).
- Encryption of data in motion and at rest
- Column access control (masking, tokenisation) and row access control defined as policies aided by object tagging and data auto-classification.
- Designing data quality alerts and thresholds ensuring compliance and cost monitoring. _Data privacy_.
- _And other design time artefacts…_

### Business view

A conceptual data model (CDM) is a high-level abstraction of an organisation’s information landscape represented with business concepts and their relationships. A semantic layer maps analytical data into business-friendly constructs abstracting away the technicalities of the physical data model underneath. A CDM is a planning artefact for data modelling, whereas a semantic layer improves accessibility of diverse data underneath. Data products and the data contract that it comes with provides input into the semantic layer. Within the semantic layer the complex relationships between business objects can be further analysed with a **knowledge graph** (a structured representation of information that captures relationships between business objects). A knowledge graph contains the following key components:

- **Nodes** represent business objects
- **Edges** represent relationships
- **Attributes** represent the node’s state
- Ontology defines the **schema**, node types and constraints of the graph model

> _It’s not a huge leap from data vault to knowledge graph…_

  

![](https://media.licdn.com/dms/image/v2/D5612AQFVMp0KqvQajQ/article-inline_image-shrink_1000_1488/article-inline_image-shrink_1000_1488/0/1733692599623?e=1742428800&v=beta&t=rwcK1qB8K5zeEl2G0zczfGboZDUhqvWb8SibeJA2hcg)

Although depicted on the far-right, the semantic layer may be connected to SAL, CAL

### Semantic model

Semantic layer is also home to the **semantic model**, a single, cohesive business view translating complex technical data structures into familiar business terms. In here, data vault is simplified into:

- **Entities** = business objects such as customer, account, product or transaction
- **Dimensions** = descriptive attributes contextualising entities that a business user may use to _slice and dice_ entity data.
- **Time** **dimensions** = dimensions holding business dates and times.
- **Metrics** or measures = additive and semi-additive facts to aggregate your entities by. With slicing and dicing, metrics are expected to be correct.
- **Hierarchies** = define drill paths through parent child relationships between entities; metrics and measures should aggregate according to the drill-path depth even if a relationship between entities fans-out.

To ensure the semantic model is not merely a semantic layer band-aid, these principles are suggested:

- The semantic model should do as _little_ modelling as possible.

Data modelling is solved in raw and business vault resolving conflicts, deviations and complexities before they are ready for presentation. Semantic models should be restricted to _functional_ business rule implementation and nothing more.

- Technical debt and model complexity should be pushed as far left as possible.

Data from application source domain is _not_ in the form consumable by the casual business user. Nuances of application source models are ingested and resolved in the enterprise data warehouse or if possible, in the source application.

- There should be no _layering_ in a semantic layer (stacking).

Layering within a semantic layer begins to push complexity to the right introducing unvetted dependencies which will ensure the semantic model is not easy to change.

- There should be only _one_ semantic layer.
- The semantic model should reflect the business ontology and business terms.
- Semantic model is the end state of harmonised information, no technical debt is _resolved_ in the semantic model.

Technical debt is a tax that accumulates with penalties if it is not resolved the longer it is not paid. Tech debt is resolved as far (left) upstream as possible.

- Semantic model has no audit trail, calculations that need historization should be pushed left. The semantic model is ephemeral.

Only very lightweight calculations are permitted in the semantic model, complex calculations must be resolved upstream.

- Semantic model must be vetted by business to ensure it accurately reflects business knowledge.

Ultimately the accountability for business results is with the business users; therefore, an exercise to resolve ambiguities is to ensure that the data accurately reflects how the business functions.

- Semantic model should be accessible, easily searchable and secure at the same time. Above all, it must be accurate.

A fully integrated and enriched semantic model reflects upstream due diligence. On top of this sanitised data, data is searchable by business terms as long as the business user is authenticated to access it.

- Semantic model should provide insights without delay.

The default deployment of a semantic model is as views cached into memory for immediate accessibility.

- Semantic model does not apply business rules.

Business rules _may_ involve complex business logic which are automated through software applications. Business rules that have no purpose in software applications or are not feasible to be deployed there, are instead resolved in the data warehouse deployed as transformation rules.

- No hardcoded values are implemented in the semantic model

Implemented as data-driven business rules, data-driven changes to a semantic model are driven by reference data

  

![](https://media.licdn.com/dms/image/v2/D5612AQE2Cnh2aqUozA/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1733711387249?e=1742428800&v=beta&t=_L-LiseUvA-IBnvRwoLBj1LmcOKGjWLlPDNq-GE67MY)

Semantic model within the Semantic Layer

## Tooling

Automation is the purpose of tooling, none of the MDVS is manually executed and all is defined as metadata driven automation. Only the coding of business rules may require customised code written in the language of your choice, the outcome of those business rules (following the business rule principles we defined above) are loaded just like another data source as business vault link and satellite tables and therefore inherit the same auditability as raw vault. This only works in a bi-temporal data vault.

### Moving data

Batch and streaming follow a data contract owned by consumers of that data and honoured by producers. Common tools in the space include Fivetran, Stitch, Matillion, Talend, Airbyte, Informatica to name a few. ‘Moving data’ tools (commonly called extract-transform-load — ETL or ELT) connect and transform polyglot data into something useful as data dumps. Some tools provide orchestration of templated approaches to loading and transforming data, some prefer interoperability to orchestration and scheduling tools like Control-M, Airflow or Prefect (again not an exhaustive list). As for streaming ingestion the following tools provide message queues and append-only logs, Kafka, RabbitMQ, AWS Kinesis, Azure Event Hubs, Google Pub/Sub to name a few.

Because we are building a data vault, you must consider a data vault automation tool if you are not building an automation engine of your [own](https://patrickcuba.medium.com/decided-to-build-your-own-data-vault-automation-tool-a9a6273b9f9b). The tools included in this space (in no preferential order) are the likes of Wherescape, Vaultspeed, AutomateDV (a dbt package), dFakto and Coalesce to name a few.

### Monitoring Data

A hot topic in the last few years is data observability, the crux of this movement is presenting such data in an easily consumable format to make rapid decisions which presents its own analytics based on active metadata and is the basis of DataOps. Common tools you will see here are Monte Carlo and [DataOps.live](http://dataops.live/) however many ‘moving data’ tools offer overlapping capabilities with these tools too. For a data vault you should also parameterise a reconciliation [framework](https://github.com/PatrickCuba/the_data_must_flow/tree/master/data-auto-testing).

### Visualising Data

It’s not simply bar and pie charts we use in dashboards when it comes to data visualisation, although the metrics in this regard are certainly needed for regulatory compliance and measuring enterprise health. Metadata is important for regulatory compliance and model explainability too, this is in the form of data provenance tools which can be natively supported by the ‘moving data’ tools itself. Some tools specialise in this area too and function beyond a single platform to offer lineage from micro-service to machine learning model while enriching each business object, relationship and state data with business context. Tools that fall into this category include Acryl DataHub, Collibra, Apache Atlas, Amundsen and Alation.

The dominant analytical data models each have a focal point to their implementation,

- Inmon 3rd normal form data models use constraints to ensure compliance to industry-focussed business rules, a target-oriented model.
- Data vault models focus on the business objects, its relationships to other business objects and the state information of each. An integration-oriented model designed to be flexible to change.
- Dimensional data models focus on the business case based on metrics and the applicable state of business object data.

Using an appropriate and interoperable tool is essential to visualising this data, tools in this area include SQLDBM, ErWin by Quest, Ellie and many more. Some of the data vault automation tools include a visualisation of the data vault model too.

### Securing Data

Customer data does not belong to an enterprise, they belong to the customer, the curation and management of customer data does indeed belong to the enterprise. The management of customer data must be secure first and centred on trust and the tools around the stack support searchability through a catalogue.

### Artificial Intelligence

You cannot support your artificial intelligence efforts without implementing a sound and trustworthy data strategy. Artificial intelligence needs historical data with the care applied to turn that raw data into feature tables (business vault satellite tables for [this](https://patrickcuba.medium.com/data-vault-on-snowflake-feature-engineering-business-vault-244983e27503) offers the auditable data discipline for model explainability). Managing and orchestrating feature engineering in a feature store framework is supported by some of the dominant data platforms like Databricks and Snowflake as well as being embedded into tools like H2O and AWS Sagemaker (again, to name a few).

All tooling whether it deals with data, software (code) or security follows the respective philosophies to automate change in continuous delivery, namely DevOps, DataOps, DevSecOps, MLOps… etc.

## Why Stack?

A systematic approach with intent is a disciplined approach to designing and building an analytics platform that scales. Most analytics follow prescribed reusable patterns as a template and therefore to effectively execute your intent is to apply small levers to move vast amounts of data; that requires that the enterprise master its metadata. Regardless of if there is a data vault in the middle or not, managing data is multifaceted, you must leverage your data effectively and ensure there is zero risk to managing that data. The stack is a proven pattern for delivering data analytics at scale; bring your experience, expertise and opinions to the modern data vault stack.

> _“There is no_ **_AI_** _Strategy without a_ **_Data_** _Strategy.” — —_ **_Sridhar Ramaswamy_** _Snowflake CEO_

Innovation in analytics is driven by reducing ‘steps’ (layers) or executing analytics in a more efficient way. The major reason why there is data movement in the first place is the prioritisation of utilizing “scare resources with alternative uses” (quote from Thomas Sowell), let’s elaborate on this a little:

- We cannot run analytics on application software infrastructure (source-system) because competing with the basic computer components of CPU, memory and storage will impact that software’s performance, the customer’s experience and the enterprise’s reputation.
- We cannot impose data analytics’ needs on operational software because the application’s data model is optimised to serve the software application and their customers. In data analytics we can safely transform the polyglot data we need into the form we want as templated hub, link and satellite tables.
- We cannot dictate the source application’s data store because the data storage is optimised for per row operations and not for analysing columnar data across history.
- Software professionals driven to design, build and support business software are incentivised to support their real-time customer needs. A machine learning model is deployed as code and available to make real-time predictions within the business software itself, however the model’s training is based on historical data from the analytics platform. The motivation for supporting OLTP and OLAP workloads will always differ and OLTP automates business processes based on current data, not historical data. A data vault’s goal is to take the numerous business processes automated in software applications and deliver the process outcomes historized as hub, link and satellite tables (business objects, interactions and state information respectively).
- We cannot enforce our business architecture onto software applications automating business processes of the business. Vendor or customised business software meets an enterprise’s needs and often not completely. Some software applications work on surrogate keys only, others serve a global audience of their own and therefore implement the desirable business process how they see it. The latter is likely inflexible to change or take customer feedback unless their customer (you) is a high-paying or strategic customer of theirs. The source model will differ to the business model however what the software automation and analytics share is the focus on business objects. The aggregate domain must therefore be flexible to change with minimal or no impact to the other bounded contexts it is designed to serve.
- We cannot impose the requirements of an enterprise’s corporate memory onto software applications however software applications should leverage an enterprise’s corporate memory. Corporate memory drives enterprise decisions and therefore you should expect software applications to evolve or be replaced more often than an enterprise’s analytics hub, the analytics hub evolves as the software application changes.

To build a robust data-driven organization, solve as much of your technical debt as far left as possible to ensure data problems are solved as close to the source. This ‘small lever’ (shift-left model) is the most efficient method for designing, building and deploying reliable and safe engagement data products downstream.

## References

- Rules for an almost unbreakable Data Vault, [bit.ly/4djNchE](http://bit.ly/4djNchE)
- More Rules for an _(almost)_ unbreakable Data Vault, [bit.ly/3y4jeyD](http://bit.ly/3y4jeyD)
- The OBT Fallacy & Popcorn Analytics, [bit.ly/3Pfhbgz](http://bit.ly/3Pfhbgz)
- A Confluence of Metadata, [https://vaultspeed.com/resources/ebooks/a-confluence-of-metadata](https://vaultspeed.com/resources/ebooks/a-confluence-of-metadata)
