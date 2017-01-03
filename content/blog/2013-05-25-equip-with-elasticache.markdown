---
author: vishnur66
comments: false
date: 2013-05-25 04:21:00+00:00
layout: post
link: http://www.vishnu-tech.com/2013/05/equip-with-elasticache/
slug: equip-with-elasticache
title: Equip with Elasticache
wordpress_id: 236
categories:
- Amazon AWS
---

**Memcache ? and Its Facts !!**

Fotolog, as they themselves point out, is probably the largest site nobody has ever heard of, pulling in more page views than even Flickr.
Fotolog has 51 instances of memcached on 21 servers with 175G in use and 254G available.

Memached is: A high-performance, distributed memory object caching system, generic in nature, but intended for use in speeding up dynamic web applications by alleviating database load.The magic is that none of the memcached servers need know about each other. To scale up you just add more servers and the key hashing algorithm makes it all work out right. Memcached is not redundant, has no failover, and has no authentication. It’s simple server for storing and getting data, the complex bits must be implemented by applications.

[Case Study](http://highscalability.com/bunch-great-strategies-using-memcached-and-mysql-better-together)

Amazon ElastiCache supports nodes with cache sizes ranging from 6 to 67 GB. A DNS name is assigned to each Cache Node when it is created.

[Memcache Hasing](http://amix.dk/blog/post/19356)

[AWS Blog on Elasticache](http://aws.typepad.com/aws/2011/08/amazon-elasticache-distributed-in-memory-caching.html)

[Understanding Elasticache Internals](http://harish11g.blogspot.in/2012/12/elasticache-memcached-connection-buffer.html)

**Why is memcached not recommended for sessions? Everyone does it!**
If a session disappears, often the user is logged out. If a portion of a cache disappears, either due to a hardware crash or a simple software upgrade, it should not cause your users noticable pain. This overly wordy post explains alternatives. Memcached can often be used to reduce IO requirements to very very little, which means you may continue to use your existing relational database for the things it’s good at.

Like keeping your users from being knocked off your site.
In detail why we dont use memcache for sessions – [http://dormando.livejournal.com/495593.html](http://dormando.livejournal.com/495593.html)

**What about the MySQL query cache?**
The MySQL query cache can be a useful start for small sites. Unfortunately it uses many global locks on the mysql database, so enabling it can throttle you down. It also caches queries per table, and has to expire the entire cache related to a table when it changes, at all. If your site is fairly static this can work out fine, but when your tables start changing with any frequency this immediately falls over.

Memory is also limited, as it requires using a chunk of what’s directly on your database.

**Can using memcached make my application slower?**
Yes, absolutely. If your DB queries are all fast, your website is fast, adding memcached might not make it faster.

[Memcache FAQ on Google Code](http://code.google.com/p/memcached/wiki/NewProgrammingFAQ)

**Elasticache Setup**

[![launch-amazon-elasticache-cluster-1](https://www.vishnu-tech.com/wp-content/uploads/2013/05/launch-amazon-elasticache-cluster-1.jpg)](https://www.vishnu-tech.com/wp-content/uploads/2013/05/launch-amazon-elasticache-cluster-1.jpg)

Name: This is the Cache Identifier name and should be unique for an Amazon EC2 region(per account).

Node Type: Cache Capacity type with Memory and CPU. If you want 20 GB of distributed Cache you can choose either 3 Cache.M1.Large or 2 Cache.M1.Xlarge Node types. Usually users prefer Node types with more memory rather than High CPU node types (Not sure what kind of workload needs cache.c1.Xlarge capacity on memory hungry applications). Recently AWS has introduced Cache.M3.Class Node type which fits Memcached kind of use cases very well. The Node type cannot be modified after creating an Amazon ElastiCache Cluster, so please plan your base capacity in advance with some thought. To know more about ElastiCache deployment strategies refer URL: [http://harish11g.blogspot.in/2012/11/amazon-elasticache-memcached-ec2.html](http://harish11g.blogspot.in/2012/11/amazon-elasticache-memcached-ec2.html)

Number of Nodes: Number of Node types you want the Amazon ElastiCache Cluster to launch. There is a limit to Cache nodes you can launch per account. Please increase this limit using ElastiCache Limit Increase Request form.

Version: Memcached 1.4.5

Cache Port: The default port 11211 in which the Amazon ElastiCache node accepts connections.

Preferred Zone: It is the Preferred Amazon EC2 Availability Zone in which you want to launch the Amazon ElastiCache Cluster. It is recommended to keep the Web/App EC2 instances and Amazon ElastiCache nodes on same Availability zone for low latency processing. In case multi-AZ is applicable your architecture, standard Amazon EC2 Regional Data Transfer charges of $0.01 per GB in/out apply when transferring data between an Amazon EC2 instance and an Amazon ElastiCache Node in different Availability Zones of the same Region, you are only charged for the Data Transfer in or out of the Amazon EC2 instance.

The Cache security group will allow request access between your EC2 instances and ElastiCache Nodes. The Security group of your EC2 instances should be added in this ElastiCache Security group for opening the access. This setting applies to all the existing and new cache nodes inside the Amazon ElastiCache cluster. You can either create a new Amazon ElastiCache Security group or make changes in the default security group as well. In case you have Multitude of cache clusters with variety of EC2 tiers accessing them on various workflows I would strongly recommend creating your own cache security groups as best practice. Currently Amazon ElastiCache cannot be accessed outside Amazon EC2 (It does not make sense unless you have Gigabit NW to make productive use of Cache with low latency). Also in future, we can expect Amazon ElastiCache to work inside Amazon Virtual Private Cloud (VPC) network as well.
Cache Parameter Group: You can either create a new parameter group or use the default memcached parameter group. AWS provided default will suffice for most use cases. The parameters will be applied to all the cache nodes in the Amazon ElastiCache cluster.

what is an elasticache cluster node?
A cache node is the smallest building block of an Amazon ElastiCache deployment. It is a fixed-size chunk of secure, network-attached RAM. Each cache node runs an instance of the Memcached service, and has its own DNS name and port. Multiple types of cache nodes are supported, each with varying amounts of associated memory.

[source](http://harish11g.blogspot.in/2012/11/configuring-amazon-elasticache-launch.html)

**What is Configuration Endpoint ?**

[![configurations_endpoint](https://www.vishnu-tech.com/wp-content/uploads/2013/05/configurations_endpoint.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/05/configurations_endpoint.png)

To use Amazon ElastiCache you have to set up a cache cluster. A cache cluster is a collection of cache nodes. You choose the number and the type of nodes to match the performance needs of your application. In the past, if you changed the nodes in your cache cluster (for example, by adding a new node), you would have to update the list of node endpoints manually. Typically, updating the list of node endpoints involves reinitializing the client by shutting down and restarting the application, which can result in downtime (depending on how the client application is architected). With the launch of Auto Discovery, this complexity has been eliminated.

All ElastiCache clusters (new and existing!) now include a unique Configuration Endpoint, which is a DNS Record that is valid for the lifetime of the cluster. This DNS Record contains the DNS names of each of the nodes that belong to the cluster. Amazon ElastiCache will ensure that the Configuration Endpoint always points to at least one such “target” node. A query to the target node then returns endpoints for all the nodes in the cluster. To be a bit more specific, running a query means sending the config command to the target node. We implemented this command as an extension to the Memcached ASCII protocol (read about Adding Auto-Discovery to Your Client Library for more information).

You can then connect to the cluster nodes just as before and use the Memcached protocol commands such as get, set, incr, and decr. The Configuration Endpoint is accessible programmatically through the ElastiCache API, via the command line tools, and from the ElastiCache Console.

To take advantage of Auto Discovery, you will need to use a Memcached client library that is able to use this new feature. To get started, you can use the ElastiCache Cluster Client, which takes the popular SpyMemcached client and adds Auto Discovery functionality. We have a Java client available now (view source), which can be downloaded from the ElastiCache Console:

We plan to add Auto Discovery support to other popular Memcached client libraries over time; a PHP client is already in the works.

ElastiCache remains 100% Memcached-compatible so you can keep using your existing Memcached client libraries with new and existing clusters, but to take advantage of Auto Discovery you must use an Auto Discovery-capable client.

**Do we need to install memcached on server?**
You don’t need to have memcache installed, only the memcache pecl module in your php installation. Elasticache is a memcached server, nothing more nothing less. As long as you have the memcache pecl module installed in your php, the memcache option will be available on the W3TC dropdowns.

**Installing memcache on PHP**
You can install the pecl module with:
#pecl install memcache
OR on an apt based system like debian or ubuntu
#apt-get install php5-memcached

**Installing Elasticache Cluster Client Module**
[source](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/Appendix.PHPAutoDiscoverySetup.html)
#apt-get update
#apt-get install gcc g++ php5 php-pear

Download Amazon_Elasticache_cluster_client for PHP version from the Elasticache Management Console.

With root/sudo permission, add a new file memcached.ini under the directory /etc/php5/conf.d, and insert “extension=<absolute path to amazon-elasticache-cluster-client.so>” in it.
#echo “extension=<absolute path to amazon-elasticache-cluster-client.so>” > /etc/php5/conf.d/memcached.ini

**Test it by running the below contents inside a .php file**
<?php
error_reporting(E_ALL & ~E_NOTICE);

$mc = new Memcached();
$mc->addServer(“localhost”, 11211);

$mc->set(“foo”, “Hello!”);
$mc->set(“bar”, “Memcached…”);

$arr = array(
$mc->get(“foo”),
$mc->get(“bar”)
);
#uncomment below line if detailed results are necessary
#var_dump($arr);
echo “<br> foo = “.$mc->get(“foo”);
echo “<br> bar = “.$mc->get(“bar”);
?>

**Observations**
Here $mc is an object of the class Memcached() which is defined inside the php module “php5-memcached”.Hence the function addServer() also worked well where we add the endpoint of our Elasticache Cluster Nodes Endpoint/Configuration Endpoint and port number.
**Output should be**
foo = Hello!
bar = Memcached…
Which means that the memcache module is online.

[Developer’s Part in Elasticache Implementation](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/AutoDiscovery.html#AutoDiscovery.ModifyAppPHP)
[Internal Working of the Elasticache and Application](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/AutoDiscovery.HowAutoDiscoveryWorks.html)
[Different Elasticache Scenarios](http://harish11g.blogspot.in/2012/11/amazon-elasticache-memcached-ec2.html)

**Convey this to the PHP Guy and Provide him with the Configuration Endpoint :**
ElastiCache supports Auto Discovery—the ability for client programs to automatically identify all of the nodes in a cache cluster, and to initiate and maintain connections to all of these nodes. With Auto Discovery, your application does not need to manually connect to individual cache nodes; instead, your application connects to a configuration endpoint. The configuration endpoint DNS entry contains the CNAME entries for each of the cache node endpoints; thus, by connecting to the configuration endpoint, you application immediately “knows” about all of the nodes in the cluster and can connect to all of them. You do not need to hardcode the individual cache node endpoints in your application.

**Choosing a Cache Node Type and the Number of Cache Nodes**

The total memory capacity of your cache cluster is calculated by multiplying the number of cache nodes in the cluster by the capacity of each Node. The capacity of each cache node is based on the cache node type.
The number of cache nodes in the cache cluster is a key factor in the availability of your cache cluster. The failure of a single cache node can have an impact on the availability of your application and the load on your backend database while ElastiCache provisions a replacement for the failed cache node. The scale of this availability impact can be reduced by spreading your memory and compute capacity over a larger number of cache nodes, each with smaller capacity, rather than a fewer number of high capacity nodes.

In a scenario where you want to have 20GB of cache memory, you can set it up in one of the following ways:

Use 15 cache.m1.small cache nodes with 1.3 GB of memory each = 19.5 GB

Use 3 cache.m1.large cache nodes with 7.1 GB of memory each = 21.3 GB

Use 3 cache.c1.xlarge cache nodes with 6.6 GB of memory each = 19.8 GB

These options provide you with similar memory capacity, but different computational capacity for your cache cluster.

If you’re unsure about how much capacity you need, we recommend starting with one cache.m1.small cache node type and monitoring the memory usage, CPU utilization and Cache Hit Rate with the ElastiCache metrics that are published to Amazon CloudWatch.

If your cache cluster does not have the desired hit rate, you can easily add more nodes, thereby increasing the total available memory in your cache cluster. You will need to obtain an updated endpoint list from the ElastiCache CLI, API or AWS Management Console, and configure your clients to use the additional node(s).

If your cache cluster turns out to be bound by CPU but has sufficient hit rate, then try setting up a new cluster with a different cache node type.

ElastiCache supports adding or removing cache nodes from an existing cache cluster using the AWS Management Console, the API, and the command line tools, allowing you to increase both memory and compute capacity of the cluster at any time.

_Note:_ElastiCache does not currently support dynamically changing the cache node type for a cache cluster after it has been created. If you wish to change the Node Type of a cache cluster, you will need to set up a new cache cluster with the desired Node Type, and migrate your application to that cache cluster.

**Configure PHP to use Elasticache for Sesssions**

#vim /etc/php5/conf.d/memcached.ini
extension=/root/ecache/amazon-elasticache-cluster-client.so
Editing php.ini file
#vim /etc/php5/apache2/php.ini
session.save_handler = memcached
#We mention the configuration endpoint of the elasticache
session.save_path = “test.nugnqi.cfg.use1.cache.amazonaws.com:11211″
#extension is already inside the conf.d/memcache.ini file hence this file may not be necessary
extension=/root/ecache/amazon-elasticache-cluster-client.so
#The ecache dir is the Elasticache Client software that is downloaded for appropriate PHP version to be in used in place of Memcache extension of PHP.

**Install phpMemcachedAdmin**

wget “[http://phpmemcacheadmin.googlecode.com/files/phpMemcachedAdmin-1.2.2-r262.tar.gz&#8221](http://phpmemcacheadmin.googlecode.com/files/phpMemcachedAdmin-1.2.2-r262.tar.gz&#8221);
tar xvzf phpMemcachedAdmin-1.2.2-r262.tar.gz   (inside apache documentroot)
chmod +r *
chmod 0777 Config/Memcache.php
