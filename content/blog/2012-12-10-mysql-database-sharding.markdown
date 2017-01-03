---
author: vishnur66
comments: false
date: 2012-12-10 10:26:20+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/mysql-database-sharding/
slug: mysql-database-sharding
title: Mysql- Database Sharding
wordpress_id: 33
categories:
- Mysql
tags:
- amazon
- cloud
- computing
- ec2
- linux
- mysql
---

_**“share nonthing” : Key Law on database sharding Architecture.**_
_**Small Databases are Fast, Big Databases are Slow !!!**_
_**DB Sharding – Breaking a Bigger DB into a Smaller DB.**_

Key Points on DB Sharding –



	
  * Partition Data across master

	
  * Writes and read are distributed

	
  * Application needs modification

	
  * Needs choice partitioning strategy for uniform data distribution.


Issues -



	
  * Joins cannot be performed across shards

	
  * Application modification can be expensive.

	
  * Example : Evernote uses database sharding – localized failures , no need for joins. Each shards handles all  data &  traffic for about 100,000 users.


Sharding is another way to resolve MySQL scalability issues. It usually means splitting up the data by some logic derived from the application. This can be done by selecting a key in the data and splitting the data by hashing that key and having some distribution logic. It can also be done by identifying the application needs and setting different tables or different data sets in different databases (splitting the North-America sales data from the EMEA sales data, etc.)

This approach is simple from the database standpoint, but is very complex from the application standpoint since the application needs to be modified to deal with the data being scattered into the different shards. Moreover, combining data from different shards can be very complex and involves development in the application (you can’t just run a simple JOIN.)

Advantages of scaling out and in using sharding:

* Scales beyond the limitations of a single machine
* Scales both read and write operations (but makes some operations impossible to achieve in the database)
* Scales both throughput and capacity

Disadvantages of scaling out and in using sharding:

* Complex and requires application changes
* Scaling is usually offline and requires a re-partitioning event – and may require application changes.

Today, there are some solutions that introduce auto-sharding (Scalebase, Dbshards). This approach makes sharding more similar to shared-nothing partitioning, thus taking the sting out of some sharding complexities. However, it still requires application awareness and could prove to be a limiting factor if you needed to update your app or migrate to a different database solution.
