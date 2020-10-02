+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2017-06-17T01:27:00Z
description = ""
draft = false
slug = "how-to-install-and-test-rpms-from-a-koji-scratch-build"
title = "How To Install and Test RPMs from a Koji scratch-build"
tags = ["Apache Tomcat", "RPM"]

+++


This document outlines how to test scratch builds of Tomcat for Fedora Rawhide. It is pretty basic, but the steps below allow you to go from scratch-build to installed RPMs pretty quickly. These same steps apply to any other Fedora package as well.

## Building

First, you need to initiate a scratch-build for your package and the desired target. To do so, ensure that your current working directory is the git repository for the package and that you're on the master (master is rawhide) branch. If you wanted to build for another OS version (such as Fedora 24), you could check out another branch (fc24) and build that.

`$ fedpkg scratch-build`

This will post some output to the terminal about the build while the build is in progress. The important part to note is the taskID from the "Task info" line.

```bash
Building tomcat-8.0.44-1.fc27 for rawhide
Created task: 19976249
Task info: https://koji.fedoraproject.org/koji/taskinfo?taskID=19976249
Watching tasks (this may be safely interrupted)...
19976249 build (rawhide, /rpms/tomcat:e1abf5cd6dee5174693babb2792b20a8c5d35d43): free
19976249 build (rawhide, /rpms/tomcat:e1abf5cd6dee5174693babb2792b20a8c5d35d43): free -&amp;gt; open (buildhw-09.phx2.fedoraproject.org)
  19976250 buildSRPMFromSCM (/rpms/tomcat:e1abf5cd6dee5174693babb2792b20a8c5d35d43): open (buildvm-ppc64le-06.ppc.fedoraproject.org) 
  ....
```

While the build is in progress output will continue to be posted to the screen until it has completed or failed.

## Downloading

When I originally wrote this post, Koji did not have the ability to download scratch-build RPMs, so you'd need to a script to do so for you. The [RFE](https://bugzilla.redhat.com/show_bug.cgi?id=675140) that was filed has been closed and the "download-task" command is available to download scratch builds as of koji version 1.10.1. If your version of koji is older than that you can use the download-scratch.py script in comment 1 on the RFE linked above.

After the build has completed, you can download all of the RPMs associated with that taskID by executing the following:

```bash
$ koji download-task 19976249
Downloading [1/11]: tomcat-javadoc-8.0.44-1.fc27.noarch.rpm
....
```

## Installing

‍The download-task command will download all of the RPMs to your current working directory (I suggest that you create and cd into a directory to store the RPMs before downloading). Once that is done, you'll need to scp them to a system that's running Rawhide to install:

`$ scp tomcat*.rpm fc27-testVM:~/tomcat-testing/`

‍When the scp has completed and the RPMs are on a Rawhide system SSH into the system, change into the directory where they were put (~/tomcat-testing in my case), and you can use yum to localinstall them such as:

```bash
$ ssh fc27-testVM
$ cd ~/tomcat-testing/
$ yum localinstall tomcat
```



