---
author: vishnur66
comments: false
date: 2014-01-19 13:46:59+00:00
layout: post
link: http://www.vishnu-tech.com/2014/01/git-mirror-if-git-hub-goes-down/
slug: git-mirror-if-git-hub-goes-down
title: Git Mirror - If git hub goes down
wordpress_id: 269
categories:
- Git
tags:
- git
- git mirror
- github down
- mirror
---

Git has [a built-in server](https://www.kernel.org/pub/software/scm/git/docs/git-daemon.html) for sharing git repositories. If you have several repositories in your working directory:

for eg:

Step-1 :- We clone the repo from Github

git clone git@github.com:vishnu/vishnu.git ---> To our home directory for eg: /home/vishnu

step-2 :- git-deamon
git daemon --base-path=/home/vishnu --export-all --enable=receive-pack
(I created a supervisor to run this)

root@vishnu-machine:~# vim /etc/supervisor/conf.d/git_mirror_update.conf 
[program:git_mirror_update]
command=/usr/local/bin/git daemon --base-path=/home/vishnu --export-all --enable=receive-pack
process_name=%(program_name)s
directory=/home/vishnu
autostart=true
autorestart=true
stopsignal=QUIT
redirect_stderr=true
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=5
stdout_capture_maxbytes=1MB

root@vishnu-machine:~# supervisorctl status
git_mirror_update RUNNING pid 3041, uptime 8:18:46



step-3 :- then we create a mirror from the repo which we cloned to another location in our system

git clone git://127.0.0.1/vishnu

Step-4 :-

git clone --mirror git@github.com:vishnu/vishnu.git

Step-5 :-

cd vishnu.git

Step-6 :-

git push --mirror git://127.0.0.1/vishnu ------> This will update all the remote branches

In the deploy.rb, use

set :repository, "git://127.0.0.1/vishnu"

For refferel :- https://help.github.com/articles/working-when-github-goes-down
