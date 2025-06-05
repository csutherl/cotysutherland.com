+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-20T19:54:00Z
description = ""
draft = false
slug = "amazon-emr-experimentation-with-web-logs"
title = "Amazon EMR Experimentation with Web Logs"

+++


### Purpose

This post is to document the work that I am doing with the AWS Elastic MapReduce (EMR) tool. I will be recording some benchmarks and general notes about my experience with EMR.

### The Test Data

I took the example web logs set from the analyzing web log data tutorial (found [here](http://docs.aws.amazon.com/gettingstarted/latest/emr/getting-started-emr-tutorial.html)) and cloned it into larger sets. I installed s3cmd and pulled all of the files down to my local machine, then ran a loop to cat them together. I now have a 1G, a 10G, and a 100G access_log file comprised of concatenating those original six files quite a few times. I just uploaded those three files via the web upload tool in the S3 management page because I could not get s3cmd to put them in s3 for some reason (I didn’t bother to figure out why at the time). I lost the metric that I had on the 1G and 10G file, but the 100G failed the first time so I had to restart it’s upload. The 100G file stared uploading at 8:40 on Feb 19 and completed around 17:00 on Feb 19.

That is all of the test data that I am currently using, but I do plan to create a python script to generate randomly sized web logs and place them in S3 so that we have a more realistic environment for testing.

### Implementation

****Test 1****For my first test, I processed a single 10G web log with the Pig script from the preconfigured application. The script processes the file and outputs a few things; top 50 external referrers, top 50 IPs, top 50 search terms from bing/google, total requests bytes per hour. Below is a table to document the performance of the processing application. As you can see in the table, two worker nodes processed the file in just over three hours.

****Test 2****Theoretically performance should scale linearly depending on the number of nodes that we have…so I am going to multiply the number of worker nodes by ~10 and see how fast it processes then. I will keep the hardware/AMI version the same so that there is no change there.

Update after run: Interestingly enough, the performance did not scale linearly. I added almost ten times the resources for this run, but did not get 1/10 of the run time. I noticed that while this job was running that the Pig process was reported as running before all of the nodes were provisioned. I think that as nodes were provisioned, they were being added to the job, which would stretch out the run time a bit. I checked the monitoring status and do not see any I/O contention with S3, so that is the only explanation I can come up with as to why performance didn’t increase linearly.

****Test 3****I am going to grab one more data point so that we can make some general assumptions about throughput on m1.medium instances. This time I will run the same processing job, but with only 10 nodes.

****Test 4****Since Test 3’s time was so close to Test 2 I am running one more test with 5 nodes. I am trying to find the point where the ROI of more hardware is plateaued.

|Test iteration|AMI Version|Master Hardware|Master instance count|Slave Hardware|Slave instance count|Total Elapsed Time|Pig Runtime|
|---|---|---|---|---|---|---|---|
|1|3.0.3|m1.medium|1|m1.medium|2|3 hrs 7 mins|2 hrs 58 mins|
|2|3.0.3|m1.medium|1|m1.medium|19|35 mins|25 mins|
|3|3.0.3|m1.medium|1|m1.medium|10|42 mins|35 mins|
|4|3.0.3|m1.medium|1|m1.medium|5|1 hr 12 mins|1 hr 3 mins|

Note: There is apparently some quota on the number of EC2 nodes that you can spin up set to 20 by default. If you want to run more than 20 instances, you have to fill out some request form. See “Q: How many instances can I run in Amazon EC2?” found in the FAQ [here](http://aws.amazon.com/ec2/faqs/).

