---
author: vishnur66
comments: true
date: 2017-01-17 13:22:26+00:00
layout: post
slug: ansible-dcos
title: Ansible Playbook for dc/os
categories:
- Ansible
tags:
- AWS
- Ansible
- dc/os
- mesos
- datacenter
---

## Why dc/os?

*DC/OS is based on the production proven Apache Mesos distributed systems kernel, combining years of real-life experience with best practices for building and running modern applications in production.*

*Build modern apps using state of the art technologies such as containers and big data services, and confidently move from development to production.*

*You can learn more about dc/os from [here](https://github.com/dcos)*

## Why dc/os in Vimpelcom?

* *At VimpelCom we are embarking on the journey of transformation and intend to become one of the pioneers of digital disruption.*

* *Most of our microservice applications are running on containers and container orchestration is pretty easy with dc/os. You can also use other tools like docker-swarm, Kubernetes etc for this purpose however there are some apps which are not running in containers & connected to some microservice container applications. So we need way to provide HA, reliability and scalability for those apps as well.*

* *We used AWS for our development/staging environments. Our testing & production env are running on-premises due to data compliance policies.*

* *dc/os provides Cloudformation templates to create the setup in AWS.* [Click here to see the cloudformation template](https://downloads.dcos.io/dcos/stable/aws.html?_ga=1.118161932.488990590.1483648358).

* *However we cannot use this Cloudformation template for our purpose because our production is running on-premises. So we created an Ansible playbook which can be used in AWS as well as on-premises.*

* *I am not sharing the original source code in here however I am open sourcing a simple ansible playbook which can be used for both in AWS as well as in Datacenter*

* *In order to use this playbook for on-premises, you may need to change some env variables and few alternations which is pretty easy if you're familiar with Ansible.*


### You can see the ansible repo from [here](https://github.com/vishnudxb/ansible-dcos)

FYI, from the below images, you may notice the ELB's & ip's, don't worry 🙄🙄 it's a temporary cluster created for testing this playbook 😛😜

### Once you installed, you can see the number of nodes like below
================================================================================

![Number of nodes](/images/nodes.png)


### For installing dcos cli
===========================

![dcos cli](/images/install-cli.png)


### For running a single service
==================================

![Single service](/images/running-1-service.png)


### For scaling the running service
=======================================

![Scaling service](/images/scale-service.png)

### Here you can see the service has been scaled to 5
===============================================================

![Scaling service](/images/running-5-service.png)
