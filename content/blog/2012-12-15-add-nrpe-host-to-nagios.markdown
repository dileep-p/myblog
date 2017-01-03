---
author: vishnur66
comments: false
date: 2012-12-15 10:40:10+00:00
layout: post
link: http://www.vishnu-tech.com/2012/12/add-nrpe-host-to-nagios/
slug: add-nrpe-host-to-nagios
title: Add NRPE host to Nagios
wordpress_id: 97
categories:
- NAGIOS
tags:
- nagios
- nrpe
- ubuntu
---

NRPE _(Nagios Remote Plugin Executor)_ is a Nagios agent witch allows for remote  system monitoring by executing scripts on a remote system allowing monitoring of disk usage, system’s load or number of users currently logged in and much more. Normally Nagios can only monitor public services such as HTTP & FTP. This is great if you only want to monitor public servers, however I bet that most people want to monitor their own servers and have access to private information, that’s were NRPE comes in. It works on a client server basis, you install a daemon on the machine you  want to monitor, then setup your Nagios server to connect to the remote daemon to gather information

[![nrpe_diag](https://www.vishnu-tech.com/wp-content/uploads/2012/12/nrpe_diag.png?w=300)](http://cloudsherpaz.wordpress.com/2012/12/15/add-nrpe-host-to-nagios/nrpe_diag/)

[![nagios-config](https://www.vishnu-tech.com/wp-content/uploads/2012/12/nagios-config.png?w=300)](http://cloudsherpaz.wordpress.com/2012/12/15/add-nrpe-host-to-nagios/nagios-config/)

So how do we set this up, well first we need to be running a Nagios server.Once you have a Nagios server setup you’ll need to download and install the NRPE daemon on the remote machine.

So lets get started first SSH into your remote machine and enter:

    
    /usr/sbin/useradd nagios
    passwd nagios


this will setup a new Nagios user, now we need to install some plugins:

    
    wget http://prdownloads.sourceforge.net/sourceforge/nagiosplug/nagios-plugins-1.4.14.tar.gz
    tar -xzf nagios-plugins-1.4.14.tar.gz
    cd nagios-plugins-1.4.14
    sudo ./configure --with-nagios-user=nagios --with-nagios-group=nagios
    sudo make
    sudo make install


This will download and install the Nagios plugins required for NRPE to run, now we need to give the plugins access via the new Nagios user:

    
    chown nagios.nagios /usr/local/nagios
    chown -R nagios.nagios /usr/local/nagios/libexec


NRPE require you install xinetd and libssl-dev so we’ll do that now

    
    apt-get install xinetd
    sudo apt-get install libssl-dev


Now that done its time to install the NRPE daemon itself:

    
    cd ~/
    wget http://prdownloads.sourceforge.net/sourceforge/nagios/nrpe-2.12.tar.gz
    tar xzf nrpe-2.12.tar.gz
    cd nrpe-2.12
    ./configure
    make all
    make install-plugin
    make install-daemon
    make install-daemon-config
    make install-xinetd


now we need to configure the daemon so it will talk to our Nagios server, well do this buy editing /etc/xinetd.d/nrpe and adding our monitoring servers address:

    
    nano /etc/xinetd.d/nrpe
    <em>Should Look Like:</em>
    # default: on
    # description: NRPE (Nagios Remote Plugin Executor)
    service nrpe
    {
            flags           = REUSE
            socket_type     = stream
            port            = 5666
            wait            = no
            user            = nagios
            group           = nagios
            server          = /usr/local/nagios/bin/nrpe
            server_args     = -c /usr/local/nagios/etc/nrpe.cfg --inetd
            log_on_failure  += USERID
            disable         = no
            only_from       = 127.0.0.1 <strong>[NAGIOS SERVER ADDRESS]</strong>
    }


Then add “nrpe 5666/tcp # NRPE” to /etc/services:

    
    nano /etc/services
    <em>add:</em>
    nrpe 5666/tcp # NRPE


you can check this all been configured probably by typing:

    
    netstat -at | grep nrpe


this should show something like

    
    (tcp        0      0 *:nrpe                  *:*                     LISTEN)


If that worked correctly its time to setup your Nagios server to monitor your remote host, so ssh into your Nagios server. Now we are going to add separate host file for the new host so we need to edit the nano “nagios.cfg” file:

    
    nano /usr/local/nagios/etc/nagios.cfg
    <em>add lines:</em>
    cfg_file=/usr/local/nagios/etc/objects/host1.cfg


Now we need to create the Host1.cfg:

    
    nano /usr/local/nagios/etc/objects/host1.cfg


and enter the following code:

    
    define host{
            use linux-server ; Inherit default values from a template
            host_name remotehost ; The name we're giving to this server
            alias Fedora Core 6 ; A longer name for the server
            address the-tech-tutorial.com; IP address of the server
    }
    
    define service{
            use generic-service
            host_name remotehost
            service_description CPU Load
            check_command check_nrpe!check_load
    }
    
    define service{
            use generic-service
            host_name remotehost
            service_description Current Users
            check_command check_nrpe!check_users
    }
    
    define service{
            use generic-service
            host_name remotehost
            service_description /dev/hda1 Free Space
            check_command check_nrpe!check_sda1
    }
    
    define service{
            use generic-service
            host_name remotehost
            service_description Total Processes
            check_command check_nrpe!check_total_procs
    }
    
    define service{
            use generic-service
            host_name remotehost
            service_description Zombie Processes
            check_command check_nrpe!check_zombie_procs
    }


Run the nagios config check then restart nagios:

    
    /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
    /etc/init.d/nagios restart


And hay presto you Nagios should now be monitoring your remote host, don’t worry if it says pending it can take up to 5 minutes for the information to come through.

If you want to add more hosts repeat the above steps and use host2.cfg instead of host1.cfg.

[![Finished](https://www.vishnu-tech.com/wp-content/uploads/2012/12/finished.jpg?w=300)](http://cloudsherpaz.wordpress.com/2012/12/15/add-nrpe-host-to-nagios/finished/)

the critical in the above screen dump is because I had disabled ICMP on the remote server.
