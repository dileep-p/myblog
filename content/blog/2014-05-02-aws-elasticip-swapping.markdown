---
author: vishnur66
comments: false
date: 2014-05-02 19:06:30+00:00
layout: post
link: http://www.vishnu-tech.com/2014/05/aws-elasticip-swapping/
slug: aws-elasticip-swapping
title: AWS-ElasticIP-Swapping
wordpress_id: 291
categories:
- Amazon AWS
- Amazon EC2
- Amazon S3
- Git
tags:
- AWS
- elastic ip
---

# Attach and Detach Public IP in AWS


_This script is used for detach elastic ip from one server and attach it to the secondary private ip of the other server._

For example:-

We have two servers with same content named "server01" and "server02" with primary and secondary private IP in AWS and each instance have a public IP  (ie. Elastic IP). This two public IP's are pointed to the DNS.

If "server01" goes down, only you need to detach the elastic IP and attached it to the "server02" to the secondary private IP.

You can find the script in the below link:-

[Elastic-IP-Swap](https://github.com/vishnunamshi/ElasticIP-Swap)










