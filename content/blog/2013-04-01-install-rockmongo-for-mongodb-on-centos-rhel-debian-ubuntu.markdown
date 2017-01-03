---
author: vishnur66
comments: false
date: 2013-04-01 06:44:11+00:00
layout: post
link: http://www.vishnu-tech.com/2013/04/install-rockmongo-for-mongodb-on-centos-rhel-debian-ubuntu/
slug: install-rockmongo-for-mongodb-on-centos-rhel-debian-ubuntu
title: Install RockMongo For Mongodb On CentOS, RHEL, Debian, Ubuntu
wordpress_id: 210
categories:
- MONGO DB
tags:
- Mongodb
- phpmyadmin
- rockmongo
---

_**Overview**_

If you are looking for a phpadmin like tool for mongodb, RockMongo is your best bet. It is one of the best PHP MongoDB Administrator tool available today. It lets you create database, collections and insert and fetch documents using a web interface. This post will help you setup RockMongo

Install RockMongo for mongodb on CentOS, RHEL, Debian, Ubuntu

The prerequisites include mongo-php-driver, lets install it first

On CentOS, RHEL

# yum install php-devel git httpd

On Debian, Ubuntu

# apt-get install php5-dev apache2 git

Pull mongo-php-driver from git

# git clone https://github.com/mongodb/mongo-php-driver.git
# cd mongo-php-driver/
# phpize
# ./configure
# make all
# make install

Open php.ini and add the so that it gets loaded

On CentOS/RHEL

# vi /etc/php.ini

On Debian, Ubuntu

# vi /etc/php5/apache2/php.ini

Append the following to it

extension=mongo.so

Download the latest rockmongo zip file and unzip it into root directory of the web server.

# wget http://rockmongo.com/downloads/go?id=12

On CentOS/RHEL

# mv rockmongo-1.1.5.zip /var/www/html/

On Debian, Ubuntu

# mv rockmongo-1.1.5.zip /var/www/

Unzip the package

# unzip rockmongo-1.1.5.zip

Restart apache

On CentOS, RHEL

# /etc/init.d/httpd restart

On Debian, Ubuntu

# /etc/init.d/apache2 restart

Open the web browser and point it to

http://ipaddress-or-hostname/rockmongo/index.php

Login using

username: admin
password: admin


[![M](https://www.vishnu-tech.com/wp-content/uploads/2013/04/m.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/04/m.png)







