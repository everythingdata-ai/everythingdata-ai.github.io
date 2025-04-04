---
layout: post
title: Tout of Apache Beam
categories: [Data Engineering, Apache Beam]
---

I have been seeing GCP's Dataflow everywhere lately, but I haven't mustered the courage to check it out until now.
Dataflow is runner for Apache Beam, which is an open-source tool for data processing.

![image](https://github.com/user-attachments/assets/83fafd1f-477d-480d-b01e-f8e590095ad9)

Beam is adapted for both batch and streaming, and supports multiple prorgramming languages : Java, Python Go, Typescript...

![image](https://github.com/user-attachments/assets/53d41846-9ba7-4b98-a020-dedc4111b052)

This post will contain my notes from the Tout of Beam hands-on course that you can follow [here](https://tour.beam.apache.org/).

### Beam Concepts

#### Runners

Apache Beam provides a portable API layer for building parallel data processing pipelines.
These pipelines can be executed across a diversity of execution engines, called runners.

There is a variety of runners available, he are the main ones :

- Direct Runner : executes pipelines on your machine, to make sure that the pipelines are valid and robust.
It makes testing/debugging and validating pielines easier, before deploying them on a remoste cluster.
It's optimized for correctness rather than performance, so it shouldn't be used in production environments.
Here is a run example :

```python
python -m apache_beam.examples.wordcount --input YOUR_INPUT_FILE --output counts
```

- Google Cloud Dataflow Runner : this runner uploads your executable code and dependencies to a Google Cloud Storage bucket and creates a Cloud Dataflow job, which executes your pipeline on managed resources in Google Cloud Platform. 
Dataflow is a fully managed GCP servic, allowing you to autoscale the number of workers depending on the job.
Here is a run example :

```python
python -m apache_beam.examples.wordcount --input gs://dataflow-samples/shakespeare/kinglear.txt \
                                         --output gs://YOUR_GCS_BUCKET/counts \
                                         --runner DataflowRunner \
                                         --project YOUR_GCP_PROJECT \
                                         --region YOUR_GCP_REGION \
                                         --temp_location gs://YOUR_GCS_BUCKET/tmp/
```

There is also an Apache Spark Runner and an Apache Flink runner,

#### Pipeline

The first thing you need to use Beam is a driver program, which defines your pipeline : all the inputs, transforms and outputs.
It also sets the execution option for your pipeline, including the runner.

To simplify large-scale distributed data processing, Beam provides several abstractions that include : 

- Pipeline : encapsulates your entire data processing task, from start to finish (reading intput data, transforming that data, and writing output data)

- PCollection : a distributed data set that the pipeline operates on. It can be a fixed source like a file, or unbounded from a continuously updating souce.
PCollections are the inputs and outputs for each step in your pipeline.

- PTransform : represents a data processing operation/step in the pipeline. Every PTransform takes one or more PCollection objects as input, performs a processing function on them, and then produces zero or more output PCollection objects.

- I/O transforms : library PTransforms that read or write data to various external storage systems ()

A typical Beam driver program works as follow : 
Create a pipeline objects and set the execution options, including the Pipeline runner.
-> Create an initial PCOllection.
-> Apply PTransforms to each PCollection.
-> Use IOs to write the final PCollection to an exeternal source.
-> run the pipeline using the designated Pieline Runner.

Here is a example : 

```python
import apache_beam as beam

with beam.Pipeline() as p:
  pass  # build your pipeline here

beam_options = PipelineOptions(
    runner='DataflowRunner',
    project='my-project-id',
    job_name='unique-job-name',
    temp_location='gs://my-bucket/temp',
)

pipeline = beam.Pipeline(options=beam_options)
```











