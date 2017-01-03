---
author: vishnur66
comments: true
date: 2012-12-10 10:29:59+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/how-to-reset-mysql-root-password/
slug: how-to-reset-mysql-root-password
title: How to Reset Mysql ROOT Password
wordpress_id: 36
categories:
- Mysql
tags:
- amazon
- cloud
- computing
- ec2
- linux
- mysql
---

1) **Stop the mysql demon**

/etc/init.d/mysql stop

2) **Start the mysqld demon process using the –skip-grant-tables option with this command**

/usr/sbin/mysqld –skip-grant-tables –skip-networking &

3)** start the mysql client process using this command**

mysql -u root

4) **from the mysql prompt execute this command to be able to change any password**

FLUSH PRIVILEGES;

5)** Then reset/update your password**

SET PASSWORD FOR root@’localhost’ = PASSWORD(‘password’);

FLUSH PRIVILEGES;

6) **Then stop the mysqld process and relaunch it with the classical way:**

/etc/init.d/mysql stop
/etc/init.d/mysql start
