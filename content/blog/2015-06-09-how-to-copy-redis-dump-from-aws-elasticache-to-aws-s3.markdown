---
author: vishnur66
comments: true
date: 2015-06-09 15:22:13+00:00
layout: post
link: http://www.vishnu-tech.com/2015/06/how-to-copy-redis-dump-from-aws-elasticache-to-aws-s3/
slug: how-to-copy-redis-dump-from-aws-elasticache-to-aws-s3
title: How to copy redis dump from AWS Elasticache to AWS S3
wordpress_id: 492
categories:
- Amazon AWS
- Amazon EC2
- Amazon S3
tags:
- amazon s3
- AWS
- aws ec2
- aws s3
- elasticache
- redis
---

For Amazon Elasticache, we can't copy the snapshot to AWS S3.  So inorder to do that you need to do the below steps:-



	
  * Spin up new EC2 instance.

	
  *  Install Redis on that Instance.

	
  *  Setup the instance as a read replica of the AWS Elasticache Primary (redis)

	
  *  Wait for the Master Slave data sync

	
  *  Issue a redis **SAVE **command to generate a local dump

	
  *  Copy local dump.rdb on AWS S3


You can do the whole setup by simply checkout this _**[REPO](https://github.com/vishnudxb/redisbackup-to-s3)**_  and run the below command

[color-box color="green"]

_**vishnudxb@server:~# ./terraform apply -var 'access_key=PUTMYACCESSKEY'  \**_

_**                                                              -var 'secret_key=PUTMYSECRETKEY'  \**_

_**                                                              -var 'key_file=/home/redis.pem'  \**_

_**                                                              -var 'key_name=redis' \**_

_**                                                              -var 'region=us-east-1' \**_

_**                                                              -var 'instance_type=m3.large' \**_

_**                                                              -var 'availability_zone=us-east-1a' \ **_

_**                                                              -var 'subnet_id=subnet-e94xxxx'  \**_

_**                                                              -var 'security_id=sg-7xxxx1d' \**_

_**                                                             -var 'redis_endpoint=aws redis endpoint'  \**_

_**                                                              -var 'redis_port=6379' \**_

_**                                                              -var 'aws_bucket=redis-db-backup'**_

[/color-box]
Once you execute the above command and you can see the output like the below:-

[![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/06/redis.png)](https://www.vishnu-tech.com/wp-content/uploads/2015/06/redis.png)




