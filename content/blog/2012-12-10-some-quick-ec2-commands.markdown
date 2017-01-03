---
author: vishnur66
comments: true
date: 2012-12-10 07:19:00+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/some-quick-ec2-commands/
slug: some-quick-ec2-commands
title: Some Quick EC2 Commands
wordpress_id: 12
categories:
- Amazon AWS
---

NOTE- The -K and -C are REQUIRED parameters for all the ec2 commands.


## **#ec2-run-instances**


Launches a specified number of instances of an AMI for which you have permissions.

If you don’t specify a security group at launch time, the “default” security group is used.

Launching public images without a key pair ID will leave them inaccessible.

**Syntax:**

ec2-run-instances ami_id [-n instance_count] [-g group ] [-k
keypair] [--instance-type instance_type] [--availability-zone zone] [--block-device-mapping block_device_mapping] [--disable-api-termination] [--instance-initiated-shutdown-behavior behavior] [--private-ip-address ip_address]

Example1:

_#ec2-run-instances ami-d1cef5a5 –instance-type t1.micro –region eu-west-1 -k keypairname -K path/pk-Y.pem -C path/cert-O.pem_

**Useful Options**

–instance-type : m1.small | m1.medium | m1.large |m1.xlarge | c1.medium | c1.xlarge |  m2.xlarge| m2.2xlarge | m2.4xlarge | cc1.4xlarge |cg1.4xlarge | cc2.8xlarge | t1.micro

–block-device-mapping – Example:

_-b “/dev/sdb=snap-92d333fb::false”_

which means “[snapshot-id]:[size]:[delete-on-termination
(true|false)]“

–disable-api-termination : To disable the ability to terminate the instance using the EC2 API.(To re-enable this ability, you must change the _disableApiTermination_ attribute’s value to _false _using _ec2-modify-instance-attribute_.)

–instance-initiated-shutdown-behavior stop(or terminate) :  If an instance shutdown is initiated, this determines whether the instance stops or terminates.

–region REGION – to override the default region which is us-east-1 as mentioned in the environment variable EC2_URL.

Example2:

_#ec2-run-instances ami-d1cef5a5 –instance-type t1.micro –region eu-west-1 -k keypairname -K path/pk-Y.pem -C path/cert-O.pem –disable-api-termination -b “/dev/sdb=snap-92d333fb::false”  –instance-initiated-shutdown-behavior stop_


##  




## **#ec2-describes-regions**


output:

REGION    eu-west-1           ec2.eu-west-1.amazonaws.com
REGION    sa-east-1            ec2.sa-east-1.amazonaws.com
REGION    us-east-1            ec2.us-east-1.amazonaws.com
REGION    ap-northeast-1    ec2.ap-northeast-1.amazonaws.com
REGION    us-west-2           ec2.us-west-2.amazonaws.com
REGION    us-west-1           ec2.us-west-1.amazonaws.com
REGION    ap-southeast-1    ec2.ap-southeast-1.amazonaws.com




## #ec2kill


To Terminate an ec2 instance.

Example1:

_#ec2kill –region eu-west-1 i-abc12345_


## #ec2minatt


To modify an ec2 instance attribute.

Example1 – to set the disable-api-termination attribute to false:

_#ec2minatt i-abc123456 –disable-api-termination false –region eu-west-1_


_ _
