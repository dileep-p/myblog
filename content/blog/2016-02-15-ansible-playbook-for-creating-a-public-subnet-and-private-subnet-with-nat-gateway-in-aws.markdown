---
author: vishnur66
comments: true
date: 2016-02-15 13:46:50+00:00
layout: post
link: http://www.vishnu-tech.com/2016/02/ansible-playbook-for-creating-a-public-subnet-and-private-subnet-with-nat-gateway-in-aws/
slug: ansible-playbook-for-creating-a-public-subnet-and-private-subnet-with-nat-gateway-in-aws
title: Ansible playbook for creating a public subnet and private subnet with NAT gateway
  in AWS.
wordpress_id: 557
categories:
- Amazon AWS
tags:
- amazon
- ansible
- AWS
- gateway
- NAT
- nat gateway
- private subnet
- public subnet
- subnet
- vpc
---

An Ansible playbook for creating a Public subnet and Private subnet with NAT gateway. Currently Ansible is working on the module 'ec2_vpc_nat_gateway'  and it is not production ready. So I used a bash script along with the Ansible play to create the private subnet with NAT gateway. You can find the github repo from [here.](https://github.com/vishnudxb/ansible-vpc-nat)


