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

## Apache Tomcat

[![Apache Tomcat](https://tomcat.apache.org/res/images/tomcat.png)](https://tomcat.apache.org)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://ci.apache.org/projects/tomcat.html)
[![GitHub Stars](https://img.shields.io/github/stars/apache/tomcat?style=social)](https://github.com/apache/tomcat)
[![Source on GitHub](https://img.shields.io/badge/source-github-blue)](https://github.com/apache/tomcat)

[Apache Tomcat](https://tomcat.apache.org) is an open source implementation of the Java Servlet, JavaServer Pages, Java Expression Language, and Java WebSocket specifications. I’ve been a **committer since 2016**, a **Project Management Committee (PMC) member since 2017**, and am part of the **Tomcat Security Team**.

- **Languages:** Java, C (`tomcat-native`, `mod_jk`)
- 🔗 [Visit Project](https://tomcat.apache.org)

---

## Embedded Tomcat Quickstarts

[![GitHub Repo](https://img.shields.io/github/stars/web-servers/tomcat-embedded-quickstarts?style=social)](https://github.com/web-servers/tomcat-embedded-quickstarts)
[![Source on GitHub](https://img.shields.io/badge/source-github-blue)](https://github.com/web-servers/tomcat-embedded-quickstarts)

As the use of **Embedded Tomcat** has grown, I noticed a gap in the available reference material. I created this project to offer quickstart examples showing how to use Embedded Tomcat — including both a **vanilla** implementation and a **Spring Boot** version.

- **Languages:** Java

---

## Vault Extension for Apache Tomcat

[![GitHub Repo](https://img.shields.io/github/stars/web-servers/tomcat-vault?style=social)](https://github.com/web-servers/tomcat-vault)
[![Source on GitHub](https://img.shields.io/badge/source-github-blue)](https://github.com/web-servers/tomcat-vault)

The `tomcat-vault` library is a **PicketLink Vault** extension that lets you store sensitive information (like passwords) outside of Tomcat configuration files. This component is used in the product I support at Red Hat, so I’ve contributed actively to maintaining and improving it.

- **Languages:** Java

---

## Fedora Package Maintainer

[![Fedora](https://upload.wikimedia.org/wikipedia/commons/3/3f/Fedora_logo.svg)](https://src.fedoraproject.org/rpms/tomcat)
[![GitHub Repo](https://img.shields.io/badge/source-fedora%20pagure-blue)](https://src.fedoraproject.org/rpms/tomcat)

I’ve maintained the **Tomcat package for Fedora** since 2015 and co-maintain the **tomcat-native** package as well. These packages adapt upstream releases for system administrators by adding packaging logic, `systemd` integration, and platform-specific patches.

- **Languages:** RPM spec, Bash
