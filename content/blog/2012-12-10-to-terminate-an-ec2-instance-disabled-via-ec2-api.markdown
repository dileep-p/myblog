---
author: vishnur66
comments: false
date: 2012-12-10 10:38:44+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/to-terminate-an-ec2-instance-disabled-via-ec2-api/
slug: to-terminate-an-ec2-instance-disabled-via-ec2-api
title: To Terminate an EC2 Instance, disabled via ec2-api
wordpress_id: 40
categories:
- Amazon EC2
tags:
- amazon
- cloud
- computing
- ec2
- linux
---

#**ec2minatt i-555555 –disable-api-termination false –region us-east-1 -K /EC2_API_Certs/pk-4GGV.pem -C /EC2_API_Certs/cert-GV.pem**

Make a note of the ebs-id attached

Terminate the Instance from the AWS Console and then Delete the EBS, same could be done through command line
