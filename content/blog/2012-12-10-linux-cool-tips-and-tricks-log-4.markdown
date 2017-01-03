---
author: vishnur66
comments: false
date: 2012-12-10 11:32:31+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/linux-cool-tips-and-tricks-log-4/
slug: linux-cool-tips-and-tricks-log-4
title: Linux Cool Tips and Tricks Log 4
wordpress_id: 61
categories:
- Linux Tips and Tricks
tags:
- linux
- logs
- tricks
---

## Overview


Linux Tips and Tricks continued…


## Find which operating system you are running


Finding the OS information is easy in Linux, I have seen most people jump to /etc/issue file to find it. This is absolutely correct however this file can be easily modified and changed by the sysadmin, to give you false information. A sure shot method of finding OS name and details is to fire this command on your terminal.


lsb_release -a







Example: when ran on my system
Distributor ID: Ubuntu
Description: Ubuntu 12.04 LTS
Release: 12.04
Codename: precise




## Find all files larger than some value and less than some value


If you want to free up some disk space quickly and find all big files use the command below




find / -type f -size +100M -size -1G




Cheers…
