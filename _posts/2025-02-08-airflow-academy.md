---
layout: post
title: Astronomer Airflow Academy
categories: [Data Engineering, Airflow]
---

These are my notes from the Airflow Academy by Astronomer.
This course is a good preparatoin for the Apache Airflow fundamentals certification.
You can check out the free course [here](https://academy.astronomer.io/path/airflow-101)

### Introduction to Orchestration and Airflow

Why orchestration ?
Let's say you're a Data Engineer who wants to ingest data from databases and 3rd party sources & APIs into a data warehouse.
Then you have a data analyst who wants to access the data warehouse to transfor the data through dbt and make dashboards.
Also there a data scientist team that are building models on top of the data warehouse, and machine learning team industrializing those models.

With data orchestration, you are able to build data pipelines that coordinate and automate data flow across various tools and systems.

What is Airflow ?
Airflow is a modern data open-source orchestration tool, for programatically authoring, scheduling and monitoring your data pipelines.

Airflow is defined by :
- the rise of pipelines-as-code in Python
- the ability to intergrate with hundreds of external systems
- time and event-based scheduling
- feature-rich software

Airflow has become the de-facto choise for orchestration.

### Basics

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
