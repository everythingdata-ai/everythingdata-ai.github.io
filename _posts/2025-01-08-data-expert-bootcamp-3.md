---
layout: post
title: Spark Fundamentals - Data Expert Bootcamp
categories: [Data Engineering, Spark]
---

Today I continue with Zach Wilson Data Engineering Bootcamp.
Week 3 is about Apache Spark Fundamentals.
Since I am new to Spark, this week have been quite challenging and the pace was a bit too fast for a beginner.
I decided to take a break from the Bootcamp after today and come back to it in a few days.
Here are my notes from Week 3.

## 1 - Spark + Iceberg - Memory Tuning, Joins, Partition

### What is Apache Spark ?
Spark is a distributed compute framework that allows you to process very large amount of data efficiently. It's the successor of MapReduce, Hadoop and Hive.

### Why is Spark so good ?
- Spark leverages RAM much more effectively than previous iterations of distributed compute (way faster than Hive/JAVA MR/etc)
- Spark is storage agnostic, allowing a decoupling of storage and compute
- Spark has a huge community of developers so StackOverflow/ChatGPT will help you troubleshoot

### When is Spark not so good ?
- If nobody else in the team/company knows Spark
- If the company already uses something else a lot (BigQuery, Snowflake...), it's better to have 20 big query pipelines than 19 bigquert pipelines and 1 spark pipeline

### How does Spark work ?
Spark has a few pieces to it, if Spark was a basketball team, it would be :

- The plan : the play/strategy
This is the transformation you describe in Python, Scala or SQL
The plan is evaluated lazily : execution only happens when it needs to
When does execution need to happen : writing output or when part the plan depends on the data itself (e.g. calling dataframe.colleect() to determin the next set of transformations)

- The driver : the coach of the team
The driver reads the plan
Important Spark driver settings : spark.driver.memory / spark.driver.memoryOverheadFactor
The rest of the settings shouldn't be touched
The driver determines when to actually start executing the job and stop being lazy, how to join datasets, as well as how much parallelism each step needs

- The executors : the players 
The driver passes the plan to the executors who do the actual work (transform, filter, aggregate...)
The settings that should be defined : spark.executor.memory - a low number lay cause "spill to disk" which will cause your job to slow down / spark.executor.cores - default is 4, shouldn't go higher than 6 / spark.executor.memoryOverheadFactor - usually 10%

### The types of JOINs in Spark
- Shuffle sort-merge join : the least performant, but useful because it's the most versatile and always works, especially when both sides of the join are large
- Broadcast hash join : Works well if one side of the join in small
- Bucket joins : a join without shuffle

### Is Shuffle good or bad ? 
At low-to-medium volume it's really good and makes lives easier
At high volumes >10Tb it's painful and better avoided

### How to minimize shuffle at high volumes ?
- Bucket the data if multiple JOINs or aggregations are happening downstream 
- Spark has the ability to bucket data to minimize or eliminate the need for shuffle when doing JOINs
- Bucket joins are very efficient but have drawbacks, the main drawback is the initial parallelism = number of buckets. Bucket joins only work if the two tables number of buckets are multiples of each other : always use powers of 2 for # of buckets

### Shuffle and Skew
Sometimes some partitions have dramatically more data than other, this can happen because there aren't enough partitions or the natural way the data is (Beyonce gets a lot more notifications than the average Facebook user)

You can tell that your data is skewed if your job is getting to 99%, taking forever and then failing.

To avoid that, you can do a box and whiskers plot of the data to see if there's any extreme outliers

You can deal with skew in multiple ways :
- Adaptive query execution - only in Spark 3+ : Set spark.sql.adaptive.enabled = True 
- Salting the GROUP BY = best option before Spark 3 : Group by a random number, aggregate + group by again. Be careful with things like AVG, break it into SUM and COUNT and divide
- One side of the pipeline that processes the outliers (Beyonce) and another side that processes everyone else
- Use explain() to show the join strategies that Spark will take

```Python
df.withColumn("salt_random_column", (rand * n).cast(IntegerType))
.groupBy(groupByFields, "salt_random_column")
.agg(aggFields)
.groupBy(groupByFields)
.agg(aggFields)

```
### How can Spark read data ?
- From the lake : Delta Lake, Apache Iceberg, Hive metastore...
- From an RDBMS : Postgres, Oracle...
- From an API : make a REST call and turn into data, not advised if you're making multiple calls
- From a flat file : CSV, JSON...

### Spark output datasets
The output should almost always be partitioned on "date" which is the execution date of the pipeline

### Lab 
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import expr, col, lit
spark = SparkSession.builder.appName("Jupyter").getOrCreate()

spark

df = spark.read.option("header", "true") \
.csv("/home/iceberg/data/events.csv") \
.withColumn("event_date", expr("DATE_TRUNC('day', event_time)"))

# Use take instead of collect to not cause an out of memory error
df.join(df, lit(1) == lit(1)).take(5)

# Split the data by event_date
sorted = df.repartition(10, col("event_date")) \
# sortWithinPartitions - Will sort locally in each partition
# sort - Global sort of the data, as far back as you can go, it's very slow
        .sortWithinPartitions(col("event_date"), col("host"), col("browser_family")) \
        .withColumn("event_time", col("event_time").cast("timestamp")) \

sorted.explain()
sorted.show()
```

```sql
CREATE DATABASE IF NOT EXISTS bootcamp

CREATE TABLE IF NOT EXISTS bootcamp.events (
    url STRING,
    referrer STRING,
    browser_family STRING,
    os_family STRING,
    device_family STRING,
    host STRING,
    event_time TIMESTAMP,
    event_date DATE
)
USING iceberg
PARTITIONED BY (years(event_date));

