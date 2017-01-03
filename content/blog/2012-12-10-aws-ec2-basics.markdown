---
author: vishnur66
comments: true
date: 2012-12-10 07:24:22+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/aws-ec2-basics/
slug: aws-ec2-basics
title: AWS EC2 Basics
wordpress_id: 14
categories:
- Amazon AWS
---

## EC2 (elastic cloud compute)


**Instance Stop**:



	
  * The instance will be shutdown and the virtual machine that was provisioned for you will be permanently taken away.

	
  * Will NO longer be charged for instance usage.

	
  * The attached bootable EBS volume will NOT be deleted.

	
  * All information on the local (ephemeral) hard drive will be lost.

	
  * Volume will continue to persist in its availability zone and standard charges for EBS volumes will apply.

	
  * Ability to stop an instance is only supported on instances that were launched using an EBS-based AMI where the root device data is stored on an attached EBS volume as an EBS boot partition instead of being stored on the local instance itself.

	
  * When you start a stopped instance the EBS volume is simply attached to the newly provisioned instance.  Although, the AWS-id of the new virtual machine will be the same, it will have new IP Addresses, DNS Names, etc.

	
  * Starting a stopped instance isnt same as Rebooting an instance because according to the former the Instance gets newly provisioned VM.



	
  * **Things that change when you stop/start include:**



	
  1. New internal IP address (though could randomly be the same).

	
  2. New external IP address (though could randomly be the same).

	
  3. If an Elastic IP address was associated with the instance before it was stopped, then you’ll need to re-associate it after the start. (Behavior may differ with VPC instances.)

	
  4. Any contents on the instance’s former ephemeral storage were wiped and you are given fresh ephemeral storage (often mounted as /mnt).

	
  5. You can leave an instance stopped for as long as you like and not get charged for run time (though you do get charged at a much lower rate for the EBS volume storage). See the next point.

	
  6. A fresh billing hour is started for the instance when you start it again. E.g., if you start a new instance and then stop/start it 3 times within the first 60 minutes, you’ll get charged for 4 hours instead of 1.

	
  7. There is a small chance that EC2 will not have available slots of the correct instance type to run your instance when you want to start it again. I’ve had this happen and temporarily switched to a different, available instance type to get it running again.


**Instance Termination**



	
  * The key difference between stopping and terminating an instance is that the attached bootable EBS volume will be deleted on Instance Termination.

	
  * EC2 instance will be shutdown and the virtual machine that was provisioned for you will be permanently taken away.

	
  * Will no longer be charged for instance usage.


**Instance Reboot**



	
  * When you reboot, it’s a simple reboot at the OS level and the instance stays running on the same hardware, with the same private and public IP addresses, keeps the same Elastic IP address (if associated), and keeps the same ephemeral storage without getting wiped. No new billing hour is started on a reboot and you do not give up the instance hardware.


**EBS Boot Vs Instance Store**

If you are just getting started with Amazon EC2, then use EBS boot instances and stop reading this article. Forget that you ever heard about instance-store and accept my apology that I just mentioned it. Once you are completely comfortable with using EBS boot instances on EC2, you may (or may not) want to come back here and read why you made a good decision.



	
  * Instance-store users don’t mind losing all of the data on the instance.


Benefits of EBS Boot Instances

	
  * EBS boot instances store the root file system on an EBS volume which is persistent storage. If the instance hardware fails, the EBS volume remains accessible. It is also possible to request the EBS volume to persist beyond the termination of an EC2 instance. _When an instance-store instance fails or is shut down, all of the data on the root disk is lost forever and can never be retrieved._ Read more about [protecting EC2 instances from accidental termination and loss of data](http://alestic.com/2010/01/ec2-instance-locking).

	
  * EBS boot instances can be stopped and restarted at will. The “stopped” state suspends the hourly instance billing charges, preserving the information on the EBS volumes. The stopped instance can be started again a few minutes later or months later, restoring state just as if the instance was rebooted. _An instance-store instance can only be left running with full charges or terminated, which causes you to lose all data on the disk._ Read more about [how stop/start of an EBS boot instance is similar to and different from a simple reboot](http://alestic.com/2011/09/ec2-reboot-stop-start).

	
  * When something goes wrong with an EBS boot instance so that it can’t be booted, or you lose access through ssh (e.g., lost keys, bad ssh config change), you can still view and modify or fix the EBS root volume by attaching it to another running instance. _With an instance-store instance, everything on the root disk is lost and cannot be recovered._ Read more about [fixing files on the root EBS volume of an EC2 instance](http://alestic.com/2011/02/ec2-fix-ebs-root).

	
  * EBS boot instances can be run with a root EBS volume size from the default specified by the AMI (often 8GB) up to 1,000GB (1TB). _The instance-store AMIs have a max root disk size of 10GB with no way to increase it._ Read more about [increasing the root disk size of an EBS boot AMI](http://alestic.com/2009/12/ec2-ebs-boot-resize).

	
  * It is possible to grow the size of the root disk after an EBS boot instance has been started. _An instance-store instance has no way to grow the root disk size._ Read more about [resizing the root disk on a running EBS boot EC2 instance](http://alestic.com/2010/02/ec2-resize-running-ebs-root).

	
  * It is possible to change the instance type for a running EBS boot instance without needing to start a new instance. For example, you can scale up from an m1.large to an m1.xlarge and then a few hours or days later, scale back down. _An instance-store instance is stuck with the type on which it was originally run._ Read more about [changing the instance type of a running EBS boot instance](http://alestic.com/2011/02/ec2-change-type).

	
  * You can easily replace the hardware for your instance if you are running an EBS boot instance. This is extremely valuable if your instance is having problems that you suspect may be related to the underlying hardware. _An instance-store instance is bound to the hardware it started on and cannot be moved._ Read more about [using stop/start to replace EC2 hardware](http://alestic.com/2011/02/ec2-move-hardware).

	
  * EBS boot AMIs are simpler and faster to create than instance-store AMIs. In fact, you can trigger the creation of an EBS boot AMI from a running instance in one command, API call, or console click. _You need to copy sensitive AWS credentials to the instance when creating an instance-store AMI._

	
  * Amazon has stated that EBS boot AMIs boot up faster than S3 based AMIs (instance-store). In my recent experience, the difference is negligible, especially when testing popular AMIs that are likely to be cached, but we might as well chalk this up as another benefit.

	
  * The t1.micro instance type released recently by Amazon only supports EBS boot instances. This move is like a sign from Amazon that you really don’t want to run the legacy instance-store instances.


Even if you use an EBS boot instance, I still recommend keeping your data on a separate EBS volume. This has a number of benefits.

**Changing Instance type(large or small), architecture (32bit or 64bit)**
There are 3 limitations that we need to keep in mind



	
  * This action can only performed on Instances that are in the stopped state.

	
  * It is not permitted to switch between 32 and 64 bit Instance types due to AMI limitations.


For example: Small Instances can only grow to Medium while Extra Large Instances can only shrink to Large.

**[Solved – “I can’t connect to my server on Amazon EC2″ by Alestic.](http://alestic.com/2009/08/ec2-connectivity)**

[http://alestic.com/2009/08/ec2-connectivity](http://alestic.com/2009/08/ec2-connectivity)

**[Convert Instance store to EBS backed Instance.](http://stackoverflow.com/questions/2082724/amazon-ec2-swap-root-instance-store-device-with-ebs-device)**

[http://stackoverflow.com/questions/2082724/amazon-ec2-swap-root-instance-store-device-with-ebs-device](http://stackoverflow.com/questions/2082724/amazon-ec2-swap-root-instance-store-device-with-ebs-device)
