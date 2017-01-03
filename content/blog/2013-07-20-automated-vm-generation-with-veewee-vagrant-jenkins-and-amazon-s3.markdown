---
author: vishnur66
comments: false
date: 2013-07-20 05:43:18+00:00
layout: post
link: http://www.vishnu-tech.com/2013/07/automated-vm-generation-with-veewee-vagrant-jenkins-and-amazon-s3/
slug: automated-vm-generation-with-veewee-vagrant-jenkins-and-amazon-s3
title: Automated VM Generation with veewee, Vagrant, Jenkins and Amazon S3
wordpress_id: 246
categories:
- Amazon AWS
---






Recently at my job there has been an increasing amount of LAMP development going on. One part of the learning curve is getting developers set up with an LAMP environment to develop on. Currently this is done by developers using a shared environment. I wanted to explore the idea of developers being able to easily spin up a local LAMP environment using Vagrant and Puppet.

There are a large number of [Vagrant base boxes](http://www.vagrantbox.es/) available but people (like your boss) probably shy away from the idea of using a base box built by somebody else. Who knows what could be on it without doing some kind of audit? Luckily there is a tool called [veewee](https://github.com/jedi4ever/veewee) that helps automate the process of building your own base boxes.

I thought it would be fun to try and automate the whole process using [Jenkins](http://jenkins-ci.org/) and publish the box to Amazon S3. Later I’ll create a Vagrant project and reference the base box I created using vewee and work towards turning it into a LAMP box.

I’ll be using the following tools:



	
  * [RVM](https://rvm.io/) - Used to install and manage our Ruby environment.

	
  * [Ruby](http://www.ruby-lang.org/) - veewee and Vagrant are Ruby projects.

	
  * [RubyGems](http://rubygems.org/) - veewee and Vagrant are provided as Ruby Gems.

	
  * [Bundler](http://gembundler.com/) - Used to define and resolve the Ruby Gems used by our project.

	
  * [VirtualBox](https://www.virtualbox.org/) - veewee and Vagrant use VirtualBox to build and run Virtual Machines.

	
  * [veewee](https://github.com/jedi4ever/veewee) - The tool we use to build our own custom box.

	
  * [Vagrant](http://www.vagrantup.com/) - The tool we use to configure and run our Virtual Machines.

	
  * [CentOS](http://www.centos.org/) - A free Linux distribution equivalent to RedHat.

	
  * [Puppet](http://puppetlabs.com/) - The tool will be using to automatically provision the VM - install packages and setup service.

	
  * [GitHub](https://github.com/) - Where we’ll store out project code.

	
  * [Jenkins](http://jenkins-ci.org/) - A Continuous Integration server we’ll use to automate the building of our box.

	
  * [Amazon S3](http://aws.amazon.com/s3/) - Used as a public place to host our base box.




### Assumptions


I’m assuming the following about you:



	
  * you’re familiar with Ruby

	
  * you’re familiar with Unix/Linux

	
  * you know how to use Git fairly well

	
  * you know how to set up an Amazon S3 Bucket


If you don’t know any of those things then Google is your best friend.


## Prepare Host Machine


I’m doing this on my Mac so there were a few things I needed to set up before getting started.


### VirtualBox


Vagrant uses VirtualBox so I installed that first. You can download it from [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads).


### Ruby


veewee and Vagrant are both Ruby Gems so you’ll need to have Ruby and RubyGems installed. My recommended way of doing this is to use [RVM](https://rvm.io/).


#### Install Ruby 1.9.3


Once you have RVM installed you can install Ruby 1.9.3.

    
    <span style="color:#0000ff;"><code>$ rvm install 1.9.3
    </code></span>


Then switch to Ruby 1.9.3

    
    <span style="color:#0000ff;"><code>$ rvm use 1.9.3
    </code></span>




## veewee Project


We’re going to make a veewee project that defines and builds the CentOS base box we want to use in Vagrant. Let’s start by making a directory for our veewee project:

    
    <span style="color:#0000ff;"><code>$ mkdir veewee-centos63
    $ cd veewee-centos63
    </code></span>




### Define Gem Dependencies


Since we’re using veewee and Vagrant, we need to install those Gems for our project to work. The best way to do this is using a [Gemfile](http://gembundler.com/gemfile.html) which lists the version of each Gem we want to use. So create a `Gemfile` in your project’s directory with the following contents:

    
    <span style="color:#0000ff;"><code>source :rubygems
    
    gem 'vagrant', '1.0.5'
    gem 'veewee', '0.3.7'
    </code></span>


Then to install the gems simple run:

    
    <span style="color:#0000ff;"><code>$ bundle
    </code></span>




### Create Base Box Definition


Now we create the definition files required for making a base box using veewee:

    
    <span style="color:#0000ff;"><code>$ bundle exec veewee vbox define 'centos63' 'CentOS-6.3-x86_64-minimal'
    </code></span>


This will create a number of files in a `definitions` folder. You can tweak these to your liking but I’m leaving them as is for now.

FYI: The `bundle exec` makes sure we’re using the version of veewee defined in our Gemfile.

If you’re curious, you can see a full list of available templates by running:

    
    <span style="color:#0000ff;"><code>$ bundle exec veewee vbox templates
    </code></span>




### Build Base Box


Now it’s time to actually build, validate and export the base box:

    
    <span style="color:#0000ff;"><code>$ bundle exec veewee vbox build 'centos63'
    $ bundle exec veewee vbox validate 'centos63'
    $ bundle exec vagrant basebox export 'centos63'
    </code></span>


This will create a file named `centos63.box` in your `veewee-centos63` directory.

To immediately add the box to the host machine’s Vagrant boxes:

    
    <span style="color:#0000ff;"><code>$ vagrant box add 'centos63' 'centos63.box'
    </code></span>


If the box already exists you’ll need to first run the following:

    
    <span style="color:#0000ff;"><code>$ bundle exec vagrant box remove 'centos63'
    </code></span>


Later on we’ll be pushing this box to Amazon S3 and referencing it from a Vagrant project to get it installed.


### Automating the Build


Since we’ll be building this box using a CI server, we want to automate the process as much as possible, so let’s write a little script called `build.sh` to run the above for us:

    
    <span style="color:#0000ff;"><code>#!/bin/bash
    
    bundle install
    
    bundle exec veewee vbox build 'centos63' --force --auto --nogui
    bundle exec veewee vbox validate 'centos63'
    
    bundle exec vagrant basebox export 'centos63' --force
    </code></span>


Make sure you allow executable permissions on that script so you can run it:

    
    <span style="color:#0000ff;"><code>$ chmod u+x build.sh
    </code></span>


Then run the script to make sure it works:

    
    <span style="color:#0000ff;"><code>$ ./build.sh
    </code></span>




### Version Control


Finally it’s time to get this little project into version control. We’ll put it on GitHub so there’s a public place for our Jenkins server to access the code.

First let’s create a `.gitignore` file so we prevent the resulting box from getting checked into version control accidentally. Add the following at a minimum:

    
    <span style="color:#0000ff;"><code>centos63.box
    </code></span>


Now set up a local Git repo:

    
    <span style="color:#0000ff;"><code>$ git init
    $ git add definitions Gemfile Gemfile.lock build.sh
    $ git commit -m "Initial project"
    </code></span>


Set up [a new GitHub repository](https://github.com/new) and push you local repo to your GitHub one:

    
    <code><span style="color:#0000ff;">$ git remote add origin https://github.com/spilth/veewee-centos63.git
    $ git push -u origin master</span> 
    </code>




## Automatic Box Building with Jenkins


Now it’s time to set up Jenkins to build your base box for you whenever there’s a change to it’s definition.


### Installation


There’s a Jenkins native package for most operating systems, so I suggest you download it from [http://jenkins-ci.org/](http://jenkins-ci.org/). The install package should automatically start Jenkins and you’ll be able to get to it from your browser using: [http://localhost:8080/](http://localhost:8080/)


### Jenkins Plugins


You’ll need a few plugins to help build the project. From the main Jenkins screen choose _Manage Jenkins_, then click _Manage Plugins_. Click on the _Available_ tab and in the _Filter_ box search for the following:



	
  * “github” or [https://wiki.jenkins-ci.org/display/JENKINS/Github+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Github+Plugin)

	
  * “rvm” or [https://wiki.jenkins-ci.org/display/JENKINS/RVM+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/RVM+Plugin)

	
  * “s3” or [https://wiki.jenkins-ci.org/display/JENKINS/S3+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/S3+Plugin)




#### Configure Git Plugin


From the main screen of Jenkins choose _Manage Jenkins_, then _Configure System_. Find the section titled _Git plugin_ and enter values for _Global Config user.name_ and _Global Config user.email_.


#### Configure Amazon S3 Plugin


On the _Configure System_ screen also look for the section titled _Amazon S3_. Click on the _Add_ button and set up a new profile with you access key and secret key.


### Creating a Jenkins Job


Now we need to create a job in Jekins that will check out your code from GitHub, run the build script we made, store the `centos63.box` it generates and push that box to our Amazon S3 Bucket.



	
  * From the main screen click on _New Job_.

	
  * Give the job a name

	
  * Choose _Build a free-style project_

	
  * Click _OK_

	
  * Under _Source Code Management_ choose _Git_ and enter the Read-Only URL for your GitHub repository.

	
  * Under _Build Environment_ choose _Run the build in a RVM-managed environment_ and enter `1.9.3` in the _Implementation_ field.

	
  * Under _Build_ click _Add build step_ and choose _Execute shell_. Enter `./build.sh` in the _Command_ text area.

	
  * Under _Post-build Actions_ click _Add post-build action_ and choose _Publish artifacts to S3 Bucket_. Choose the profile you created above, then click _Add_. In the _Source_ field put `centos63.box` and put the name of your S3 bucket in the _Destination bucket_ field.

	
  * Click _Save_ at the bottom of the page.

	
  * Finally, click the _Build Now_ link in the left-side nav to kick off your job. Once the job starts you can click the datestamp, then _Console Output_ to see the log of the build script.


Note that it will take some time to download the CentOS ISO and Virtual Box extensions during the first build. It will download them to an `iso` directory in the job’s workspace so future builds won’t take as long.

Additionally, depending on the speed of your connection, uploading to Amazon S3 might take a while.





