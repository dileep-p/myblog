---
author: vishnur66
comments: true
date: 2012-12-10 10:40:06+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/install-ec2-api-tools-on-linux/
slug: install-ec2-api-tools-on-linux
title: Install ec2-api Tools on Linux
wordpress_id: 42
categories:
- Amazon EC2
tags:
- amazon
- cloud
- computing
- ec2
---

OS -** Ubuntu Lucid**
Processor **- x64**

[Download the ec2-api tools from the amazon site](http://aws.amazon.com/developertools/351)

[Downloaded jre1.7.0_x64 from the java site](http://www.oracle.com/technetwork/java/index.html)

Steps to install JAVA JRE
**#add-apt-repository ppa:sun-java-community-team/sun-java6**
**#apt-get update**
**#apt-cache search java* [to know the latest jre/jdk]**
**#apt-get install sun-java6-jre**
**java is installed into /usr/lib/jvm/java-6-sun-1.6.0.21/**

To check successful java installation
**#java -version**

Setting up variables
**#export JAVA_HOME=/usr/lib/jvm/java-6-sun-1.6.0.21**
[put it in /etc/profiles]

Another Check
**$JAVA_HOME/bin/java -version**

Now the prequisites are done . So comming back to the ec2-api tools
**#export EC2_HOME=/usr/local/ec2-api-tools-1.5.5.0**
[path where i unzipped it , also mention this in /etc/profile]

**#export PATH=$PATH:$EC2_HOME/bin**
[put it in /etc/profile too]

**#export EC2_PRIVATE_KEY=/EC2_API_Certs/pk-47O.pem**

**#export EC2_CERT=/EC2_API_Certs/cert-4GV.pem**
