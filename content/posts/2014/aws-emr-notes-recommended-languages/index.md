+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-19T20:38:00Z
description = ""
draft = false
slug = "aws-emr-notes-recommended-languages"
title = "AWS EMR Notes - Recommended Languages"

+++


### Purpose

This post is meant to capture my notes on research, testing, and recommendations of differing Elastic MapReduce (EMR) programming approaches (languages/architectures) for a list of use cases.

### Sample use cases to research

Here is a small list of the different languages and data movement architectures that are a part of a typical Hadoop ecosystem: [Pig](https://pig.apache.org/), Java, [Python](http://python.org/), [Hive](http://hive.apache.org/), [R](http://www.r-project.org/), [Cloudera Impala](http://www.cloudera.com/content/cloudera/en/products-and-services/cdh/impala.html), [Flume](http://flume.apache.org/), [Sqoop](http://sqoop.apache.org/), [Mahout](http://mahout.apache.org/).

### Breakdown of languages/architectures:

* SQL interface – The options for a SQL interface into EMR that works natively on AWS (or any Hadoop distro) are limited. It looks like there is only Hive or Impala from what I’ve read so far; I will explain each of these.
    * Hive – Hive Query Language is very similar to regular SQL in syntax. It’s easy to pick up and start using for a person with SQL experience. HiveQL creates and executes MapReduce jobs which gives it the ability to process large quantities of data in a somewhat performant manner. Hive is not recommended for use for real time/interactive queries.
    * Impala (Developer guide notes here) – With Impala, you can basically use regular SQL. It is Massively Parallel Processing instead of MapReduce, which increases speed substantially over Hive. Impala is meant to be use for interactively querying of data that is stored within HDFS/HBase.
* ETL (aggregation/cleansing)
    * Hive – Explained above. You just take the output of HiveQL queries and persist them into Hive defined tables.
    * Pig – Language built specifically for ETL in Hadoop. Pig Latin generates and executes MapReduce code much like Hive does. Pig has it’s own syntax which is very different than SQL, so the learning curve on this is a bit more steep than Hive. It is very effective at processing/transforming large amounts of data in parallel.
    * Lower level languages such as Python and Java – If you use these languages, you have to have a deeper understanding of the MapReduce framework. You have to build at least mappers and reducers (if you want to do anything more than basic aggregation/canned reducer tasks). With that said, you gain flexibility and the ability to customize all sorts of aspects of the EMR job. I feel that this approach would take a lot longer to build any usable code for someone coming in with limited knowledge of Hadoop whereas you don’t need to have all of the knowledge up front to write Hive/Pig jobs. Another benefit of knowing a lower level language is that you can extend Hive/Pig/Impala with User Defined Functions. That gives you the ability to extend the higher level languages with functions that you need.
* Data Movement (no transformation)
    * Flume – A distributed service that collects, aggregates, and moves large amounts of log data/events in real time. The agent that runs on machines is comprise of sources (where the data originates from), channels (how the data moves), and sinks (basically targets). You configure that agent to do a particular job and it runs and captures data in real time from the logging (or other) sources.
I created a post about a specific use case for Flume here
    * Sqoop – Designed for transferring bulk data between Hadoop and structured datastores, such as relational databases. This could work well for staging large amounts of data from sources into HDFS to be transformed with Hive/Pig.
* Analytics
    * R – Currently THE programming language for statistics and graphics. R has a ton of stats libraries that can be used to do things ranging from basic statistics to machine learning. It is the best data analysis language around (seconded by python) imho.
    * Mahout – a library of machine learning algorithms that are all packaged up for use. Mahout does not have a dependency on Hadoop (you can run it standalone), but works well within Hadoop.
    * [Pandas](http://pandas.pydata.org/) – I am throwing pandas in for those of you readers that are interested in python as your language of choice. Pandas works similar to how R does in its uses, but the syntax makes a bit more sense to the every day programmer (imo ).

### What languages/tools should we use for various scenarios?

I hope that I answered a majority of that question with the explanations above. On top of that, my suggestion for getting started in this space would be to get started with Hive/Impala and Pig though. Assuming that we would be working with lots of flat files at first, it is pretty straightforward to move them into HDFS and retrieve the output. Once there is a good basic understanding around those languages/tools, then you can move into learning about scheduling workflows and moving data in and out of Hadoop from relational databases in some other manner.

### Other random notes…

### Amazon EMR Hive is a bit different from Apache Hive

There is an article explaining the differences [here](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-hive-differences.html), but I will touch on them briefly below. There are also additional features in Amazon EMR’s Hive that do not exist in Hadoop Hive; see [this](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-hive-additional-features.html)article.

* The default input formats are different. The Apache Hive default input format is text. The Amazon EMR default input format for Hive is org.apache.hadoop.hive.ql.io.CombineHiveInputFormat.
* If you have many GZip files in your Hive cluster, you can optimize performance by passing multiple files to each mapper.
* The log file location of EMR’s Hive is different than Apache Hive.
* The Thrift service ports are different.
* EMR does NOT support Hive Authorization.
* The hive.merge.smallfiles.avgsize parameter is ignored if the output path is in S3.

