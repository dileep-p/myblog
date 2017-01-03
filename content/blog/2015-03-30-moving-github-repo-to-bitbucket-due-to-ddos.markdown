---
author: vishnur66
comments: true
date: 2015-03-30 10:48:31+00:00
layout: post
link: http://www.vishnu-tech.com/2015/03/moving-github-repo-to-bitbucket-due-to-ddos/
slug: moving-github-repo-to-bitbucket-due-to-ddos
title: Moving github repo to Bitbucket due to DDOS
wordpress_id: 425
categories:
- Git
tags:
- bitbucket
- git
- github
- github down
---

### **Github Under JS-Based "Greatfire" DDoS Attack, Allegedly From Chinese Government.**





#### During the past two days, popular code hosting site GitHub has been under a DDoS attack, which has led to intermittent service interruptions. As blogger [Anthr@X](http://insight-labs.org/?p=1682) reports from traceroute lists, the attack originated from MITM-modified JavaScript files for the Chinese company Baidu's user tracking code, changing the unencrypted content as it passed through the great firewall of China to request the URLs github.com/greatfire/ and github.com/cn-nytimes/. The Chinese government's dislike of widespread VPN usage may have caused it to arrange the attack, where only people accessing Baidu's services from outside the firewall would contribute to the DDoS. This wouldn't have been the first time China arranged this kind of "protest."


_**Because of this DDOS attack, it affects our depolyment process. So we planned to move some of the critical repos to **__**bitbucket (for temp).**_



_STEP1:-_

_Create your private repo in the bitbucket._

_STEP2:-_

_Let's suppose in your local machine you already have a clone repo of your code from github.com_

_From your local machine, you need to change the origin, you need to push all tags, branches and refs to the bitbucket._

_So you need to do the below commands_

_ _

[color-box color="green"]

git remote -v     #It will show the origin (fetch and push) like below:

origin git@github.com:username/yourproject.git (fetch)

origin git@github.com:username/yourproject.git (push)

[/color-box]

_Now your origin is github.com. Before changing it to bitbucket, you need to do checkout all your remote branches to your local machine, then only you can push your branches to bitbucket, otherwise you can only push the tags, there won't be any branches apart from "master"._

[color-box color="green"]

git branch     #will list the branch in your local machine

git branch -r     #will list the remote branch from github

[/color-box]

_ Now, you can use the below commands to checkout all your remote branches to your localmachine._

[color-box color="green"]

for remote in `git branch -r `; do git branch --track $remote; done

for remote in `git branch -r `; do git checkout $remote ; git pull; done

[/color-box]

_ Then again checkout to master_

[color-box color="green"]

git status

git checkout master

[/color-box]

_Now, we need to move the origin from github to bitbucket_

[color-box color="green"]

git remote -v

git remote rm origin

git remote -v

git remote add origin git@bitbucket.org:username/your-repo-in-bitbucket.git

git push -u origin --all

git push -u origin --tags

[/color-box]

_This will move all from github to bitbucket._

_We use Capistrano for deployment, so update your deploy.rb file._

_Change the git repo to bitbucket repo._

[color-box color="green"]

set :repository, "git@github.com:username/githubrepo.git"

[/color-box]

 Change the above line and update it with your bitbucket repo.

[color-box color="green"]

set :repository, "git@bitbucket.org:username/your-repo-in-bitbucket.git"

[/color-box]

_**Now you are good to deploy. **_
