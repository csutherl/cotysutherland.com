+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2019-05-31T19:46:51Z
description = ""
draft = true
slug = "fuzzing-tomcat-with-afl"
tags = ["Apache Tomcat"]
title = "Fuzzing Tomcat With AFL"

+++


This whole thing started with me thinking about fuzzing tomcat-native with AFL. Fuzzing is a pretty new idea for me, but I have many friends suggesting it as a good way to find issues in libraries. AFL is super smart.

First attempt at fuzzing was a bust because of the way tomcat-native is designed. I can't fuzz it directly without writing a lot of code to glue the functions in tomcat-native together (which is what Tomcat does for you). Given that doing so would take a lot of time and effort, I decided to try and find a way to fuzz Tomcat (java) and stumbled upon https://github.com/isstac/kelinci. With this, I quickly whipped up a driver and was able to get started!

My first fuzzing attempt with Tomcat was to try and bypass client auth with a malformed client cert. To do this, I created a self signed cert and added it to the keystore and truststore. After that I configured tomcat's connector using tomcat embedded so that I could easily start/request/stop it programitcally without all the overhead the initial configuration of vanilla tomcat provides. This was neat and all, but after 500k iterations I determined that the only thing i was testing was how the JDK handled malformed jks stores :(

From there I moved on to other attempts.

You can check out the code for my driver, here!

