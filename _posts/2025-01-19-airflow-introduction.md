---
layout: post
title: Discovering Apache Airflow
categories: [Data Engineering, Airflow]
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

![Airflow architecture](/images/posts/2025/01/airflow-architecture.png)

- A scheduler, which handles both triggering scheduled workflows, and submitting tasks to the executor to run.
- An executor, which handles running tasks.
- In most production-suitable environments, executors will actually push task execution out to _workers_.
- A webserver, which presents a handy user interface to inspect, trigger and debug the behaviour of DAGs and tasks.
- A folder of DAG files, read by the scheduler and executor
- A metadata database, used by the scheduler, executor and webserver to store the state of each DAG.

### Hands-on
Now that we got the theory out of the way, let's get our hands dirty in dig a little bit deeper into Airflow by creating our first flow.

This can be done locally, but in my case I will do it on the cloud (GCP)

First I will create a Compute Engine instance, using a minimum requirement Debian VM (2 vCPU + 1 GB memory)
On the Network tab, check Allow HTTP and HTTPS and then click Create.
Once the istance is initiated, let's SSH into it, and install Python and update the catalog: 

```Shell
sudo apt update
sudo apt -y upgrade
sudo apt-get install wget 
sudo apt install -y python3-pip
```

Then let's create a virtual environment and activate it :

```Shell
virtualenv -p python venv
source venv/bin/activate
```

We can then install Airflow : 

```Shell
pip install "apache-airflow[celery]==2.5.3" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.5.3/constraints-3.7.txt"
```

Then set-up the project and the Airflow user :

```Shell
mkdir airflow & cd airflow
mkdir dags

airflow db init

airflow users create \
    --username admin\
    --firstname mourad \
    --lastname gh\
    --role Admin \
    --email mouradelghissassi@gmail.com
```

And finally start the scheduler and the webserver.
You might have to whitelist your IP for port 8080 by going to Firewall > Create Firewall Rule

```Shell
airflow scheduler -D
airflow webservice -p 8080 -D
```

Now tha tour Apache Airflow is up and running, let's build our first DAG !
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

Setting up a first flow is pretty easy and straightforward.

### Use cases :
Apache Airflow has supported many companies in reaching their goals, for example :

- Sift used Airflow for defining and organizing Machine Learning pipeline dependencies.
- SeniorLink increased the visibility of their batch processes and decoupled them.
- Experity deployed Airflow as an enterprise scheduling tool.
- Onefootball used Airflow to orchestrate SQL transformations in their data warehouses, and to send daily analytics emails.

### The Good and the Bad :
After this brief overview of Apache Airflow, let’s explore the advantages and disadvantages of incorporating it into your project.

Starting with the Pros :

- Open source : Airflow is supported by a large and active tech community, making it easier to find answers to your issues online.
- Easy to use : Anyone with Python knowledge, which is the fourth most popular programming language worlwide [1], can deploy a workflow. So Airflow is available for a wide range of developers .

Some of the Cons are :

- Code based : while some may consider Airflow’s code-based approach an advantage, it can also be a disadvantage, since Airflow’s learning curve can be challenging and confusing for novice data engineers, compared to no-code alternatives.
- Batch-only : unlike Big Data tools such as Kafka or Spark, Airflow exclusively works with batches and is not designed for data streaming.

### Conclusion

With its extensive list of features and functions, I can see why Apache Airflow is rapidly becoming popular among the best workflow management tool. While there is no one-size-fits-all solution, Airflow has proven to meet the data processing needs of numerous use cases.

If you want to learn more about Airflow, [this Git repository](https://github.com/jghoman/awesome-apache-airflow) has everything you need, it's a curated treasure of blogs, videos and code samples !

