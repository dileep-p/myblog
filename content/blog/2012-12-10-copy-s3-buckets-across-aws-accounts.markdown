---
author: vishnur66
comments: true
date: 2012-12-10 07:15:54+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/copy-s3-buckets-across-aws-accounts/
slug: copy-s3-buckets-across-aws-accounts
title: Copy S3 Buckets across AWS accounts
wordpress_id: 6
categories:
- Amazon AWS
---

Amazon S3 bucket names are UNIQUE accross all AWS Accounts.
For example, suppose your first account username is acc1@gmail.com and second is acc2@gmail.com.

**#s3cmd –configure : Configure for acc1 aws account**

and create similar bucket (not same bucket name) in the acc2 account and set those bucket permissions to (Gurantee=)Everyone – (Tick)Upload/Delete.

Then you can use s3cmd (using the credentials of the acc1) to do something like:

**s3cmd cp s3://acc1_bucket/folder/ s3://acc2_bucket/folder -r**

All transfer will be done on Amazon’s side.
