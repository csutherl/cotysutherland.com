+++
title = "Open Source Projects and Contributions"
slug = "projects"
layout = "single"
type = "page"
showDate = false
showReadingTime = false
showWordCount = false
showEdit = false
showAuthor = false
+++

# Projects

Most of my work centers around **open source projects** and I’m fortunate to have a role that lets me contribute directly to those communities as part of my day job. Below are a few of the projects I actively work on or maintain. I’ve also contributed to others over the years, including **JBoss Web**, **mod_cluster**, **Apache HTTP Server (httpd)**, and **OpenSSL**.

I also enjoy being a webmaster as a hobby and maintain a few sites for nonprofit organizations.

---

{{< projectheader
  title="Fedora Project Maintainer"
  logo="https://upload.wikimedia.org/wikipedia/commons/3/3f/Fedora_logo.svg"
  repoUrl="https://src.fedoraproject.org/rpms/tomcat"
  buildstatus="false"
  stars="false"
>}}

I’ve maintained the **Tomcat package for Fedora** since 2015 and co-maintain the **tomcat-native** package as well. These packages adapt upstream releases for system administrators by adding packaging logic, systemd integration, and platform-specific patches.

- **Languages:** RPM spec, Bash, Java

---

{{< projectheader
  title="Apache Tomcat"
  logo="https://tomcat.apache.org/res/images/tomcat.png"
  repo="apache/tomcat"
  build="https://img.shields.io/github/actions/workflow/status/apache/tomcat/ci.yml?branch=main"
>}}


[Apache Tomcat](https://tomcat.apache.org) is an open source implementation of the Java Servlet, JavaServer Pages, Java Expression Language, and Java WebSocket specifications. I’ve been a committer since **2016**, a **Project Management Committee (PMC)** member since **2017**, and am part of the **Tomcat Security Team**.

- **Languages**: Java, C (tomcat-native, mod_jk)
- [🔗 Visit Project Website](https://tomcat.apache.org/)

---

{{< projectheader
  title="Vault Extension for Apache Tomcat"
  repo="web-servers/tomcat-vault"
  buildstats="false"
>}}

The Vault Extension for Apache Tomcat library is a **PicketLink Vault** extension that lets you store sensitive information (like passwords) outside of Tomcat configuration files. This component is used in the product I support at Red Hat, so I’ve contributed actively to maintaining and improving it.

- **Languages:** Java

---
