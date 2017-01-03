---
author: vishnur66
comments: false
date: 2012-12-10 10:17:11+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/rds-sharding-quick-commands/
slug: rds-sharding-quick-commands
title: RDS Sharding & Quick Commands
wordpress_id: 21
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

[Source article on RDS sharding by Amazon.](http://aws.amazon.com/articles/0040302286264415#virtualshards)

[http://aws.amazon.com/articles/0040302286264415#virtualshards](http://aws.amazon.com/articles/0040302286264415#virtualshards)

**[What is sharding?](http://searchcloudcomputing.techtarget.com/definition/sharding)**

Here unlike ec2 instances there is no rds-db-instance-id instead we use the db-instance names (called as dbinstance_identifiers) in our rds commmands to identify among rds-db-instances.

Example-
Database Name – news
RDS DB Instances – shard1, shard2

Consider creating 2 db-shards ie we need 2 db-instance to map those db-shards to db-instances. To reduce the schema setup overhead we use the snapshot and restore capabilities of Amazon RDS to do the following:

1. Create a single “seed” DB Instance.
2. Set up the schema on that database.
3. Snapshot the database.
4. Create more databases from that snapshot using the RDS RestoreDBInstanceFromSnapshot API.

**1.Create SEED DATABASE INSTANCE**

_#rds-create-db-instance shard1 –engine mysql5.1 –master-username unni –master-user-password 123 –allocated-storage 5 –db-instance-class db.t1.micro –db-name news –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

$$output$$
DBINSTANCE  shard1  db.t1.micro  mysql  5  unni  creating  1  ****  n  5.1.63  general-public-license
SECGROUP  default  active
PARAMGRP  default.mysql5.1  in-sync
OPTIONGROUP  default:mysql-5-1  in-sync

Add IP address to DB Security Group to gain access
1.Find IP from www.whatismyipaddress.com – say its – 122.174.199.204
2.Add the CIDR – 122.174.199.204/32 in the DB-Security Group
3.Try gaining access to rds by – #mysql -h shard1.covaztmm6tup.eu-west-1.rds.amazonaws.com -u unni -p123

**2.SETUP SCHEMA ON THE DB**

mysql>show database;
mysql>use news;
mysql>CREATE TABLE articles (
article_id numeric(64,0) NOT NULL PRIMARY KEY,
category char(13),
CHECK category IN (‘BUSINESS’, ‘ENTERTAINMENT’, ‘HEALTH’, ‘SCIENCE’, ‘SPORTS’, ‘TECHNOLOGY’, ‘WORLD’),
title char(128),
submit_time timestamp
);

**3.SNAPSHOT and CREATE OTHER DB INSTANCES**

_#rds-create-db-snapshot shard1 –db-snapshot-identifier news-seed-database –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

$$output$$
DBSNAPSHOT  news-seed-database  shard1  2012-09-04T12:02:30.129Z  mysql  5  creating  unni  5.1.63  general-public-license  manual

Once the snapshot is available it can be used to create any number of DB Instances. We’re only creating two DB Instances in this example, but it could easily be many more. Also, for our trivial setup, the only efficiency we gain is that we don’t need to create the schema on each. However, in a typical scenario there might be a lot more setup involved in creating the seed (e.g., application configuration data, user accounts, permissions, etc.).

**4.DB-INSTANCE FROM DB-SNAPSHOT**

The following command can be used to create new DB Instances from the seed snapshot:

_#rds-restore-db-instance-from-db-snapshot shard2 –db-snapshot-identifier news-seed-database –db-instance-class db.t1.micro –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

$$output$$
DBINSTANCE  shard2  db.t1.micro  mysql  5  unni  creating  1  n  5.1.63  general-public-license
SECGROUP  default  active
PARAMGRP  default.mysql5.1  in-sync
OPTIONGROUP  default:mysql-5-1  pending

—————–

To LIST RDS-INSTANCES
_#rds-describe-db-instances –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

TO DELETE RDS-INSTANCE with FINAL SNAPSHOT
_#rds-delete-db-instance shard1 –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem –final-db-snapshot-identifier shard-final-snapshot_

TO DELETE RDS-INSTANCE without FINAL SNAPSHOT
_#rds-delete-db-instance shard2 –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem –skip-final-snapshot_

TO LIST RDS-DB-SNAPSHOTS
_#rds-describe-db-snapshots –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

TO DELETE RDS-DB-SNAPSHOTS
_#rds-delete-db-snapshot shard-final-snapshot –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

NOTE
Creating a final snapshot (before dbinstance termination) incurs additional storage fees.