CREATE TABLE IF NOT EXISTS bootcamp.events_sorted (
    url STRING,
    referrer STRING,
    browser_family STRING,
    os_family STRING,
    device_family STRING,
    host STRING,
    event_time TIMESTAMP,
    event_date DATE
)
USING iceberg;

CREATE TABLE IF NOT EXISTS bootcamp.events_unsorted (
    url STRING,
    referrer STRING,
    browser_family STRING,
    os_family STRING,
    device_family STRING,
    host STRING,
    event_time TIMESTAMP,
    event_date DATE
)
USING iceberg;
```

```python
start_df = df.repartition(4, col("event_date")).withColumn("event_time", col("event_time").cast("timestamp")) \
    

first_sort_df = start_df.sortWithinPartitions(col("event_date"), col("browser_family"), col("host"))

sorted = df.repartition(10, col("event_date")) \
        .sortWithinPartitions(col("event_date")) \
        .withColumn("event_time", col("event_time").cast("timestamp")) \

start_df.write.mode("overwrite").saveAsTable("bootcamp.events_unsorted")
first_sort_df.write.mode("overwrite").saveAsTable("bootcamp.events_sorted")
sorted.write.mode("overwrite").saveAsTable("bootcamp.events")
```

```sql
SELECT SUM(file_size_in_bytes) as size, COUNT(1) as num_files, 'sorted' 
FROM demo.bootcamp.events_sorted.files

UNION ALL
SELECT SUM(file_size_in_bytes) as size, COUNT(1) as num_files, 'unsorted' 
FROM demo.bootcamp.events_unsorted.files
```
## 2 - Reducing shuffling 

### What is shuffling ?
Data shuffling is a process in modern data pipelines where the data is randomly redistributed across different partitions to enable parallel processing and better performance

There a highly parallelizable queries, and others not so much :
- Exetremely parallel : SELECT, FROM, WHERE. These queries are infinitely scalable. If you had a billion rows and a billion machines, each machine can have one row and it will be very quick to retrieve all the data

- Kind of parallel : GROUP BY, JOIN, HAVING

- Not parallel : ORDER BY. The most painful keyword in SQL, the only way to sort 1 million rows scattered on 1 million machines is if all the data gets passed to one machine, that's the opposite of parallel. Using ORDER BY should be on tables with thousands rows, not millions or more.
### How to make GROUP BY more efficient ?
Give GROUP BY some buckets and guarantees
Reduce the data volume as much as you can

### How reduced fact data modeling gives you superpowers
- Fact data often has this schema : user_id, event_time, action, date_partition. Very high volume, 1 row per event
- Daily aggregate often has this schema : user_id, action_count, date_partition. Medium sized volume, 1 row per user per day
- Reduced fact take this one step further: user_id, action_count Array, month_start_partition, year_start_partition. Low volume, 1 row per user per month/year

Impact on analysis : Multi-year analyses took hours instead of weeks

## 3 - High performance Spark

### Spark Server vs Spark Notebooks
Spark Server (how Airbnb does it) : Upload a .jar that gets ran. Every run is fresh, things get uncached automatically. It's good for testing. Starts and stops on its own.

Spark Notebook (how Netflix does it) : One Spark session that stays live. Start and stop it yourself. Make sure to call unpersist().

### Databricks considerations
Databrick should be connected with Github :
- PR review process for EVERY change
- CI/CD check

### Caching and Temporary views
Temporary views : always get recomputed unless cached !

Caching : 
- Storage levels : MEMORY_ONLY / DISK_ONLY / MEMORY_AND_DISK (the default)
- The rule is you should never use disk only, caching is really only good if it fits into memory, otherwise there's probably a staging table in your pipeline you're missing !
- In notebooks, call unpersist when you're done otherwise the cached data will remain

### Caching vs Broadcast
Caching stores pre-computed values for re-use, stays partitioned
Broadcast Join is small data that gets cached and shipped in entirety to each executor

### Broadcast Join optimization
One of the most important optimizations in Spark, usually triggered automatically by Spark when possible.
If you have one side of the Join that is small (default is 10mb, defined in spark.sql.autoBroadcastJoinThreshold, but you can get it to the orther of single digit Gbs), it gets shipped to all executors instead of shuffling it (splitting it up)
Broadcast JOINs prevent Shuffle.

You can explicitly wrap a dataset with broadcast(df) too.

### UDFs 
User Defined Functions allow you to do complex processing
PySpark UDFs are slow compared to Scala.

Apache Arrow optimizations in recent versions of Spark have helped PySpark become more inline with Scala Spark UDFs.

Dataset API allows you to not even need UDFs, you can use pure Scala functions instead.

### DataFrame vs Dataset vs SparkSQL
The 3 ways you can transform data is Spark.

Dataset is best for pipelines that require unit and integration tests. It is Scala only !

DataFrame is more suited for pipelines that are more hardened and less likely to experience change.

SparkSQL is better for pipelines that are used in collaboration with data scientists.

### Parquet
An amazing file format : Run-length encoding allows for powerful compression
Don't use global .sort() : painful, slow, annoying because it adds an extra shuffle step
Use .sortWithinPartitions : parallelizable, gets you good distribution

### Spark Tuning 
- Executor memory : don't just set to 16GBs and call it a day, wastes a lot
- Driver memory : only needs to be bumped up if you're calling df.collect() or have a very complex job
- Shuffle partitions : default is 200. Aim for 100MBs per partition to get the right sized output datasets.
- AQE (Adaptive Query Execution) : helps with skewed datasets, wasteful if the dataset isn't skewed


