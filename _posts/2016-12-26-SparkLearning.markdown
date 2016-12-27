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
the following code is for get average.
val result = input.aggregate((0, 0))(
              /*an operator used to accumulate results within a partition, this operator is similar with fold()*/
               (acc, value) => (acc._1 + value, acc._2 + 1),
               /*an associative operator used to combine results from different partitions*/
               (acc1, acc2) => (acc1._1 + acc2._1, acc1._2 + acc2._2))
val avg = result._1 / result._2.toDouble
