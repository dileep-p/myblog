---
author: vishnur66
comments: false
date: 2012-12-10 11:39:49+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/linux-cool-tips-and-tricks-log-7/
slug: linux-cool-tips-and-tricks-log-7
title: Linux Cool Tips and Tricks Log 7
wordpress_id: 68
categories:
- Linux Tips and Tricks
tags:
- linux
- logs
- tricks
---

## Overview


Linux Tips and Tricks continued….


## Convert unix timestamp to something readable


If you are a system administrator just like me, I bet you have a bitter sweet relationship with log files. They are the first thing we peek into when something goes wrong. One thing that you will find is the unix time stamp used by most of these. To convert these to human readable date and time format you can use the date command just like this


`
% date -d @1347213569
`





## Find your favorite command


If you ever wish to find your most used command, try look through your history just like this


`
% history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head`
