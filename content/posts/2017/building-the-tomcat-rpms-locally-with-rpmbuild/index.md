+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2017-06-12T18:24:00Z
description = ""
draft = false
slug = "building-the-tomcat-rpms-locally-with-rpmbuild"
title = "Building The Tomcat RPMs Locally with rpmbuild"
tags = ["Apache Tomcat", "RPM"]

+++


Ever wondered how to build the tomcat RPM locally? Well you’re in luck! This post will detail how to build the tomcat RPM locally using rpmbuild and the Fedora tomcat package. This method will also work for any other Fedora package.

### STEP 1: CLONE THE PACKAGE REPOSITORY

In order to get started you need two things. First, you need fedpkg to clone the repository. Second, you’ll need to install the rpm-build package so that you can use rpmbuild to build the RPM. You can install them with the following:

`$ yum install rpm-build fedpkg`

After you have those two packages installed, cloning the repository is a one line task with fedpkg:

```
$ fedpkg co tomcat
Cloning into 'tomcat'...
....
Receiving objects: 100% (1124/1124), 223.87 KiB | 143.00 KiB/s, done.
Resolving deltas: 100% (716/716), done.
Checking connectivity... done.
```

After that’s done, change directories into the tomcat directory and examine the files there. One of them is the tomcat specfile, which tells rpmbuild how to build the RPMs. You’ll also need to download the source tarball for rpmbuild to build, which is done with fedpkg too:

```
$ fedpkg sources
Downloading apache-tomcat-8.0.44-src.tar.gz
########################################### 100.0%
```

### STEP 2: BUILDING THE RPMS

Now that you have all of the sources you need to build the tomcat package, you can get started. Building is a bit convoluted because of how rpmbuild works. In order to build the package, you’ll need to copy all of the sources into the rpmbuild SOURCES directory (~/rpmbuild/SOURCES/ by default). Once the copying is done, then you can start the build using the build command below. Note that the build takes a couple of minutes to complete and dumps a lot of output to the screen.

```
$ cp * ~/rpmbuild/SOURCES/
$ rpmbuild -ba tomcat.spec
Executing(%prep): /bin/sh -e /var/tmp/rpm-tmp.K9jbpk
+ umask 022
+ cd /home/coty/rpmbuild/BUILD
+ cd /home/coty/rpmbuild/BUILD
+ rm -rf apache-tomcat-8.0.44-src
+ /usr/bin/gzip -dc /home/coty/rpmbuild/SOURCES/apache-tomcat-8.0.44-src.tar.gz
....
Checking for unpackaged file(s): /usr/lib/rpm/check-files /home/coty/rpmbuild/BUILDROOT/tomcat-8.0.44-1.fc24.x86_64
Wrote: /home/coty/rpmbuild/SRPMS/tomcat-8.0.44-1.fc24.src.rpm
Wrote: /home/coty/rpmbuild/RPMS/noarch/tomcat-8.0.44-1.fc24.noarch.rpm
....
Executing(%clean): /bin/sh -e /var/tmp/rpm-tmp.Ha5Yti
+ umask 022
+ cd /home/coty/rpmbuild/BUILD
+ cd apache-tomcat-8.0.44-src
+ /usr/bin/rm -rf /home/coty/rpmbuild/BUILDROOT/tomcat-8.0.44-1.fc24.x86_64
+ exit 0
```

Once the build has completed your newly created tomcat RPMs are written to ~/rpmbuild/RPMS/noarch/.

### STEP 3: INSTALLING THE RPMS

What happens in this step is sort of up to what you need to do. You can very easily install the RPMs from the disk with localinstall:

`$ yum localinstall ~/rpmbuild/RPMS/noarch/tomcat-*.rpm`

Alternatively you could create a local yum repository and install from there. The benefit of using a yum repository instead of localinstall is that if you wanted to test or distribute the new package to multiple machines you can easily host the yum repo and access it over http(s). To create a local yum repo, you need to use the createrepo command (which requires the createrepo package to be installed) to create the repo metadata. I usually just do this in the same directory that the RPMs are written to.

```
$ createrepo ~/rpmbuild/RPMS/noarch/
Spawning worker 0 with 2 pkgs
Spawning worker 1 with 2 pkgs
....
Workers Finished
Saving Primary metadata
Saving file lists metadata
Generating sqlite DBs
Sqlite DBs complete
```

Then you can create a yum repo file to point to the newly created repository:

```
$ cat /etc/yum.repos.d/local.repo
[local]
name=local repo
baseurl=file:///root/rpmbuild/RPMS/noarch/
enabled=1
gpgcheck=0
keepcache=0
```

After it’s all setup then you can install the tomcat package as usual from the local yum repo.

