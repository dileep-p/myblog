---
author: vishnur66
comments: true
date: 2016-08-19 09:23:25+00:00
layout: post
link: http://www.vishnu-tech.com/2016/08/ansible-playbooks-to-create-private-docker-registry-in-aws/
slug: ansible-playbooks-to-create-private-docker-registry-in-aws
title: Ansible playbooks to create private docker registry in AWS
wordpress_id: 585
categories:
- Amazon AWS
- Amazon EC2
- Ansible
- docker
tags:
- amazon
- amazon s3
- AWS
- aws ec2
- container
- docker
- docker registry
- ec2
- linux
- registry
---

Creating a private docker registry in AWS using Ansible playbooks. You can find the playbooks from [here.](https://github.com/vishnudxb/ansible-docker-registry)


# Variables in playbooks





	
  * _server_name: "docker-registry.vishnudxb.me" _

	
  * _ssl_bundle_crt: "/etc/nginx/ssl/ssl-bundle.crt" _

	
  * _ssl_crt_key: "/etc/nginx/ssl/ssl-cert.key"_




## AWS instance tag name





	
  * _The playbook will identified the instance using the tag name ie docker-registry_




## To-Do























	
  * Authorization for users inside the registry.




























# 



