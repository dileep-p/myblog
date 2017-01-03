---
author: vishnur66
comments: false
date: 2013-01-05 10:31:37+00:00
layout: post
link: http://www.vishnu-tech.com/2013/01/migrate-mysql-database-to-mongodb/
slug: migrate-mysql-database-to-mongodb
title: Migrate Mysql database to Mongodb
wordpress_id: 165
categories:
- MONGO DB
tags:
- database
- install
- install mongodb
- linux
- mongo
- Mongodb
- Nosql
- ubuntu
---

I recently had a chance to try out one of the new NoSQL databases called MongoDB [http://www.mongodb.org](http://www.mongodb.org). This new class of databases seems to solve many of the bottlenecks in MySql and other relational databases. It will give you shear performance, self replication and scalability at not cost because it open source. MongoDB has plenty of drivers for other scripting and high-level languages I use PHP so I download the PHP driver. You can see the supported list here: [http://www.mongodb.org/display/DOCS/Drivers](http://www.mongodb.org/display/DOCS/Drivers). In this blog I convert  a MySQL database using PHP to MongoDB.

First you install MongoDB, you can do it by checking my previous blog.

Then we run the script to convert a Mysql DB to Mongodb.

create a new file called MySqltoMongodb**.php, In that file please copy paste the below contants (please give your Mysql DB details as well as your Mongodb details)**



	
  1. 


<?php


	
  2. 


// mysql settings


	
  3. 


$mydb = "database";


	
  4. 


$myconn = [mysql_connect](http://www.php.net/mysql_connect)('localhost','user','password');


	
  5. 


$setmydb = [mysql_select_db](http://www.php.net/mysql_select_db)( $mydb );


	
  6. 


$mytables = getMyTables( $mydb );


	
  7. 


 //mongo db settings


	
  8. 


$modb = "database";


	
  9. 


$moConnect="mongodb://user:password@localhost";


	
  10. 


 function getMyTables( $dbname ) {


	
  11. 


$tables = [array](http://www.php.net/array)();


	
  12. 


$sql = [mysql_query](http://www.php.net/mysql_query)("SHOW TABLES FROM $dbname ") or [die](http://www.php.net/die)("Error getting tables from $dbname");


	
  13. 


 if( [mysql_num_rows](http://www.php.net/mysql_num_rows)( $sql ) > 0 ) {


	
  14. 


while( $table = [mysql_fetch_array](http://www.php.net/mysql_fetch_array)( $sql ) ) {


	
  15. 


$explain = explainMyTable( $table[0] );


	
  16. 


$tables[$table[0]] = $explain;


	
  17. 


}


	
  18. 


}


	
  19. 


return $tables;


	
  20. 


}


	
  21. 


 function explainMyTable( $tbname ) {


	
  22. 


$explain = [array](http://www.php.net/array)();


	
  23. 


$sql = [mysql_query](http://www.php.net/mysql_query)("EXPLAIN $tbname") or [die](http://www.php.net/die)("Error getting table structure");


	
  24. 


$i = 0;


	
  25. 


 while( $get = [mysql_fetch_array](http://www.php.net/mysql_fetch_array)( $sql ) ) {


	
  26. 


[array_push](http://www.php.net/array_push)( $explain, $get[0] );


	
  27. 


$i++;


	
  28. 


}


	
  29. 


return $explain;


	
  30. 


}


	
  31. 


 function checkEncode($string) {


	
  32. 


if( !mb_check_encoding($string,'UTF-8')) {


	
  33. 


return [mb_convert_encoding](http://www.php.net/mb_convert_encoding)($string,'UTF-8','ISO-8859-1');


	
  34. 


} else {


	
  35. 


return $string;


	
  36. 


}


	
  37. 


 }


	
  38. 


try {


	
  39. 


$moconn = new Mongo($moConnect);


	
  40. 


$modb = $moconn->selectDB( $modb );


	
  41. 


} catch(MongoConnectionException $e) {


	
  42. 


[die](http://www.php.net/die)($e."Problem during mongodb initialization. Please start mongodb server.");


	
  43. 


}


	
  44. 


 foreach( $mytables as $table => $struct ) {


	
  45. 


$sql = [mysql_query](http://www.php.net/mysql_query)("SELECT * FROM $table LIMIT 0 , 500000") or [die](http://www.php.net/die)( [mysql_error](http://www.php.net/mysql_error)() );


	
  46. 


$count = [mysql_num_rows](http://www.php.net/mysql_num_rows)( $sql );


	
  47. 


 // Starts new collection on mongodb


	
  48. 


$collection = $modb->$table;


	
  49. 


 // If it has content insert all content


	
  50. 


if( $count > 0 ) {


	
  51. 


while( $info = [mysql_fetch_array](http://www.php.net/mysql_fetch_array)( $sql, MYSQL_NUM )) {


	
  52. 


$infosize = [count](http://www.php.net/count)( $info );


	
  53. 


$mosql = [array](http://www.php.net/array)();


	
  54. 


 for( $i=0; $i < $infosize; $i++ ) {


	
  55. 


if(![empty](http://www.php.net/empty)($struct[$i]))


	
  56. 


$mosql[$struct[$i]] = checkEncode($info[$i]);


	
  57. 


}


	
  58. 


 $collection->insert($mosql);


	
  59. 


}


	
  60. 


// Only create a new entry empty


	
  61. 


} else {


	
  62. 


 for( $i=0; $i < $infosize; $i++ ) {


	
  63. 


if(![empty](http://www.php.net/empty)($struct[$i]))


	
  64. 


$mosql[$struct[$i]] = '';


	
  65. 


 }


	
  66. 


$collection->insert($mosql);


	
  67. 


}


	
  68. 


}


	
  69. 


 [echo](http://www.php.net/echo) "Done! Please, check your MongoDB collection!";


	
  70. 


?>



Now fire up your browser and launch the page. If all all goes well you should see
**"Done! Please, check your MongoDB collection!"**

After running this script check your Mongo db collection, in that you can see your Mysql Table.

[![Screenshot-6](https://www.vishnu-tech.com/wp-content/uploads/2013/01/screenshot-6.png?w=300)](https://www.vishnu-tech.com/wp-content/uploads/2013/01/screenshot-6.png)
