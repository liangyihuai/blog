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

Table 4-1. Transformations on one pair RDD (example: {(1, 2), (3, 4), (3, 6)})

---
 Function name :	 Purpose 	: Example 	: Result 
 
 reduceByKey(func)
 
: Combine values with the same key. 
 
 : rdd.reduceByKey( (x, y) => x + y)
 
 : {(1, 2), (3, 10)}
 
 groupByKey()
 
: Group values with the same key. 
 
 rdd.groupByKey()
 
 : {(1, [2]), (3, [4, 6])}
 combineByKey(createCombiner, mergeValue, mergeCombiners, partitioner): Combine values with the same key using a different result type.: See Examples  4-12 through 4-14.
 
 mapValues(func): Apply a function to each value of a pair RDD without changing the key. : rdd.mapValues(x => x+1): {(1, 3), (3, 5), (3, 7)}
 
 flatMapValues(func)
 
: Apply a function that returns an iterator to each value of a pair RDD, and for each element returned, produce a key/value entry with the old key. Often used for tokenization. 
 
 : rdd.flatMapValues(x => (x to 5)
 
 : {(1, 2), (1, 3), (1, 4), (1, 5), (3, 4), (3, 5)}
 
 keys()
 
: Return an RDD of just the keys. 
 
 : rdd.keys()
 
 : {1, 3, 3}
 
 values(): Return an RDD of just the values. : rdd.values(): {2, 4, 6}
 
 sortByKey(): Return an RDD sorted by the key.: rdd.sortByKey(): {(1, 2), (3, 4), (3, 6)}
---


[mllib-statistics](https://spark.apache.org/docs/2.0.2/mllib-statistics.html)
