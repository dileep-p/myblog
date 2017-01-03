---
author: vishnur66
comments: false
date: 2012-12-10 10:19:59+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/rds-cli-tools-setup/
slug: rds-cli-tools-setup
title: RDS CLI Tools Setup
wordpress_id: 24
categories:
- Amazon AWS
tags:
- amazon
- amazon s3
- cloud
- computing
- ec2
- linux
- mysql
- RDS
- s3cmd
---

#cd /usr/local/rds
#chmod 744 bin/*

Setup Credentials into the file to use a default AWS Account —
$ cd /usr/local/rds
$ sudo cp credential-file-path.template credential-file
$ sudo vi credential-file
$ sudo chmod 600 /usr/local/aws/rds/credential-file

Now add to the ~/.bashrc
# Set location of the ec2 and rds command line tools
export EC2_HOME=/usr
export AWS_RDS_HOME=/usr/local/aws/rds
# Set AWS path
export PATH=$PATH:$EC2_HOME/bin:$AWS_RDS_HOME/bin

Source the .bashrc file —
$ source .bashrc

_[Reference Link 1](http://domino.symetrikdesign.com/2011/04/28/installing-amazon-rds-command-line-toolkit-on-ubuntu-10-04/)_
