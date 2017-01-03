---
author: vishnur66
comments: true
date: 2015-05-02 17:57:35+00:00
layout: post
link: http://www.vishnu-tech.com/2015/05/ansible-playbook-for-chaos-monkey/
slug: ansible-playbook-for-chaos-monkey
title: Ansible playbook for Chaos Monkey
wordpress_id: 462
categories:
- Ansible
tags:
- ansible
- chaosmonkey
- netflix
- playbook
- simianarmy
---

[![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/05/chaosmonk.jpeg)](https://www.vishnu-tech.com/wp-content/uploads/2015/05/chaosmonk.jpeg)
_**What is Chaos Monkey and Why we need to use it.**_

_Chaos Monkey is a software tool that was developed by Netflix engineers to test the resiliency and recoverability of their Amazon Web Services (AWS)._

_The software simulates failures of instances of services running within Auto Scaling Groups (ASG) by shutting down one or more of the virtual machines. According to the developers, Chaos Monkey was named for the way it wreaks havoc like a wild and armed monkey set loose in a data center._

_Chaos Monkey works on the principle that the best way to avoid major failures is to fail constantly. However, unlike unexpected failures, which seem to occur at the worst possible times, the software is opt-out by default. It can also be configured for opt-in._

_Chaos Monkey has a configurable schedule that allows simulated failures to occur at times when they can be closely monitored. In this way, it’s possible to prepare for major unexpected errors rather than just waiting for catastrophe to strike and seeing how well you can manage._



_Here I am using Ubuntu distro in AWS. _


## _**You can see the Github repo in** [here.](https://github.com/vishnudxb/ansible-chaosmonkey)_




## 





