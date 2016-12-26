
---
layout: post
title: Spark Learning
date: 2016-12-26 15:48
comments: true
external-url: blog.csdn.net/liangyihuai
categories: Spark
---


- submit the spark job.

```
mvn clean && mvn compile && mvn package
$SPARK_HOME/bin/spark-submit \
  --class com.oreilly.learningsparkexamples.mini.java.WordCount \
  ./target/learning-spark-mini-example-0.0.1.jar \
  ./README.md ./wordcounts
```
