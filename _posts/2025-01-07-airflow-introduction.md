---
layout: post
title: Discovering Apache Airflow
categories: [Data Engineering]
---

If you’ve been keeping an eye on the data engineering market, you might have noticed that Apache Airflow is becoming more and more common in job postings.
Although I haven’t had the opportunity to use it professionally, I decided to explore it and learn more to see what it’s all about.
Apache Airflow is an open source workflow orchestration tool, that allows you to build and run batch data pipelines, by creating tasks and managing depencies between them.

Mandatory history lesson : Airflow was developed by Airbnb in 2014, and was made open source in 2016.

### Advntages :
Before we dive into Airflow, it’s important to mention that Airflow is a code based tool, that represents a workflow as a DAG (Directed Acyclic Graph). Here are the key advantages of expressing data pipelines and their configuration as code :

- Maintainable : Developers can follow explicitly what has been specified, by reading the code.
- Versionable: Code revisions can easily be tracked by a version control system such as Git.
- Collaborative: Teams of developers can easily collaborate on both development and maintenance of the code for the entire workflow.
- Testable: Any revisions can be passed through unit tests to ensure the code still works as intended.

![Airflow advantages](/images/posts/2025/01/airflow-advantages.png)

### Principles :
Now that we’ve seen the advantages of pipelines as code, let’s take a deeper look into Airflow. The principles of Airflow, as described by Airbnb, are the following :

- Dynamic: Airflow pipelines are configurated as Python code, allowing for dynamic pipeline generation. This allows for writting code that instantiate pipelines dynamically.
- Extensible: easily define your own operators, executors and extend the library so that it fits the level of abstraction that suits your environment.
- Elegant: Airflow pipelines are lean and explicit. Parameterizing your scripts is built in the core of Airflow using powerful Jinja templating engine.
- Scalable: Airflow has a modular architecture and uses a message queue to talk to orchestrate an arbitrary number of workers. Airflow is ready to scale to infinity.

![Airflow principles](/images/posts/2025/01/airflow-principles.png)

### Architecture
All of this sounds promosing. Let’s so how it translates in reality, by taking a look at Airflow’s architecture. An Airflow installation generally consists of the following components:

![Airflow architecture](/images/posts/2025/01/airflow-achitecture.png)

- A scheduler, which handles both triggering scheduled workflows, and submitting tasks to the executor to run.
- An executor, which handles running tasks.
- In most production-suitable environments, executors will actually push task execution out to _workers_.
- A webserver, which presents a handy user interface to inspect, trigger and debug the behaviour of DAGs and tasks.
- A folder of DAG files, read by the scheduler and executor
- A metadata database, used by the scheduler, executor and webserver to store the state of each DAG.

### Hands-on
Now that we got the theory out of the way, let's get our hands dirty in dig a little bit deeper into Airflow by creating our first flow.
An Apache Airflow DAG is a Python script which consists of the following logical blocks :

- Python library imports

```python
# import the libraries
from datetime import timedelta
# The DAG object; we'll need this to instantiate a DAG
from airflow import DAG
# Operators; we need this to write tasks!
from airflow.operators.bash import BashOperator
# This makes scheduling easy
import pendulum
```

- DAG argument specification 

```python
# defining DAG arguments
default_args = {
    'owner': 'Mourad Gh',
    'start_date': pendulum.today('UTC'),
    'email': ['mou@rad.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}
```

- DAG definition, or instantiation, 

```python
# defining the DAG
dag = DAG(
    'my-first-dag',
    default_args=default_args,
    description='My first DAG',
    schedule=timedelta(days=1),
)
```

- Individual task definitions, which are the nodes of the DAG, 

```python
# Define an extract task that reads the timestami and visitinfo fields
extract = BashOperator(
    task_id='extract',
    bash_command='cut -f1,4 -d"," /home/project/airflow/dags/web-server-access-log.txt',
    dag=dag,
)
```

- and finally, the task pipeline, which specifies the dependencies between tasks

```python
# Create the task pipeline block
download >> extract >> transform >> load
```

Once your Python script created, all you have to do is copy it to your Airflow's DAG folder (using the UI) and you're good to go !

![Airflow UI](/images/posts/2025/01/airflow-ui.png)


If you want to learn more about Airflow, [this Git repository](https://github.com/jghoman/awesome-apache-airflow) has everything you need, it's a curated treasure of blogs, videos and code samples !

