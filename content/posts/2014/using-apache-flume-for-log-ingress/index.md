+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-20T03:40:00Z
description = ""
draft = false
slug = "using-apache-flume-for-log-ingress"
title = "Using Apache Flume for Log Ingress?"

+++


I wanted to evaluate Apache Flume for the ingress of some web server access logs to get a feel for how Apache Flume works. In this post I will go over some of the things that I found out about Apache Flume, as well as a few examples of situations in which it would be useful to utilize.

### So what is Apache Flume anyway?

Apache Flume is a service for efficiently collecting, aggregating, and moving large amounts of log data. It is a very simple architecture of based streaming data flows. All data that moves through Flume is considered to be a stream. The overhead of the agent is pretty small at around 20MB minimum RAM required, but you can increase the RAM available to Flume for a performance gain.

Flume is comprised of three parts: sources, channels, and sinks. Each of these parts serves a single purpose that makes up Flume. Sources, as you might guess, are where the data is originating from. There are lots of built in types of sources (Avro, Exec, and NetCat to name a few), but you can also create your own custom sources. Opposite of Sources are Sinks. Sinks are for taking the data from a source and outputting it to some destination. Examples of Sink types in Flume are HDFS, Avro, IRC, and custom sinks. Now you may be asking yourself “How does data move from source to sink?”…if you are, the answer is Channels. Channels in Flume are mechanisms to move data from source to sink. You can define custom channels if you’d like, but there are lots of predefined channels such as memory, JDBC, and file.

Now that we’ve reviewed the basic parts of Flume, why would I use it? As I said before, Flume considers everything a stream that comes in which means that even if you have files as a source that it is streaming. This is a really awesome tool if you need real time log data like what you would get from tail, but my case doesn’t require that. There is some trickery that you can do in Flume to give the appearance of batch loading, but it is still just spooling data up and then offloading it when you hit a predefined threshold. Most of what I am finding states that Flume is event based, but I found a couple documents that mentioned it has the capability to poll sources as well. Unfortunately, I haven’t found very much more about that, so I am thinking that it was in the OG (Original Generation) of Flume, not NG (Next Generation).

### My findings when reviewing Flume for this use case

As I mentioned, the use case that I’m researching was to pull a set of logs from a group of web servers and put them into HDFS.

Some assumptions that I made were:

* I will have some way of keeping the script up to date so that it pulls only the files that I want.
* The files will always be in the same location with the same naming pattern. I am assuming that these files are rolled from the actual access_log.

With these assumptions made, I determined that there were three options of using Flume for this use case. Option 1, install an agent on the web server that writes across the network into HDFS real time with an exec source (tail on the log file). We probably don’t want to do that as it requires installing an agent on the server side. Option 2, install an agent somewhere in between the two servers or on the master node to pull the logs into HDFS. This approach would work, but again the sources for Flume are streaming, not batch load. What I would need to do for this is to create an exec source that fires a script which gets the logs and cats them out so that I can stream stdout into HDFS. To me that seems like overkill, so I thought up option 3. Option 3 is simple, write a bash script that runs on cron which pulls the logs, decompresses them (if we want to), and then perform a `hadoop fs -put` on the file to place it where we want the files to be in HDFS.

### Conclusion

I do not think Flume works well for this use case. I need to do things in more of a batch manner and the overhead (while pretty small at about 20M of RAM for the JVM) of having an agent running to do something that can be done just as easily with cron is unnecessary.

I know I didn’t talk very much about the actual configuration behind Flume, but its really quiet simple and probably better stated by the Apache folks. [Here](http://flume.apache.org/FlumeUserGuide.html#a-simple-example) is a link to a simple example if you care to check it out.

