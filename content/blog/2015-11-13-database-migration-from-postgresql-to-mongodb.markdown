---
author: vishnur66
comments: true
date: 2015-11-13 15:38:14+00:00
layout: post
link: http://www.vishnu-tech.com/2015/11/database-migration-from-postgresql-to-mongodb/
slug: database-migration-from-postgresql-to-mongodb
title: Database Migration From Postgresql to Mongodb
wordpress_id: 532
categories:
- MONGO DB
tags:
- database
- database migration
- migration
- Mongodb
- Nosql
- postgres
- postgresql
---

Here I am migrating the Postgresql database to Mongodb with the help _**[Mongify.](http://mongify.com/) **_

If you check my postgres db, you can see a table named "rep_test" and the data inside the table.

[color-box color="green"]

postgres=# \dt

List of relations


Schema |   Name   | Type  |  Owner   




--------+----------+-------+----------




public | rep_test | table | postgres




(1 row)




[/color-box]


[color-box color="blue"]





postgres=# select * from rep_test;




      test       




-----------------




data one




some more words




lalala




hello there




blahblah




(5 rows)


[/color-box]

_Now for the db migration, you need to install the Mongify._

[color-box color="green"]


gem install mongify




Then you need to create a file named "database.config"




For eg:-




root@locahost:~# cat database.config




_sql_connection do_




_  adapter     "postgresql"_




_  host           "localhost"_




_  username  "postgres"_




_  password  "postgres123"_




_  database  "postgres"_




_end_




_mongodb_connection do_




_  host      "localhost"_




_  database  "postgres"_




_end_




[/color-box]




_Then, you need to check if the database.config file is correct and the connections are working fine. You can check this by the below command:_


[color-box color="blue"]

mongify check database.config


[/color-box]




If the adapter is missing, it will give an error and you need to install the adapter by running the below command:


[color-box color="green"]


gem install activerecord-postgresql-adapter




[/color-box]




_Now, you need to run the translation command_


[color-box color="blue"]


mongify translation database.config > translation.rb




[/color-box]




_Now, you can tell the mongify to move the data to mongodb. _


[color-box color="green"]


mongify process database.config translation.rb




[/color-box]




_Now, Verify everything is migrated to your Mongodb_


[color-box color="blue"]


> show collections




rep_test




system.indexes




> db.rep_test.find()




{ "_id" : ObjectId("5645f3f000286643c4000001"), "test" : "data one" }




{ "_id" : ObjectId("5645f3f000286643c4000002"), "test" : "some more words" }




{ "_id" : ObjectId("5645f3f000286643c4000003"), "test" : "lalala" }




{ "_id" : ObjectId("5645f3f000286643c4000004"), "test" : "hello there" }




{ "_id" : ObjectId("5645f3f000286643c4000005"), "test" : "blahblah" }




[/color-box]













