---
author: vishnur66
comments: false
date: 2012-12-15 10:22:10+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/installing-nagios-on-ubuntu/
slug: installing-nagios-on-ubuntu
title: Installing Nagios on Ubuntu
wordpress_id: 93
categories:
- NAGIOS
tags:
- nagios
- nrpe
- ubuntu
---

[Nagios](http://www.nagios.org/) is the Industry Standard Network Monitoring Engine. OK great so whats that mean, well nagios is a server that monitors your hosts and services and will inform you if something goes wrong and when it is fixed again. It can monitor network services, host resources and even network probes such as temperature and moisture.

These features as well as many more make nagios, by far, the most complete Network Monitoring tool on the market, however with all these features comes complexity and nagios has obviously been designed with the experienced Network Administrator in mind. But don’t worry too much, its not that difficult to learn as long as you take it one step at a time, and in today’s tutorial we’re going to look at the first step, actually getting it installed



First of all you will need a server running the LAMP Stack. Then you will need to setup a special user account and group for nagios, this allows nagios to have some rights over the server without giving it full root access, to do this first type:

    
    sudo useradd -m -s /bin/bash nagios


to add the new user and group, then you’ll need to set the password, use:

    
    sudo passwd nagios


Now create the ‘nagcmd’ group to allow external commands to be run, then add the nagios user and the apache user to the group:

    
    sudo /usr/sbin/groupadd nagcmd
    sudo /usr/sbin/usermod -a -G nagcmd nagios
    sudo /usr/sbin/usermod -a -G nagcmd www-data


Now that’s all out of the way you’ll need to download Nagios (I recommend downloading it to its own directory, eg. ~/nagios-install):

    
    wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-3.4.3.tar.gz


then extract the source code form the tar file and navigate into the new folder:

    
    tar -xzf nagios-3.4.3.tar.gz
    cd nagios-3.2.1


now we just need to download gcc, the c++ compiler,

    
    sudo apt-get install gcc


Now its time to install nagios, we are going to do this buy compiling the source code, this can seem a little confusing the first time you do it, but don’t worry you don’t relay have to understand it yet (that comes when you start to make your own programs) just follow the lines and you will be ok:

    
    ./configure --with-command-group=nagcmd
    sudo make all
    sudo make install
    sudo make install-init
    sudo make install-config
    sudo make install-commandmode


Now we just need to configure the contacts .cfg file so nagious can email us the report:

    
    sudo vim /usr/local/nagios/etc/objects/contacts.cfg


and change the email field to your email address.

Because the installer thinks Apache is installed in /etc/httpd/ witch it isn’t, we need to make a symlink to /etc/apache2/. A symlink is a file or folder that contains reference to another folder/file, so when the installer thinks its installing into /etc/httpd its actually installing into apache2, to do this type:

    
    sudo mkdir /etc/httpd
    sudo ln -s /etc/apache2/* /etc/httpd/
    sudo make install-webconf


now we need to add the password to login to our nagios site, remember this password you will need it later:

    
    sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin


restart apache

    
    sudo /etc/init.d/apache2 reload


You don’t need to do the next step, however its extremely useful to install the base plug in pack to provide some extra functionality for nagios, to do this

    
    cd ~/
    wget http://prdownloads.sourceforge.net/sourceforge/nagiosplug/nagios-plugins-1.4.14.tar.gz
    tar -xzf nagios-plugins-1.4.14.tar.gz
    cd nagios-plugins-1.4.14
    sudo ./configure --with-nagios-user=nagios --with-nagios-group=nagios
    sudo make
    sudo make install


Configure Nagios to automatically start when the system boots.

    
    sudo ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios


Verify the sample Nagios configuration files.

    
    sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg


If there are no errors, start Nagios.

    
    sudo /etc/init.d/nagios start


now you can login to Nagios buy going to http://your_ip/nagios/ in your web browser and using the details:

username:nagiosadmin
password:_[the password you set earler]_

And that’s about it you should now have a nagios server set-up that is monitoring the local host

**You may need to update nagios to its latest version, to do this type:**

    
    cd /usr/local/src
    sudo wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-3.2.2.tar.gz
    sudo tar xvzf nagios-3.2.2.tar.gz
    cd nagios-3.2.2
    sudo ./configure --prefix=/usr/local/nagios --with-nagios-user=nagios --with-nagios-group=nagios --with-command-group=nagcmd --enable-nanosleep --enable-event-broker
    sudo make all
    sudo make install
    sudo make install-init
    sudo make install-commandmode
    sudo make install-config
    sudo update-rc.d -f nagios defaults
    cd ..
