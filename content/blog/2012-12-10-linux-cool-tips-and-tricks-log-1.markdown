---
author: vishnur66
comments: false
date: 2012-12-10 11:19:06+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/linux-cool-tips-and-tricks-log-1/
slug: linux-cool-tips-and-tricks-log-1
title: Linux Cool Tips and Tricks Log 1
wordpress_id: 48
categories:
- Linux Tips and Tricks
tags:
- linux
- logs
- tricks
---

Laziness is a natural human quality. Also, we human beings are getting busy each passing day, so we are always short on time. You combine these two and we see big hindrance in our learning curve. This beautiful Sunday morning I thought why not start a small informative series highlighting some of the greatest tricks that Linux has up its sleeves. Here are some of the commands, tips and tricks that you may not know. I have purposely kept these articles small with couple of tricks so that you learn them, remember them and try them.

Lets Start


## The bond and bourne of linux commands.


What if you need to fire of a command without leaving a tarce. So to execute a command without saving it in the history, just put a blank space before it.


#[space] command







Example







#   pwd




## For the forgetful.


You just opened a file to edit in your favorite vi/vim editor and just when you tried saving it you were greeted with a warning “Can’t open file for writing”. Ouch you forget to open it using root. So to save it use the trick below.


:w !sudo tee %
