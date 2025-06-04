+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2018-08-06T19:47:00Z
description = ""
draft = false
slug = "tomcat-cant-bind-to-unprivileged-ports"
tags = ["Apache Tomcat", "SELinux", "RPM"]
title = "Tomcat can't bind to unprivileged ports, thanks SELinux"

+++


This issue recently arose on the [tomcat users list](http://tomcat.apache.org/lists.html#tomcat-users), so I thought I would write up a quick blog post about it to make the solution a bit easier to find on the web.

Here are the steps to reproduce the issue along with the required environment information:

1. Install the tomcat package from Centos 7 with selinux-policy >= 3.13.1-145
2. Try using a port other than the default port, 8080:
`$ sed -i 's/ port=\"8080/ port=\"8090/' /etc/tomcat/server.xml`
3. Start tomcat
`$ systemctl start tomcat`
4. Check the log for a BindException:

```
$ grep -A1 BindException /var/log/tomcat/catalina.*.log | grep 8090 -A1
Caused by: java.net.BindException: Permission denied (Bind failed) :8090
    at org.apache.tomcat.util.net.JIoEndpoint.bind(JIoEndpoint.java:413)
```

The behavior noted above is due to a fix in the selinux-policy package; see [rhbz#1432083](https://bugzilla.redhat.com/show_bug.cgi?id=1432083) for more details.

If you check /var/log/audit/audit.log you'll see an AVC denial, such as:

```
type=AVC msg=audit(1513815897.006:136): avc:  denied  { name_bind} for  pid=1467 comm="java" src=8090scontext=system_u:system_r:tomcat_t:s0tcontext=system_u:object_r:unreserved_port_t:s0 tclass=tcp_socket
```

Previous versions of the selinux-policy package incorrectly labeled tomcat as `unconfined_t` so the process could do whatever it wanted. That problem has been address and now tomcat is confined by SELinux as it should be :)

You can fix the problem by adding the port you want to allow to the system's HTTP port type, `http_port_t` using semanage, like: `semanage port --add -t http_port_t -p tcp 8090`.

