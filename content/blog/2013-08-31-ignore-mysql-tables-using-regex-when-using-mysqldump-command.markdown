---
author: vishnur66
comments: false
date: 2013-08-31 07:00:33+00:00
layout: post
link: http://www.vishnu-tech.com/2013/08/ignore-mysql-tables-using-regex-when-using-mysqldump-command/
slug: ignore-mysql-tables-using-regex-when-using-mysqldump-command
title: Ignore Mysql Tables using regex when using Mysqldump command
wordpress_id: 262
categories:
- Mysql
tags:
- ignore table
- mysql
- mysqldump
- regex
- regular expressions
---

Hi guys,

We can ignore database tables while taking mysqldump using the below command

mysqldump -u username -p database --ignore-table=database.table1 --ignore-table=database.table2 > database.sql

However if we want to ignore more tables we need to add the tables manually and its a real pain in the A$$

Here I am showing how to take the mysqldump using regex to ignore mysql tables.

For example:- If I want to ignore the below tables starting from

1) alice_token,

2) import_

3)sales_order_item_shipment_tracking

4)sales_order_item_status_history

5)stock_import

6)ums

root@vishnu-machine# cd /var/lib/mysql/<database> 

root@vishnu-machine:/var/lib/mysql/<database># echo '[mysqldump]' > mydump.cnf

root@vishnu-machine:/var/lib/mysql/<database>#  mysql -NBe "select concat('ignore-table=', table_schema, '.', table_name) from information_schema.tables where table_name REGEXP '^(alice_token|import_|sales_order_item_shipment_tracking|sales_order_item_status_history|stock_import|ums)';" > mydump.cnf

And verify the file "mydump.cnf"

root@vishnu-machine:/var/lib/mysql/<database># cat mydump.cnf

ignore-table=<database>.alice_token

ignore-table=<database>.import_token

ignore-table=<database>.import_sales

ignore-table=<database>.sales_order_item_shipment_tracking

ignore-table=<database>.sales_order_item_status_history

ignore-table=<database>.stock_import

ignore-table=<database>.ums

You can see the above entries in the file "mydump.cnf"

root@vishnu-machine:/var/lib/mysql/<database># mysqldump --defaults-file=mydump.cnf -u root -p <database> > vishnu.sql

And here it is new dump without tables as mentioned above.


