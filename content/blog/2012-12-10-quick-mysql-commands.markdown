---
author: vishnur66
comments: false
date: 2012-12-10 10:33:27+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/quick-mysql-commands/
slug: quick-mysql-commands
title: Quick MySQL Commands
wordpress_id: 38
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

**To Login into the MySQL**
#mysql -u username -ppaswword
**and if its RDS :**
#mysql -h rds.indexpoint/dnsname/ip -u username -ppassword

**To List Databases**
mysql>show databases;

**To View tables**
mysql>use databasename;
mysql>show tables;

**To View Contents Inside the Table**
mysql>select * from tablename;

**To Delete Database**
mysql>drop database dbname;

**To create Database user (we also need to grant permission to a db to gain access for the new user.)**
mysql>create user ‘UNNI’@'ipaddress/%/localhost’ identified by ‘passwd3@1′;

**To Grant Permission to a Database for a User and Create the User at the same Time**
mysql>grant all on databasename.* to ‘UNNI’@'ipaddress/%/localhost’ identified by ‘password’;

[NOTE-- To provide access from all IP Address use ‘%’ instead of ipaddress.]

**To List out all Database Users**
mysql>SELECT user,host FROM mysql.user;

**To Create a Database**
mysql>create database UNNI;

**To restore a specific Database in Mysql (database UNNI has to be created already)**
#mysql -u username -ppassword UNNI < UNNI.sql

[NOTE: Database UNNI had to be created before restore]

**To dump/backup a specific Database in Mysql**
#mysqldump -u username -ppassword UNNI > UNNI.sql

[NOTE: Database UNNI is backed up into UNNI.sql]

**To know the permission of Mysql User**
#show grants for ‘unni’@'localhost’;


## [NEED TO KNOW]- Create/Delete User :::


It must be noted that CREATE USER command was added in the MySQL version 5.0.2. In earlier versions, users could be created automatically when assigning permissions using the GRANT command or by manually inserting records in the mysql database.

The mysql database contains three tables – **user, host and db. **These tables contains the database permissions.

The user table contains the usernames and password combination of anyone who has access to any part of the MYSQL database. The password part is the encrypted string, which can be generated using the PASSWORD() function.

As an administrator, you can even directly insert the values into the user table of mysql database and get the desired results.
**`mysql>INSERT INTO user(Host,User,Password) VALUES('localhost', 'UNNI', PASSWORD('passwd1@3'));
`**

**`mysql>FLUSH PRIVILEGES;`**

The FLUSH PRIVILEGES command is required to inform MySQL to reload the privilege data after the change is made.

**Deleting Users**

To delete users from the MySQL database use the DROP command.
`**mysql>DROP USER user@host;**`

The command in turn removes the user record from the mysql.user table.

As the CREATE USER command, even the DROP USER command has been added since MySQL 5.0.2. In previous versions of MySQL you must revoke the user’s privileges first, delete the records from user manually and then issue the FLUSH PRIVILEGES command.

`**mysql>DELETE FROM user WHERE User= 'technofriends' AND Host= 'localhost';
FLUSH PRIVILEGES;**`





**General Notes**



	
  * There is no concept in MySQL of “Owner” of database or its objects, as there is in MS Access and MS SQL Server. I surmise this from the lack of “owner” field anywhere in mysql system tables.


