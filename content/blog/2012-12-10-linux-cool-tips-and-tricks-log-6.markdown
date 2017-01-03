---
author: vishnur66
comments: false
date: 2012-12-10 11:38:03+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/linux-cool-tips-and-tricks-log-6/
slug: linux-cool-tips-and-tricks-log-6
title: Linux Cool Tips and Tricks Log 6
wordpress_id: 66
categories:
- Linux Tips and Tricks
tags:
- linux
- logs
- tricks
---

## Overview


Linux Tips and Tricks continued…


## The destroyer command


If you are planning to throw or dump your old hard disk (Since you are big rich lad and can buy super cool SSDs) it is always advisable to wipe and erase data on your old one. You would like to make it as hard as possible for anyone to pull out any data out of your old thrown hard disk. Data can still be restored after you delete everything and perform a format. Shred is a popular utility that comes pre installed with almost every linux distribution. Lets use shred to remove anything from my second attached disk.

A word of caution, never try this on a disk you love, this would result in complete data loss.



shred -v -n 1 -z /dev/sdb-v : show progress
-n 2 : overwrite hard disk with two pass of random data (Advisable is to perform more than one pass of randaom data, the down side it is that the process takes bit long to complete)
-z : finish cleaning with one pass of zeros




 


## Which process uses which files?


How many times have you tried unmounting a drive and faced with an error. The fuser command can tell you which user is still accessing the drive making it unmountable. You can easily kill the process then and unmount the drive.



List processes using the cdrom drive (usefull for a clean unmount):
/sbin/fuser -v /mnt/cdrom
List processes using the sound card:
/sbin/fuser -v /dev/dsp0


Cheers…
