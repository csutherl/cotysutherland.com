+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-19T20:21:00Z
description = ""
draft = false
slug = "configuring-pdi-for-use-with-hadoop-1-2-0"
title = "Configuring PDI for Use with Hadoop 1.2.0"

+++


### Purpose

In this post I have captured my work configuring Pentaho Data Integration (PDI) for use with Hadoop. It is formatted as a tutorial on how to setup PDI 4.4 with Hadoop 1.2.0 for your use.

### Prerequisites

* Java 1.6 or later (Not the OpenJDK distro as it is not compatible with this version of PDI)
* Pentaho Data Integration 4.4
* An up and running Hadoop 1.2.0 Cluster

### Configuring up Pentaho

Pentaho comes preconfigured for use with Hadoop 0.2.0. Which is great…unless you want to use a different version of Hadoop. The supported versions of Hadoop for use with Pentaho are outlined in their support matrix that can be found in the [Pentaho InfoCenter](http://infocenter.pentaho.com/help/index.jsp?topic=%2Finstall_ziptar%2Freference_support48.html). In my case, I was using Hadoop 1.2.0 so I found that it is necessary to create your own Hadoop configuration for Pentaho. I augmented the instructions located in the [Pentaho InfoCenter](http://infocenter.pentaho.com/help/index.jsp?topic=%2Fpdi_admin_guide%2Freference_active_hadoop_configuration.html) on “Creating a New Hadoop Configuration” with the instructions found in this [article](http://funpdi.blogspot.com/2013/03/pentaho-data-integration-44-and-hadoop.html). See updated instructions below:

1. Go into the $PDI_HOME/plugins/pentaho-big-data-plugin/hadoop-configurations directory.
1. Make a copy of the hadoop-20 folder and rename it to hadoop-120. This folder is the name of your new configuration.
1. Copy the following JAR files from your Hadoop NameNode into the $PDI_HOME/plugins/pentaho-big-data-plugin/hadoop-configurations/hadoop-120/lib/client directory:
    * commons-codec-1.4.jar
    * commons-configuration-1.6.jar
    * hadoop-core-1.2.0.jar
1. Remove these files after copying in the updated libraries:
    * commons-codec-1.3.jar
    * hadoop-core-0.20.2.jar
1. Update theactive.hadoop.configurationproperty, which configures the distribution of Hadoop that PDI will use when communicating with the cluster, in the $PDI_HOME/plugins/pentaho-big-data-plugin/plugin.properties file to look like the following code block:

    `active.hadoop.configuration=hadoop-120`

Once the steps above have been completed then you can start PDI and begin using the Big Data steps like Hadoop Copy Files and MapReduce.

