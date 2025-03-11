---
layout: post
title: The future is SQL
categories: [Data Engineering, SQL]
---

Last week I attended a talk titled 'The Future is SQL'.

When I just started working as a Data Engineer, I remember feeling ashamed that the only programming language I used was SQL.
But now, SQL can handle the whole data pipeline and is's all you need for a modern data stack !

Here are some tools that brought back SQL from the brink of death and made it what it is today :

### Cloud Data Warehouses 

![image](https://github.com/user-attachments/assets/efc54377-3ac5-4c4e-b0ee-1084c3ae8b50)

The rise of cloud data warehouses like Snowflake and BigQuery first highlighted SQL's potential beyond traditional databases. 
These platforms demonstrated that SQL could handle petabyte-scale analytics while remaining accessible to analysts who weren't versed in complex programming languages.

### DuckDB

![image](https://github.com/user-attachments/assets/63d4672b-37c8-4ccc-9939-9cc66a12c085)

Perhaps no tool better exemplifies SQL's evolution than [DuckDB](https://duckdb.org/). This embeddable analytical database brings the power of modern columnar databases to local workflows. 
Data scientists can now process gigabytes of data on their laptops using familiar SQL syntax, making it a compelling alternative to pandas for data analysis. 
DuckDB's ability to query Parquet files and integrate with Python has made it a staple in modern data workflows.

### dbt 

![image](https://github.com/user-attachments/assets/d12416ac-37eb-4a75-8760-1a486d171313)

[Data Build Tool (dbt)](https://www.getdbt.com/) revolutionized how we think about data transformation. 
By bringing software engineering practices like version control, testing, and modularity to SQL, dbt elevated SQL from a query language to a full-fledged development environment. 
Analysts can now build complex data models using SQL, with built-in documentation, lineage tracking, and testing capabilities.

### Evidence.dev

![image](https://github.com/user-attachments/assets/8e9cbf68-7b83-4c68-8c79-b4de7c134ddb)

[Evidence.dev](https://evidence.dev/) represents the next frontier in SQL's evolution - using it as a full-stack development language for data applications. 
By writing SQL queries alongside markdown, analysts can create interactive dashboards and data apps without learning a front-end framework. 
This demonstrates SQL's potential as not just a query language, but a complete solution for building data products.

### Materialize 

![image](https://github.com/user-attachments/assets/e3da2c00-08a5-4d2b-9557-50a984990bfb)

[Materialize](https://materialize.com/) brings real-time streaming capabilities to SQL.
It accepts updates from various sources, including OLTP systems, Kafka, and webhooks, enabling end-to-end latency that is measured in seconds rather than hours.
You can pull results from Materialize using Postgres-compatible SQL, which can be issued from a service, a native web-client, or even a standard BI tool. 
You can also push updates in real-time to downstream systems like Kafka or a data warehouse.

### SQLMesh

![image](https://github.com/user-attachments/assets/6c77c59b-7ef0-41b5-a6c9-bb75852757bf)

SQLMesh is an open-source data transformation framework that brings software engineering best practices to SQL workflows, differentiating itself from tools like dbt through its automatic dependency inference, isolated development environments, and native versioning system. Unlike dbt's manual configuration approach, SQLMesh automatically tracks changes between models by parsing SQL queries, determines what needs to be recomputed when changes occur, and handles incremental models without explicit configuration. Its time travel capabilities enable point-in-time testing against historical data, while its environment-based workflow allows developers to test changes in isolation before promotion to production. Though newer and with a smaller community compared to dbt, SQLMesh offers a more integrated approach to the end-to-end development lifecycle, potentially reducing long-term maintenance overhead despite a steeper initial learning curve.


### BigFunctions

![image](https://github.com/user-attachments/assets/ea6adf15-304f-42b0-9f73-fdaf63252270)

And last but not least, the topic of the "The future is SQL" lecture : [BigFunctions](https://unytics.io/bigfunctions/) is a tool that helps you supercharge BigQuery with powerful functions.
From refreshing your Power BI to report to sending messages on Slack, BigFunctions brings over 150 functionalities directly to your BigQuery without installation.
"BigQuery, BigFunctions and dbt is all you need"

### The SQL-data stack

All these tools are helping the evolution from the modern-data-stack to the SQL-data-stack 

![image](https://github.com/user-attachments/assets/e61cdf15-3196-468a-8995-25a0fa30d7e9)

If you keep up with technical news, you might have came across articles on how Python is the most popular programming language (for a few years in a row now)

![image](https://github.com/user-attachments/assets/b57db124-fab1-43fa-9034-d0c305e7417c)

While that's partially true, it hides the real truth : SQL is the most popular language in job postings, ahead of Python the close second.  

![image](https://github.com/user-attachments/assets/8f518239-375f-43ed-83f7-559c23a998a5)

(Source : [IEEE Spectrum](https://spectrum.ieee.org/top-programming-languages-2024)

As data continues to grow in importance, SQL's role will only expand. 
The language's simplicity, combined with modern tools that enhance its capabilities, has created a perfect storm for SQL to become the primary language for data work. 

From local analysis to production data pipelines, SQL has evolved from a simple query language to the backbone of the modern data stack, and it doesn't seem to be slowing down.
