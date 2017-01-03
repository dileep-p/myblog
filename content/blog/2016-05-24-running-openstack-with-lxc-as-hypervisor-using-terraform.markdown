---
author: vishnur66
comments: true
date: 2016-05-24 18:35:19+00:00
layout: post
link: http://www.vishnu-tech.com/2016/05/running-openstack-with-lxc-as-hypervisor-using-terraform/
slug: running-openstack-with-lxc-as-hypervisor-using-terraform
title: Running Openstack with LXC as hypervisor using Terraform
wordpress_id: 571
categories:
- Amazon AWS
- Amazon EC2
tags:
- AWS
- aws ec2
- ec2
- lxc
- openstack
- terraform
---

Hi Guys,

Today I got some time to play around with Terraform & Openstack. If you want a test Openstack environment with LXC in AWS, you can use this [repo](https://github.com/vishnudxb/terraform-openstack-lxc).


It creates a new VPC environment in AWS in us-east-1 region and boot up an Ubuntu instance in the newly created VPC environment and install Openstack with LXC as hypervisor.



