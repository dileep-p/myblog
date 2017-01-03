---
author: vishnur66
comments: true
date: 2012-12-10 10:23:24+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/install-use-s3cmd-for-s3-storage/
slug: install-use-s3cmd-for-s3-storage
title: Install & Use s3cmd for S3 Storage
wordpress_id: 28
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

Amazon S3 is a reasonably priced data storage service. Ideal for off-site backups, archiving and other data storage needs. It is generally more reliable than your regular web hosting for storing your files and images. Check out About Amazon S3 section to find out more.

S3cmd is a command line tool for uploading, retrieving and managing data in Amazon S3. It is best suited for power users who don’t fear command line. It is also ideal for scripts, automated backups triggered from cron, etc.

S3cmd is an open source project available under GNU Public License v2 (GPLv2) and is free for both commercial and private use. You will only have to pay Amazon for using their storage. None of these money go to S3cmd developers.

**#apt-get install s3cmd**

**To configure s3cmd**
#s3cmd –configure
[Enter Access Key and Secret Key]

**Configuration file is saved into**
/root/.s3cfg

**To get Help**
#s3cmd –help

**To List Buckets**
#s3cmd ls

**To Delete Non-Empty Buckets**
#s3cmd rb s3://buckt_name -fv

**Copy buckets to local machine**
#s3cmd get s3://buckt_name -r

**Create Buckets**
#s3cmd mb s3://buckt_name

**Syncing local dir with s3 Buckets**
#s3cmd sync local_dir/ s3://buckt_name

