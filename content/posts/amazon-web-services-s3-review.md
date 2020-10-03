+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-21T20:07:00Z
description = ""
draft = false
slug = "amazon-web-services-s3-review"
title = "Amazon Web Services S3 Review"

+++


### Purpose

To review Amazon Web Services (AWS) S3 in order to understand the service better.

### Questions/Statements to research

Below are a few questions/statements that I am interested in knowing more about regarding S3. I will spend some time researching each topic and document things that I find interesting about them.

### Security (Encryption) options for S3 data at rest

You have two options for encrypting data that is stored in S3, you can choose to enable Amazon’s Server Side Encryption or you can choose to use Client Side Exception (you encrypt the data before you send it). If you choose the latter approach you must manage your own encryption keys, tools, etc. Those approaches are pretty straight forward, but if you would like to read more, please visit the documentation on encryption [here](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

### Replication of buckets/data sets for multiple purposes. Short term vs long term environments. How long does it take to create replica sets?

I haven’t been able to find any documentation on this subject just yet, but am working on a few test cases. I am going to use an AWS public set to get some performance benchmarks. I am documenting this experiment here: [S3 and the Million Song Dataset Experiment](/posts/s3-and-the-million-song-dataset-experiment/).

### Security options around S3 buckets

If you look at bucket properties in S3, you will see that there few options listed pertaining to security. Those options may look simple, but you can actually customize the access control in various ways that should meet whatever use case you have.

A few examples of things you can do are:

* Add permissions to buckets themselves on a per individual basis. The options are List, Upload/Delete, View, and Edit.
* Add policies to bucket which allow you to have some fine grained control over what is actually in the bucket itself.
* Add CORS configuration which allows other web sites to access the content of your buckets.

For more information on access control, please visit the documentation [here](http://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html).

### Investigate config and performance settings for concurrent access and data protection.

As far as performance configuration/settings go with S3, I did not see a whole lot. It is a pretty simple interface for getting connected to and using. I did find an article [here](http://aws.typepad.com/aws/2012/03/amazon-s3-performance-tips-tricks-seattle-hiring-event.html) that talks about partitioning strategies for data when putting it into S3, which was interesting. There is another interesting article on S3 read performance [here](http://engineering.gnip.com/s3-read-performance/).

I can say that I had some experience copying data into and around within S3 and had no trouble at all. I put in over 100G of log data, which took a good hunk of time (documented in the S3 copying post [here](/posts/amazon-emr-experimentation-with-web-logs/)) but had no evidence of resource contention. I also tested an EMR Pig job with reading from a single 10G file with no apparent problems. The tests were performed with 3, 6, 11, and 20 nodes without issue.

