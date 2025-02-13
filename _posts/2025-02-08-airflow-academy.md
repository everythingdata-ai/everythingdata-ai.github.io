---
layout: post
title: Astronomer Airflow Academy
categories: [Data Engineering, Airflow]
---

These are my notes from the Airflow Academy by Astronomer.
You can check out the free course [here](https://academy.astronomer.io/path/airflow-101)
This course is a good preparatoin for the Apache Airflow fundamentals certification.
This certification covers the basics : your understanding of Airflow’s architecture, core components, and how to use it effectively for building and monitoring workflows.

### Introduction to Orchestration and Airflow

#### Why orchestration ?
Let's say you're a Data Engineer who wants to ingest data from databases and 3rd party sources & APIs into a data warehouse.
Then you have a data analyst who wants to access the data warehouse to transfor the data through dbt and make dashboards.
Also there a data scientist team that are building models on top of the data warehouse, and machine learning team industrializing those models.

With data orchestration, you are able to build data pipelines that coordinate and automate data flow across various tools and systems.

#### What is Airflow ?
Airflow is a modern data open-source orchestration tool, for programatically authoring, scheduling and monitoring your data pipelines.

Airflow is defined by :
- the rise of pipelines-as-code in Python
- the ability to intergrate with hundreds of external systems
- time and event-based scheduling
- feature-rich software

Airflow has become the de-facto choise for orchestration.

#### Who uses Airflow ?
- Data scientists : pre-process and analyze new datasets
- Data engineers : creating new datasets and managing data pipelines
- ML engineers : retraining models automatically
- Data Analyst : automatically refresh reports after every incremental load

#### Use cases
There are 4 main use cases of Airflow :
- Data-powered applications  
- Critical operational processes
- Analytics & reporting
- MLOps & AI

Aiflow is designed for Batch processing, not streaming processing.
However, you can still mix Airflow with Kafka.

By default, Airflow is scalable.
Consider the resources and infrastructure required as the complexity increases.

Airflow can process data, but it's designed mainly for orchestrating and managing data workflows.
Make sure you have enough resources if you're using it for process.

#### How does Airflow work ?

The first key-term is DAG : Directed Acyclic Graph, a.k.a a single data pipeline
Directed : DAG has an order
Acyclic : DAG has no cycles

The second key-term is Task : a single unit of work in a DAG

Third key-term to remember is Operator : defines what the task does (for example execute a Python function)
there are 3 main categories ofoperators :
- Action operators : execute python, execute a sql query...
- Transfer operators : transfer data from S3 to Snowflake...
- Sensor operators : wait for an event like uploading a file

### Basics

#### Airflow core components
- Web server : enable user to monitor task execution
- Metadata database : a db containing task instances, DAG status, users...
- Scheduler : determins which task to run and when
- Executor : part of the scheduler, determins how and on which system to execute your tasks
- Queu : part of the executor, manages the order of execution
- Worker : takes the tasks out of the queue to execute them

#### How does Airflow run a DAG ?
1. The Airflow scheduler detects a new DAG in the DAGs directory
2. The Airflow scheduler serializes the the DAG and stores it in the metadata database
3. The Airflow scheduler scans the metadata database to see if any DAGs are ready to run
4. The scheduler sends the DAG's tasks into the executors queue
5. The task is executed when a worker is available.

#### Key components of a DAG file
A DAG file can be separated into 3 parts :

- Import statements

```python
from airflow import dag
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator
```

- sqdds

```python
with DAG(
  ...
):
```

- qdsqds

```python
  first_task = PythonOperator(
    ...
  )
  second_task = BashOperator(
    ...
  )
```

### Local Development Environment

### UI

### DAGs 101

### DAG scheduling

### Connections 101

### XComs 101

### Variables 101

### Debug DAGs

### Sensors

### Command Line Interface

### 
