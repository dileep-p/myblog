---
author: vishnur66
comments: false
date: 2013-05-25 04:11:02+00:00
layout: post
link: http://www.vishnu-tech.com/2013/05/s3cmd-elaborated/
slug: s3cmd-elaborated
title: s3cmd Elaborated…
wordpress_id: 233
categories:
- Amazon S3
tags:
- amazon s3
- s3cmd
---

**Use –rr option (reduced redundancy) for every put and sync commands !!!. **
**Use –bucket-location option to mention nearest geographical location to avoid latency.**

**To view contents inside a bucket**
#s3cmd ls s3://bucketname

**To copy/sync a directory into a bucket**
#s3cmd sync Desktop/check s3://bucket_name

**To view all contents of all buckets one level down (only non empty buckets)**
#s3cmd la -H

**To sync contents of a local dir in a buckter under an existing directory (s3 object)**
#s3cmd sync Desktop/checkunni/ s3://writingz/check/

**To sync remote s3 contents to a local directory**
#s3cmd sync s3://writingz/check/ Desktop/checkunni/

**To sync contents of a local dir in a bucket under a new directory name**
#s3cmd sync Desktop/checkunni/ s3://homie/newname/
Here newname directory is created on the fly and files of checkunni are copied inside s3://homie/newname

**Copy a non-empty directory (on s3) from one bucket to another bucket**
#s3cmd -r cp s3://homie/newname s3://writingz/

**Copy a non-empty directory (on s3) from one bucket to another bucket under a new name**
#s3cmd -r cp s3://homie/newname s3://writingz/newname2/

**To find the size of a bucket/directory**
#s3cmd du -H s3://writingz

**To download only a single file**
#s3cmd get s3://homie/dirname/filename .

**To download a remote directory locally**.
#s3cmd get -rf s3://writingz/checkunni .
use a / (forward slash) after checkunni to download only the files in it.

**To upload a single file**
#s3cmd put PSY.mp3 s3://homie/newname/

**To upload a local dir to bucket**
#s3cmd put -rf s3test s3://homie/newname/

**Delete a file**
#s3cmd del s3://writingz/abc.jpg

**Delete a directory**
#s3cmd del -rf s3://writingz/check/

**Move a file **(can also be used for rename with files only)****
#s3cmd mv s3://writingz/abc.png s3://haye/

**Move a directory to another bucket **
#s3cmd mv -rf s3://writingz/newname2 s3://haye/

**Know the s3cmd version**
#s3cmd –version

**Make a file public using**
#s3cmd put –acl-public hangover3.jpg s3://viewzz/abc.jpg

**Make a file private using**
#s3cmd setacl –acl-private s3://viewzz/hangover3.jpg

**Set all files in a bucket to public/private**
#s3cmd setacl –acl-public -r s3://writingz/

**If an md5 checksum is need to verify files integrity use**
#sudo s3cmd info s3://viewzz/hangover3.jpg (an amazon s3 object)
#md5sum hangover3.jpg (locally downloaded file from s3)
and compare the checksum value.

**To delete a bucket (bucket has to be empty use s3cmd del – to delete all files)**
#s3cmd rb s3://logix.cz-test (use -f option if bucket is non-empty)

**Get various information about Buckets or Files**
#s3cmd info s3://BUCKET[/OBJECT]

**Other useful options**
**–delete-removed** Delete remote objects with no corresponding local file
[sync]

**–no-delete-removed** Don’t delete remote objects.
**–skip-existing** Skip over files that exist at the destination (only
for [get] and [sync] commands).

**–continue** Continue getting a partially downloaded file (only for
[get] command).

**–reduced-redundancy, –rr**
Store object with ‘Reduced redundancy’. Lower per-GB
price. [put, cp, mv, sync]

**–acl-public** Store objects with ACL allowing read for anyone.

**–acl-private** Store objects with default ACL allowing access for you

only.

**–bucket-location=BUCKET_LOCATION **Datacentre to create bucket in. Eg :  ap-northeast-1  (Tokyo)

The ACL (Access Control List) of a file can be set at the time of upload using –acl-public or –acl-private options with ‘s3cmd put’ or s3cmd sync’ commands (see below).

Alternatively the ACL can be altered for existing remote files with ‘s3cmd setacl –acl-public’ (or –acl-private) command.

**Additional Links on **



	
  * [Store S3 objects to Glacier.](http://www.raghuramanb.com/2013/01/aws-log-archive-amazon-s3-glacier-part-4.html)

	
  * [Consider the costs Glacier could incur during the Transition from S3.](http://alestic.com/2012/12/s3-glacier-costs)


