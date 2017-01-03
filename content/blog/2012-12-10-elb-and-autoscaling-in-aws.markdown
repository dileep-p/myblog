---
author: vishnur66
comments: false
date: 2012-12-10 07:27:56+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/elb-and-autoscaling-in-aws/
slug: elb-and-autoscaling-in-aws
title: ELB and Autoscaling in AWS
wordpress_id: 16
categories:
- Amazon AWS
tags:
- amazon
- amazon s3
- cloud
- computing
- ec2
- ELB
- linux
- load balancing
- mysql
- RDS
- s3cmd
---

**Installing ELB and Autoscale API tools**

export AWS_ELB_HOME=/home/users/unni/bashrc/elbtool/ElasticLoadBalancing-1.0.12.0
export PATH=$PATH:$AWS_ELB_HOME/bin
export AWS_AUTO_SCALING_HOME=/home/users/unni/bashrc/auto/AutoScaling-1.0.33.1
export PATH=$PATH:$AWS_AUTO_SCALING_HOME/bin




# AUTOSCALING


Create AS, Create AS Group and Setup Scaleup Parameter.

#as-create-launch-config as-agile –region eu-west-1 –image-id ami-b123456 –instance-type t1.micro –key elbtest –group default -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem

OK-Created launch config

_#as-create-auto-scaling-group as-agile-gp –launch-configuration as-agile –region eu-west-1 –availability-zones eu-west-1a,eu-west-1b –min-size 2 –max-size 3 –load-balancers elbtest-agile -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

_OK-Created AutoScalingGroup_

_#as-create-or-update-trigger awsauto-nixontr –auto-scaling-group awsauto-nixongp –namespace “AWS/EC2″ –measure CPUUtilization –statistic Average –dimensions “AutoScalingGroupName=awsauto-nixongp” –period 60 –lower-threshold 40 –upper-threshold 80 –lower-breach-increment”=-1″ –upper-breach-increment 1 –breach-duration 1200 -K pk-XN.pem -C cert-XN.pem_

_OK-Created/Updated trigger_


# 




# ELASTIC LOAD BALANCER


**Create n Configure ELB**

_#elb-create-lb elbtest-agile –region eu-west-1 –availability-zones eu-west-1a,eu-west-1b –headers –listener “lb-port=80,instance-port=80,protocol=http” –listener “lb-port=443,instance-port=443,protocol=tcp” -K ec2_certs/pk-KO.pem -C ec2_certs/cert-K4.pem_

_DNS_NAME DNS_NAME_
_DNS_NAME elbtest-agile-1809496912.eu-west-1.elb.amazonaws.com_

_#elb-configure-healthcheck elbtest-agile –region eu-west-1 –headers –target “HTTP:80/index.html” –interval 5 –timeout 3 –unhealthy-threshold 2 –healthy-threshold 2 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

_HEALTH_CHECK TARGET INTERVAL TIMEOUT HEALTHY_THRESHOLD UNHEALTHY_THRESHOLD_
_HEALTH_CHECK HTTP:80/index.html 5 3 2 2_

**Add Instances to ELB**

_#elb-register-instances-with-lb elbtest-agile –instances i-A123456 –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

**To LIST ELBs**

#elb-describe-lbs –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem
LOAD_BALANCER elbtest-agile elbtest-agile-1809496912.eu-west-1.elb.amazonaws.com elbtest-agile-1809496912.eu-west-1.elb.amazonaws.com Z3NF1Z3NOM5OY2 2012-07-19T09:49:19.110Z





* * *





###  DELETIONS





* * *



**1.Delete Trigger**

_#as-delete-trigger awsauto-nixontrtest –auto-scaling-group awsauto-nixongptest –region eu-west-1_

DEPRECATED: This command is deprecated and included only to facilitate migration to the new trigger mechanism. You should use this command for migration purposes only.

Are you sure you want to delete this trigger? [Ny]y
OK-Deleted trigger

**2.Set Autoscale to 0**

_#as-update-auto-scaling-group as-agile-gp –region eu-west-1 –launch-configuration as-agile –min-size 0 –max-size 0 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

OK-Updated AutoScalingGroup

**3.Delete Autoscale Group**

_#as-delete-auto-scaling-group as-agile-gp –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

OK-Deleted AutoScalingGroup

**4.Delete Autoscale Config**

_#as-delete-launch-config as-agile –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

Are you sure you want to delete this launch configuration? [Ny]y
OK-Deleted launch configuration

**5.Finally Delete ELB**

_#elb-delete-lb elbtest-agile –region eu-west-1 -K ec2_certs/pk-K4.pem -C ec2_certs/cert-K4.pem_

Warning: Deleting a LoadBalancer can lead to service disruption to any
customers connected to the LoadBalancer. Are you sure you want to delete
this LoadBalancer? [Ny]y
OK-Deleting LoadBalancer

The Following commands will list out Autoscale Related Info —
as-describe-auto-scaling-groups
as-describe-auto-scaling-instances
as-describe-launch-configs
as-describe-triggers [AutoScalingGroupName]


#### 




#### References


[AWS Link To Download & Install ELB Command Line](http://docs.amazonwebservices.com/ElasticLoadBalancing/latest/DeveloperGuide/UsingTheCommandLineTools.html)
[ELB Quick Reference Link](http://aws.amazon.com/elasticloadbalancing/)
[ELB external Reference](http://www.linuxhelp.in/2012/05/amazon-elastic-load-balancing-with.html)
[Check Amazon Youtube video on ELB](http://www.youtube.com/watch?v=8KQ8aLoxVi0&feature=related)
[Check Youtube video on Autoscale](http://www.youtube.com/watch?v=ainDIPzVM84)
[Alestic Lists out all AWS Tools download](http://alestic.com/2012/09/aws-command-line-tools?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+alestic+%28Alestic.com+-+Ubuntu+on+EC2%29)
