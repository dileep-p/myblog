---
author: vishnur66
comments: false
date: 2012-12-10 11:26:07+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/linux-cool-tips-and-tricks-log-2/
slug: linux-cool-tips-and-tricks-log-2
title: Linux Cool Tips and Tricks Log 2
wordpress_id: 54
categories:
- Linux Tips and Tricks
tags:
- linux
- logs
- tricks
---

## Overview


Continuing the tips and tricks series here are the some more tips that you can use to save some time.


## The Copy Cat command


So you have a package installed on a system and you have another system require the same package, can’t u just copy it. Yes you surely can. Login to the server which has the package installed and run the following command


ssh root@servername “rpm -qa” -grep -i vsftp | xargs yum -y install




## Adding readability Super”MAN” pages


So you wish to convert manuals to pdf on the fly. Lets use ps2pdf to perform it. You can convert long html pages, man pages to pdf files for easy access and readability. Lets convert the man manual to pdf.


man -t man | ps2pdf – manpage.pdf


Cheers till next time…
