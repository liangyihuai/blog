---
layout: post
title: Spark Learning
date: 2016-12-26 15:48
comments: true
external-url: blog.csdn.net/liangyihuai
categories: BigData
---


- submit the spark job.

```
mvn clean && mvn compile && mvn package
$SPARK_HOME/bin/spark-submit \
  --class com.oreilly.learningsparkexamples.mini.java.WordCount \
  ./target/learning-spark-mini-example-0.0.1.jar \
  ./README.md ./wordcounts
```

- RDD: 
Each RDD is split into multiple partitions, which may be computed on different nodes of the cluster.

As we’ve discussed, RDDs support two types of operations: transformations and actions. Transformations are operations on RDDs that return a new RDD, such as map() and filter(). Actions are operations that return a result to the driver program or write it to storage, and kick off a computation, such as count() and first(). Spark treats transformations and actions very differently, so understanding which type of operation you are performing will be important. **If you are ever confused whether a given function is a transformation or an action, you can look at its return type: transformations return RDDs, whereas actions return some other data type.**

It is important to note that each time we call a new action, the entire RDD must be computed “from scratch.” To avoid this inefficiency, users can persist intermediate results, as we will cover in “Persistence (Caching)”.

- aggregate
the method can change the Type. the following code is for get average.
```java
val result = input.aggregate((0, 0))(
              /*an operator used to accumulate results within a partition, this operator is similar with fold()*/
               (acc, value) => (acc._1 + value, acc._2 + 1),
               /*an associative operator used to combine results from different partitions*/
               (acc1, acc2) => (acc1._1 + acc2._1, acc1._2 + acc2._2))
val avg = result._1 / result._2.toDouble
```

- cogroup??

- partitionBy

```
val sc = new SparkContext(...)
val userData = sc.sequenceFile[UserID, UserInfo]("hdfs://...")
                 .partitionBy(new HashPartitioner(100))   // Create 100 partitions
                 .persist()

```

> Note that partitionBy() is a transformation, so it always returns a new RDD — it does not change the original RDD in place. RDDs can never be modified once created. Therefore it is important to persist and save as userData the result of partitionBy(), not the original sequenceFile(). Also, the 100 passed to partitionBy() represents the number of partitions, which will control how many parallel tasks perform further operations on the RDD (e.g., joins); in general, make this at least as large as the number of cores in your cluster.without persist(), subsequent RDD actions will evaluate the entire lineage of partitioned, which will cause pairs to be hash-partitioned over and over.

```
scala> val pairs = sc.parallelize(List((1, 1), (2, 2), (3, 3)))
pairs: spark.RDD[(Int, Int)] = ParallelCollectionRDD[0] at parallelize at <console>:12

scala> pairs.partitioner
res0: Option[spark.Partitioner] = None

scala> val partitioned = pairs.partitionBy(new spark.HashPartitioner(2))
partitioned: spark.RDD[(Int, Int)] = ShuffledRDD[1] at partitionBy at <console>:14

scala> partitioned.partitioner
res1: Option[spark.Partitioner] = Some(spark.HashPartitioner@5147788d)
```

- shared variables: 		aggregation and broadcasts

Spark’s shared variables, accumulators and broadcast variables, relax this restriction for two common types of communication patterns: aggregation of results and broadcasts.


- mapPartitions and map

>Q1. What's the difference between an RDD's map and mapPartitions
map works the function being utilized at a per element level while mapPartitions exercises the function at the partition level.


>Tip
Spark’s documentation consistently uses the terms driver and executor when describing the processes that execute each Spark application. The terms master and worker are used to describe the centralized and distributed portions of the cluster manager. It’s easy to confuse these terms, so pay close attention. For instance, Hadoop YARN runs a master daemon (called the Resource Manager) and several worker daemons called Node Managers. Spark can run both drivers and executors on the YARN worker nodes.

- spark configuration precedence order
The highest priority is given to configurations declared explicitly in the user’s code using the set() function on a SparkConf object. Next are flags passed to spark-submit, then values in the properties file, and finally default values.

- coalesce() operator

















[mllib-statistics](https://spark.apache.org/docs/2.0.2/mllib-statistics.html)
[google-math](http://www.changhai.org/articles/technology/misc/google_math.php)
[programming-guide](http://spark.apache.org/docs/latest/programming-guide.html)
[tuning-spark](http://spark.apache.org/docs/latest/tuning.html)
