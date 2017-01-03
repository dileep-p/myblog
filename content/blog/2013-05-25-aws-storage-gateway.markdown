---
author: vishnur66
comments: false
date: 2013-05-25 04:26:10+00:00
layout: post
link: http://www.vishnu-tech.com/2013/05/aws-storage-gateway/
slug: aws-storage-gateway
title: AWS Storage Gateway
wordpress_id: 241
categories:
- Amazon AWS
---

[![intro](https://www.vishnu-tech.com/wp-content/uploads/2013/05/intro.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/05/intro.png)



**Using Amazon AWS Storage Gateway :**
AWS Storage Gateway is a service that connects an on-premises software appliance with cloud-based storage in the form of an iSCSI devices.
Storage Gateways Documentation – assume a scenario of Data Centre VM where we use S3 for storage solution.

There are 2 type of iSCSI devices:
**1. Gateway-Cached Volume Solution :**  create your storage volumes and mount them as iSCSI devices from your on-premises application servers – Data is stored on Amazon S3 and frequently accessed data is stored on the on-premises storage hardware.

**2. Gateway-Stored Volume Solution :** store all your data locally in storage volumes on your on-premises storage hardware. The gateway periodically takes snapshots as incremental backups and stores them in Amazon S3.

[[Youtube Video on StorageGateway walkthrough on Windows](http://www.youtube.com/watch?feature=player_embedded&v=Bb8nk0oWJbU#%21)]

AWS Storage Gateway uses two different hosting environments: VMware virtualization environment and an Amazon Elastic Compute Cloud (Amazon EC2) environment.

[VMware virtualization environment](http://docs.aws.amazon.com/storagegateway/latest/userguide/VMwareGateway.html)
[Amazon Elastic Compute Cloud (Amazon EC2) environment](http://docs.aws.amazon.com/storagegateway/latest/userguide/EC2Gateway.html)



[![working](https://www.vishnu-tech.com/wp-content/uploads/2013/05/working.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/05/working.png)

**Setting Up AWS Storage Gateway**

[Steps :](http://docs.aws.amazon.com/storagegateway/latest/userguide/LaunchingGatewayAMI.html)
Click Storage Gateway from the AWS Consol
Click – Deploy a New Gateway on Amazon EC2
Click – Lauch Gateway AMI
Click – Select
Select – Lauch with EC2 Console
Click Accept Terms
One of the following AMI can be choosen

Region    ID
US East (Virginia)                               ami-29f27a40
US West (Oregon)                               ami-4847cc78
US West (Northern California)          ami-36b39373
EU West (Ireland)                               ami-04393670
Asia Pacific (Singapore)                     ami-4a94d618
Asia Pacific (Tokyo)                            ami-d941fbd8
South America (Sao Paulo)                ami-6526fe78

The instance type must be at least a Standard XL (m1.large) or the instance will not launch.

With default setup – 2 more EBS also needs to be added,  one for cache storage and one for upload buffer.

NOTE: For a gateway-cached setup, you can add up to 18 TB of storage comprised of up to 2 TB allocated to upload buffer and up to 16 TB allocated to cache storage.
