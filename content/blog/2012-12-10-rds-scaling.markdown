---
author: vishnur66
comments: false
date: 2012-12-10 07:29:43+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/rds-scaling/
slug: rds-scaling
title: RDS Scaling
wordpress_id: 18
categories:
- Amazon AWS
tags:
- amazon
- amazon s3
- cloud
- computing
- ec2
- linux
- mysql
- RDS
- s3cmd
---

If your app gets to the point that you need to start scaling either up or out, it is a good idea to switch to multi-AZ if you don’t run it already. If you have a simple RDS instance, you will degrade your service significantly while scaling, as you can expect to lose the ability to write and/or read. With multi-AZ RDS instances, your service is almost uninterrupted.

**Scaling Up (or Down)**

Scaling up is so easy it is almost ridiculous. The only drawback is that you have some downtime during the operation. If you don’t have multi-AZ enabled, the downtime of your RDS instance could be several minutes, as you have to wait until a new instance is launched and fully functional. For multi-AZ RDS instances, you will experience some downtime as a failover is initiated after the slave has been scaled up (or down). This failover doesn’t take more than a minute most of the time.

If you initiate a scaling activity via the Console, make sure you enable Apply Immediately if you are in a hurry. If you don’t, scaling will take place during the scheduled maintenance period

Scaling using the command-line tools is a two-step process. First scale, and then reboot:

$ rds-modify-db-instance production –db-instance-class db.m1.xlarge –apply-immediately
$ rds-reboot-db-instance production

**Scaling Out**

You can scale out a relational database in two different ways:

* Using read-only slaves (read replicas in AWS)
* Sharding or partitioning

There are still some hard problems to solve, as sharding/partitioning has not been addressed yet with RDS. Master-slave type scaling is available, though. A slave, or read replica, is easily created from the Console. The only requirement on the master RDS instance is that backups are not disabled by setting the backup retention period to 0. Currently, you can have up to five read replicas that you have to launch one by one. Amazon is working on the ability to launch multiple replicas at once, but that is not yet available.

On a multi-AZ RDS instance, launching a read replica goes unnoticed. A snapshot is taken from the standby, the replica is launched, and when it is ready, it starts to catch up with the master. For a normal RDS instance, there is a brief I/O suspension in the order of one minute. AWS advises to use the same instance classes, as differing classes may incur replica lag. With read replicas, you basically introduce eventual consistency in your database (cluster).
