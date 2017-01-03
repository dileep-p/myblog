---
author: vishnur66
comments: false
date: 2014-02-22 14:46:38+00:00
layout: post
link: http://www.vishnu-tech.com/2014/02/how-to-create-a-private-npm-js-repository/
slug: how-to-create-a-private-npm-js-repository
title: How to create a private npm.js repository
wordpress_id: 272
categories:
- Open Source
tags:
- npm
- npm mirror
---

I want to create a mirror of the npm repository to mitigate periods of npm outages and to speed things up a little bit, so here’s how I did it!

## _**Installing CouchDB**_

 Install the required packages:

 `sudo apt-get install build-essential autoconf automake libtool erlang libicu-dev libmozjs-dev libcurl4-openssl-dev `

Download CouchDB 1.5:-

wget http://mirror.cc.columbia.edu/pub/software/apache/couchdb/source/1.5.0/apache-couchdb-1.5.0.tar.gz

Extracting :-

`tar xfv apache-couchdb-1.5.0.tar.gz `

Compile:

`  ``cd apache-couchdb-1.2.0`

``` ./configure`

```  make`

```  make install`

To check:-

   $ couchdb  
  Apache CouchDB 1.5.0 (LogLevel=info) is starting.  
  Apache CouchDB has started. Time to relax.  
  [info] [<0.32.0>] Apache CouchDB has started on http://127.0.0.1:5984/

   $ curl -X GET http://localhost:5984  
   [info] [<0.361.0>] 127.0.0.1 - - GET / 200

Adding CouchDB to the init.d scripts

     $ sudo adduser --disabled-login --disabled-password --no-create-home couchdb

     Adding user `couchdb' ...  
     Adding new group `couchdb' (1001) ...  
     Adding new user `couchdb' (1001) with group `couchdb' ...  
     Not creating home directory `/home/couchdb'.  
     Changing the user information for couchdb Enter the new value, or press ENTER for the default  
     Full Name []: CouchDB Admin   
     Room Number []:    
     Work Phone []:   
     Home Phone []:   
    Other []:   
    Is the information correct? [Y/n] Y

User added, now permissions:

    sudo chown -R couchdb:couchdb /usr/local/var/{log,lib,run}/couchdb

    sudo chown -R couchdb:couchdb /usr/local/etc/couchdb/local.ini

    $ sudo vim /usr/local/etc/couchdb/default.ini  
    [httpd]  
    secure_rewrites = false

    sudo ln -s /usr/local/etc/init.d/couchdb /etc/init.d  
    sudo update-rc.d couchdb defaults  
    sudo /etc/init.d/couchdb start

    

**Replicating npmjs.org**  
Now to replicate the npm registry.

curl -X POST http://127.0.0.1:5984/_replicate -d '{"source":"http://isaacs.iriscouch.com/registry/", "target":"registry", "create_target":true}' -H "Content-Type: application/json"   
What this means is that once this is completed your local replication will be an exact copy of the npm registry. However, to ensure we do indeed receive all updates we will add a "continuous":true parameter to the JSON string in our POST request, this utilises CouchDB’s _changes API and will pull any new changes when this API is notified.

curl -X POST http://127.0.0.1:5984/_replicate -d '{"source":"http://isaacs.iriscouch.com/registry/", "target":"registry", "continuous":true, "create_target":true}' -H "Content-Type: application/json"   
We are now replicating continuously from npmjs.org to our private CouchDB instance! If you ever want to stop these replications you can easily do this by running the same command as before but add a "cancel":true parameter to the JSON POST data.

curl -X POST http://127.0.0.1:5984/_replicate -d '{"source":"http://isaacs.iriscouch.com/registry/", "target":"registry", "continuous":true, "create_target":true, "cancel":true}' -H "Content-Type: application/json"   
And we're almost done! All we need to do now is to set up our own version of the npmjs.org registry 

Getting npm to work with our replicated CouchDB  
Most of the steps can be found on isaacs github in the npmjs.org git repositories README

Of course we need to have nodejs and git installed for this:

git clone git://github.com/isaacs/npmjs.org.git  
cd npmjs.org  
sudo npm install -g couchapp   
npm install couchapp   
npm install semver   
couchapp push registry/app.js http://localhost:5984/registry   
couchapp push www/app.js http://localhost:5984/registry   
Boom, we now have a working npm repository, to test this we can run the following command.

npm --registry http://localhost:5984/registry/_design/scratch/_rewrite login  
npm --registry http://localhost:5984/registry/_design/scratch/_rewrite search  
If you are getting results then everything is fine.

Running on your own subdomain is to modify the [vhosts] section in /usr/local/etc/couchdb/local.ini. Uncomment the example and restart CouchDB.

$ vim /usr/local/etc/couchdb/local.ini  
[vhosts]  
example.com = /registry/_design/scratch/_rewrite  
And while we are at it will lock down the application and prevent unauthorised users from deleting our data.

$ vim /usr/local/etc/couchdb/local.ini  
[admins]  
admin = password  
$ sudo /etc/init.d/couchdb restart   
Start using your version of npm with the client!  
Straight from the npmjs.org README, just replace <registryurl> with your registries url, for example:

http://localhost:5984/registry/_design/app/_rewrite  
You can point the npm client at the registry by putting this in your ~/.npmrc file:

registry = <registryurl>  
You can also set the npm registry config property like:

npm config set <registryurl>  
Or you can simple override the registry config on each call:

npm --registry <registryurl> install <packagename>
