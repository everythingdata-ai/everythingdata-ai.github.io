---
layout: post
title: Apache Spark Training
categories: [Data Engineering, Spark]
---

It seems that every single Data Engineering job requires an experience with Spark, unfortunately I have never had the chance to work with this technology in my previous experiences.
So I decided to see what it's about, and what's better than to learn about it from someone who helped bring it to life, and who happens to be very good at explaning complicated topics in a simple way : [Sameer Farooqui](https://www.linkedin.com/in/blueplastic/).

If you're curious about Spark, I highly recommend [this 6-hour masterpiece !](https://www.youtube.com/watch?v=7ooZ4S7Ay6Y)

### Introduction 

The motivation behind Spark is to have a unified engine to not have to learn multiple engines, just learn Spark Core and slowly learn the outer layers.

Spark is 10 to 100 times faster than MapReduce.

Spark works through a shell (Scala & Python only)
That's the driver program, that will send work to worker machines

### RDD fundamentals

An RDD (Resilient Distributed Dataset) can be created in 2 ways :
- Parallelize a collection
- Read data from an external source (S3, C*, HDFS...)

Parallelize in Python :
```python
wordsRDD = sc.parallelize(["fish", "cats", "dogs"])
```

Read a local txt file in Python
```python
linesRDD = sc.textFile("/path/to/README.md")
```

#### The Spark program lifecycle
A common Spark workflow looks like this : 

1) Create some input RDDs from external data or parallelize a collection is your driver program
2) Lazily transform them to define new RDDs using transformations like filter() or map() 
3) Ask Spark to cache() any intermediate RDDs that will need to be reused
4) Launch actions such as count() and collect() to kick off a parallel computation, which is then optimized and executed by Spark


Every transformation from Spark is lazy, everything you do just builds a DAG (metadata), until you call .collect() which executes your DAG (or another action like .count())

You have to be careful with .collect(), you don't want to call it on an RDD that is 1Tb in size, it will cause an out of memory error. Usually the driver is 1-2Gb.

If it's a large RDD, you can either save it directly to HDFS or sample a smaller part.

#### A list of available transformations 

map, intersection, cartesion, flatMap, distinct, pipe, filter, groupByKey, coalesce, mapPartitions, reduceByKey, repartition, mapPartitionWithIndex, sortByKey, partitionBy, sample, join, union, cogroup...

Most transformations are element-wise (they work on one element/row at a time)

Some transformations work on a per partition basis.

For example if you want to move an RDD with 50 partitions with a 1000 items each to a remote data source, you don't want to open and close a connexion to the remote database for each element. To avoid an element wise save, you need to open the connexion just one time for each partition, get an iterator to push the 1000 elements one by one, then close the connexion.

#### A list of actions 

reduce, takeOrdered, collect, saveAsTextFile, count, saveAsSequenceFile, first, saveAsObjectFile, take, countByKey, takeSample, foreach, saveToCassanda...

As a good developper for Spark, you should be aware of the type of RDDs in your DAG (HadoopRDD, ShuffleRDD, UnionRDD, PythonRDD...)
It's a good idea to draw your DAG with a pencil on a piece of paper

#### RDD Interface
All RDDs have an interface based on 5 methods
These 5 methods capture everything you need to know about and RDD :

- Set of partitions (number of "splits" that makes up the RDD)
- List of dependencies on parent RDDs (a metadata that says on which RDD this on is dependent on)
- Function to compute a partition given parents 
- Optional preferred locations
- Optional partitioning info for k/v RDDs (Partitioner)

Examples : 

| HadoopRDD                                                                                                                                                                          | FilteredRDD                                                                                                                                                                                          | JoinedRDD                                                                                                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Partitions = one per HDFS block<br>Dependencies = none <br>Compute (partition) = read corresponding block <br>preferredLocations(part) = HDFS block location<br>Partitioner = none | Partitions = same as parent RDD<br>Dependencies = "one-to-one" on parent<br>Compute (partition) = compute parent and filter it<br>preferredLocations(part) = none (ask parent)<br>Partitioner = none | Partitions = one per reduce task<br>Dependencies = "shuffle" on each parent<br>Compute (partition) = read and join shuffled data<br>preferredLocations(part) = none<br>Partitioner = HashPartitioner(numTasks) |

#### Ways to run Spark :
You can run Spark in 4 different ways, each architecture is quite different :
The first two have static partitioning, and the other two have dynamic partitioning

- Local : running one node 
- Standalone scheduler : if you're using Cassandra, running in a cluster environment
- YARN : if you're using Hadoop. You request X executors at the start of the job and you're stuck with them 
- Mesos : you can start your cluster with 10 executors, and can dynamically increase and decrease them over time

### Spark Runtime Architecture

### GUIs

### Memory and Persistence

### Jobs > Stages > Tasks

### Broadcast variables and accumulators

### PySpark

### Shuffle

### Spark Streaming
