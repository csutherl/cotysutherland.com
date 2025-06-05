+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-19T20:31:00Z
description = ""
draft = false
slug = "setting-up-a-small-hadoop-cluster"
title = "Setting up a Small Hadoop Cluster"

+++


### Purpose

In this blog post, I have captured the work I did in setting up a small (four node) Apache Hadoop cluster and have documented it in a manner that makes it easy to follow if you want to setup your own. I plan to use this cluster with Pentaho Data Integration (PDI) to do a small proof of concept on a single use case of PDI and Hadoop. I have listed all of the steps that I completed in this process along with references to Hadoop documentation below.

Note: Don’t laugh too much at any mistakes you may find as this is my first experience with Hadoop. Any comments/advice are welcome.

### Prerequisites

* Four machines (virtual or physical) with the following software installed:
    * Java 1.6 or later
    * ssh
    * rsync
    * Apache Hadoop (version 1.2.0 for my instance)

### Setup of the machines

All of the machines are virtual in this example, so I configured the first one and then cloned and updated it as I needed more nodes. Per the instructions for Installation in [Cluster Setup](http://hadoop.apache.org/docs/stable/cluster_setup.html), I decided to designate one machine as the NameNode, one as the JobTracker, and the other two as slaves. I spun up the first virtual machine and then configured it in the following manner…

### NameNode configuration

When the machine was created (OS Fedora 18), all of the required software packages were installed by default. After the creation process was done, I then retrieved and unpacked Hadoop 1.2.0 from [Hadoop Releases](http://hadoop.apache.org/releases.html#Download) into the ~/opt/hadoop directory (referenced as $HADOOP_HOME). Once completed, I setup my /etc/hosts file so that I can use host names in the configuration files instead of IP addresses. My /etc/hosts file looks like this:

```bash
$ cat /etc/hosts
127.0.0.1    localhost.localdomain localhost
::1          localhost6.localdomain6 localhost
10.11.80.1   namenode
10.11.80.2   jobtracker
10.11.80.3   slave0
10.11.80.4   slave1
```

‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍After updating /etc/hosts and with only a few minor changes I then configured the conf files as shown in the [Single Node Setup](http://hadoop.apache.org/docs/stable/single_node_setup.html). For the configuration files, instead of using localhost I used the IP addresses of the designated servers. For example, $HADOOP_HOME/conf/core-site.xml has the following property added to the configuration tag:

```xml
<property>
    <name>fs.default.name</name>
    <value>hdfs://namenode:9000</value>
</property>
```

‍‍‍‍‍‍‍‍‍‍And $HADOOP_HOME/conf/mapred-site.xml:

```xml
<property>
    <name>mapred.job.tracker</name>
    <value>hdfs://jobtracker:9001</value>
</property>
```

‍‍‍‍‍‍‍‍‍‍‍‍‍I made two more changes to the documented NameNode configuration:

1. The installation instructions say to set dfs.replication to 1 inside the hdfs-site.xml. However because we have two slaves, we actually want to set this value to 2 as this property tells the system how many times to replicate data (the default value is 3).
2. I did not want to setup group/user permissions within Hadoop at this time, so I made another change to the dfs.permissions property. I set the value of that property to false to remove permission checks within Hadoop which will allow anyone to access the Hadoop cluster and read/write/execute any files.

At this point, you can add your slave machine host names to the $HADOOP_HOME/conf/slaves file. One slave host name per line. For example:‍‍‍‍‍‍‍‍‍‍‍

```bash
$ cat conf/slaves
slave0
slave1
```

The last thing I did here was to disable the firewall on the machine and chkconfig it off. This ensures that the service does not start whenever the machine is restart. Note that doing that is only acceptable in a test environment, not production. If using Fedora 18, remember that Firewalld is used in conjunction with iptables so make sure to stop and disable firewalld as well as iptables. You could also take the approach of configuring your firewall correctly to allow for this traffic, but I an doing this in a sandbox environment so I did not take time to do so.

Ok…Once the configuration above has been completed, then you can clone the machine to create the JobTracker and slave machines.

### JobTracker configuration

Clone the NameNode, rename it as JobTracker, and start the clone. No further configuration required at this point. You will have to add SSH keys a bit later, but I will address that after all machines are up and configured.

### Slave0 and Slave1 configuration

To create the first slave machine, clone the JobTracker, rename it as Slave0, and start the clone. Within the $HADOOP_HOME/conf/slaves file, remove the two slave host names and replace with localhost. Now clone Slave0, rename it as Slave1, and start the new clone.

### Configuring SSH Keys

Now that all of the machines are up and running you need to configure SSH access to each one from the others in the cluster. In order to do this, you need to generate and export an SSH key to each machine. To generate an SSH key on a machine, use the following command:

`$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa`

‍‍‍‍‍Then, use the ssh-copy-id command to export the key into your local authorized_key file as well as the other machine’s authorized_key files. For example, if I started on the NameNode, then the commands would look like this:

```bash
$ ssh-copy-id localhost
$ ssh-copy-id JobTracker
$ ssh-copy-id Slave0
$ ssh-copy-id Slave1
```

### ‍‍‍‍‍‍‍‍‍‍‍‍‍Starting up the cluster!!

Before you can startup the cluster, there is one last thing that you must do to the NameNode. If you SSH into the NameNode and change directories into the $HADOOP_HOME directory, you can format the NameNode with the following command:

`$ bin/hadoop namenode -format`

‍‍‍‍‍‍Executing that command will format the Hadoop filesystem for you. After that, you just need to start the daemons. You do this on the NameNode by executing bin/start-dfs.sh and on the JobTracker by executing bin/start-mapred.sh. When you start up, you should see some messages stating that the datanodes/tasktracker nodes are starting up.

And that should be it! At this point, you have a successfully started your four node Hadoop cluster. You can verify by checking the web interfaces for the NameNode/JobTracker by visiting the URLs below:

```
NameNode - http://namenode:50070/
JobTracker - http://jobtracker:50030/
```

Thanks for reading and please feel free to leave comments if there are any problems or anything that doesn’t work with this and I will be sure to fix it.

