---
author: vishnur66
comments: true
date: 2012-12-10 10:21:57+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/aws-productssolutions-admins-capsule/
slug: aws-productssolutions-admins-capsule
title: AWS Products/Solutions – Admins Capsule
wordpress_id: 26
categories:
- Amazon AWS
tags:
- amazon
- amazon s3
- cloud
- computing
- ec2
- linux
- s3cmd
---

# Database


**[Amazon RDS](http://aws.amazon.com/running_databases/#rds)**



	
  * You can think of a [RDS](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Welcome.html) DB Instance as a database environment in the cloud with the compute and storage resources you specify.

	
  * You can create and delete DB Instances, define/refine infrastructure attributes of your DB Instance(s), and control access and security via the [AWS Management Console](https://console.aws.amazon.com/), Amazon RDS APIs, and Command Line Tools.

	
  * Multiple MySQL databases or SQL Server databases (up to 30) or Oracle database schemas can be created on a given DB Instance.

	
  * For optional [Multi-AZ deployments](http://aws.amazon.com/rds/faqs/#36) (currently supported for MySQL and Oracle database engines), Amazon RDS also manages synchronous data replication across Availability Zones and automatic failover.

	
  * [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)

	
  * By default, customers are allowed to have up to a total of 20 Amazon RDS DB instances.

	
  * [RDS cannot remove storage once it has been allocated. The only way to reduce the amount of storage allocated to a DB Instance is to dump the data out of the DB Instance, create a new DB Instance with less storage space, and load the data into the new DB Instance.](http://aws.amazon.com/articles/2936?_encoding=UTF8&jiveRedirect=1)

	
  * Unlike Multi-AZ deployments, Read Replicas use MySQL’s built-in replication and are subject to its strengths and limitations.This means recent database updates made to a standard (non Multi-AZ) source DB Instance may not be present on associated Read Replicas in the event of an unplanned outage on the source DB Instance. As such, Read Replicas do not offer the same data durability benefits as Multi-AZ deployments. While Read Replicas can provide some read availability benefits, they and are not designed to improve write availability. Read Replicas are currently supported for Amazon RDS for MySQL. They can also be used for serving read traffic when the primary database is unavailable.

	
  * The read replica mechanism uses MySQL’s native, asynchronous replication. This means replicas might be lagging behind the master as they try to catch up with writes. The interesting thing about this is that multi-AZ RDS instances apparently use another,_ proprietary type_ of synchronous replication.

	
  * A Read Replica will stay active and continue accepting read traffic even after its corresponding source DB Instance has been deleted. If you desire to delete the Read Replica in addition to the source DB Instance, you must explicitly delete the Read Replica using the DeleteDBInstance API or AWS Management Console.

	
  * By default and at no additional charge, Amazon RDS enables automated backups of your DB Instance with a 1 day retention period.

	
  * During the backup window, storage I/O may be suspended while your data is being backed up. This I/O suspension typically lasts a few minutes at most. This I/O suspension is avoided with Multi-AZ DB deployments, since the backup is taken from the standby.

	
  * Amazon RDS DB snapshots and automated backups are stored in S3.

	
  * If you desire to turn off automated backups altogether, you can do so by setting the retention period to 0 (not recommended).

	
  * When you delete a DB Instance, you have the ability to specify whether a final DB Snapshot is created upon deletion, which enables a DB Snapshot restore of the deleted database instance at a later date. All previously created DB Snapshots of your DB Instance will be retained and billed at $0.15 per GB-month, unless you choose to delete them.

	
  * Amazon RDS does not currently provide access to the binary logs for your Database Instance.

	
  * You are not charged for the data transfer incurred in replicating data between your source DB Instance and Read Replica. Billing for a Read Replica begins as soon as the Read Replica has been successfully created (i.e. when status is listed as “active”). The Read Replica will continue being billed at standard Amazon RDS DB Instance hour rates until you issue a command to delete it.

	
  * Amazon RDS primarily has 3 engines – [Mysql Database Engine](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Concepts.DBEngine.MySQL51.html), [Oracle Database Engine](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Concepts.DBEngine.Oracle.html),[Microsoft SQL Server Database Engine](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Concepts.DBEngine.SQLServer.html).

	
  * [Setup RDS CLI.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/StartCLI.html)

	
  * [RDS Terminology and Concepts.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Concepts.DBInstance.html)

	
  * [How to Connect to RDS – MySQL.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html)

	
  * [RDS CLI – References](http://docs.amazonwebservices.com/AmazonRDS/latest/CommandLineReference/command-reference.html).

	
  * [Creating and Modifying  DB Instance.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Scenarios.InstanceBasics.html)

	
  * [Backing UP and Restoring DB Instances.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/Scenarios.Backups.html)

	
  * [Viewing RDS Instance events.](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_ListEvents.html)

	
  * working with  [DB Parameter Groups](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html), [security groups](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_WorkingWithSecurityGroups.html), [option groups](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_WorkingWithOptionGroups.html) & [viewing DB instance metrics](http://docs.amazonwebservices.com/AmazonRDS/latest/UserGuide/USER_Monitoring.html).

	
  * [RDS Security Best Practices !!!](https://forums.aws.amazon.com/thread.jspa?messageID=161973&#161973)

	
  * [Tech Tips – Scaling Databases with Amazon RDS](https://forums.aws.amazon.com/thread.jspa?messageID=180752&#180752)

	
  * [Tech Tips – On Demand Test Databases](https://forums.aws.amazon.com/thread.jspa?messageID=160936&#160936)

	
  * [Tech Tips IV: Best Practices to Avoid an Inoperable RDS MySQL DB Instance](https://forums.aws.amazon.com/thread.jspa?messageID=198765&#198765)

	
  * [Tech Tips V: Defining CloudWatch alarms for Amazon RDS metrics](https://forums.aws.amazon.com/thread.jspa?threadID=56871&tstart=0)

	
  * The default storage engine with RDS is InnoDB, but you are free to choose another, like the popular MyISAM. It is important to realize that read replicas on nontransactional storage engines (like MyISAM) require you to freeze your databases, as the consistency cannot be guaranteed when snapshotting. But if you use InnoDB, you are safe, and the only thing you have to do is fire up a new read replica.

	
  * RDS storage is independent of RDS instance classes. Every class can have from 5 GB to 1 TB of storage associated. Scaling up the storage is easy, and you can do it using the Console. It does require a reboot. On the other hand, scaling down the storage is impossible.

	
  * [Reserved DB Instances page](http://aws.amazon.com/rds/reserved-instances/).

	
  * [On-Demand DB Instances.](http://aws.amazon.com/rds/)

	
  * [RDS CLI](http://aws.amazon.com/developertools/2928/)

	
  * [10 things you should know about RDS.](http://blog.webyog.com/2009/11/16/top-10-things-to-know-about-amazon-rds/)


**[Amazon DynamoDB](http://aws.amazon.com/running_databases/#dynamodb)**



	
  * A fast, highly scalable NoSQL database service

	
  * A fully managed service that offers extremely fast performance, seamless scalability and reliability, low cost and more.

	
  * [Video](http://www.youtube.com/watch?v=oz-7wJJ9HZ0)


**[Amazon SimpleDB](http://aws.amazon.com/running_databases/#simpledb)**



	
  * A NoSQL database service for smaller datasets.

	
  * A fully managed service that provides a schemaless database, reliability and more.


**[Your choice of relational AMIs](http://aws.amazon.com/running_databases/#relational_amis)**



	
  * A relational database you can manage on your own.

	
  * On Amazon EC2 and EBS that provide scale compute & storage, complete control over instances, and more.


**[Amazon ElastiCache](http://aws.amazon.com/elasticache/)**



	
  * Amazon ElastiCache is a web service that makes it easy to deploy, operate, and scale an in-memory cache in the cloud.

	
  * [Amazon ElasticCache CLI Tool.](http://aws.amazon.com/developertools/2310261897259567)




# 




# Compute


**[Amazon Elastic Cloud Compute (EC2)](http://aws.amazon.com/ec2/)**



	
  * Amazon Elastic Compute Cloud delivers scalable, pay-as-you-go compute capacity in the cloud.

	
  * [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types)

	
  * EBS-Optimized instances are for selected types only such as – Standard Instances(Large,Extra Large), High-Memory Instances(High-Memory Quadruple Extra Large).

	
  * Amazon EC2 instances are grouped into seven families: Standard, Micro, High-Memory, High-CPU, Cluster Compute, Cluster GPU, and High I/O.

	
  * [Amazon EC2 API (CLI Tools)](http://aws.amazon.com/developertools/351) and [how to setup](http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/SettingUp_CommandLine.html) it.

	
  * [Simple CLI Access to Amazon EC2 and S3](http://aws.amazon.com/developertools/739)


**[Amazon Elastic MapReduce](http://aws.amazon.com/elasticmapreduce/)**



	
  * Amazon Elastic MapReduce is a web service that enables businesses, researchers, data analysts, and developers to easily and cost-effectively process vast amounts of data.


**[Auto Scaling](http://aws.amazon.com/autoscaling/)**



	
  * Auto Scaling allows you to automatically scale your Amazon EC2 capacity up or down according to conditions you define.

	
  * [Auto Scaling CLI Tool.](http://aws.amazon.com/developertools/2535)




# 




# Networking


[**Elastic Load Balancing**](http://aws.amazon.com/elasticloadbalancing/)



	
  * [ELB API Tools](http://aws.amazon.com/developertools/2536).

	
  * Elastic Load Balancing automatically distributes incoming application traffic across multiple Amazon EC2 instances.


