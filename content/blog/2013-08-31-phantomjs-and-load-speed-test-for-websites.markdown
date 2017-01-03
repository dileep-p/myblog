---
author: vishnur66
comments: false
date: 2013-08-31 06:39:53+00:00
layout: post
link: http://www.vishnu-tech.com/2013/08/phantomjs-and-load-speed-test-for-websites/
slug: phantomjs-and-load-speed-test-for-websites
title: PhantomJS and Load Speed test for websites
wordpress_id: 255
tags:
- load speed
- load test
- page load test
- phantomjs
- website test
---

## _**PhantomJS is an optimal solution for**_


1) Headless Website Testing

2) Screen Capture

3) Page Automation

4) Network Monitoring

Please check this link  http://phantomjs.org/

_**TO INSTALL PHANTOMJS**_


cd /usr/local/share
wget https://phantomjs.googlecode.com/files/phantomjs-1.9.1-linux-x86_64.tar.bz2
tar xjvf phantomjs-1.9.1-linux-x86_64.tar.bz2
ln -s /usr/local/share/phantomjs-1.9.1-linux-x86_64/bin/phantomjs /usr/local/share/phantomjs
ln -s /usr/local/share/phantomjs-1.9.1-linux-x86_64/bin/phantomjs /usr/local/bin/phantomjs
ln -s /usr/local/share/phantomjs-1.9.1-linux-x86_64/bin/phantomjs /usr/bin/phantomjs
# Additional package
apt-get install fontconfig

Then go to the location 

root@machine# cd /usr/local/share/phantomjs-1.9.1-linux-x86_64/examples

root@machine# vim loadspeed.js

ADD OUR EDITED FILE TO GET THE LOAD PAGE SPEED

var args = require('system').args;
    if(args[1] === undefined){
        console.log('you should provide an url');
        phantom.exit();
    }

var namshiUrl = args[1];


var page = require('webpage').create(),
    system = require('system'),
    t,address;

if (system.args.length === 1) {
    console.log('Usage: loadspeed.js '+ namshiUrl);
    phantom.exit(1);
} else {
    t = Date.now();
    address = system.args[1];
    page.open(address, function (status) {
      if (status !== 'success') {
            console.log('FAIL to load the address');
        } else {
            t = Date.now() - t;
            console.log('Page title is ' + page.evaluate(function () {
                return document.title;
            }));
            console.log('Loading time ' + t + ' msec');
        }
        phantom.exit();
    });
}

THEN TEST IT

root@vishnu-machine:~# for weburl in http://google.com http://yahoo.com ; do phantomjs /usr/local/share/phantomjs-1.9.1-linux-x86_64/examples/loadspeed.js ${weburl}; done


THEN INORDER TO DOING THIS WRITE A SCRIPT


#!/bin/bash
true=0
for weburl in http://google.com http://yahoo.com http://hotmail.com ; do /usr/local/bin/phantomjs /usr/local/share/phantomjs-1.9.1-linux-x86_64/examples/loadspeed.js ${weburl}; done  > abc.txt
cat abc.txt | awk '{print $3}' | sed -n "2~2 p" > abc2.txt
for i in `cat abc2.txt`
do
if [ $i -gt 12000 ]
then
    true=1
fi
done
if [ $true -eq 1 ]
then
   mail -s loadtime mailadddress@domain.com < abc.txt
fi


