---
author: vishnur66
comments: false
date: 2013-07-20 06:04:34+00:00
layout: post
link: http://www.vishnu-tech.com/2013/07/centralizing-logs-with-lumberjack-logstash-and-elasticsearch/
slug: centralizing-logs-with-lumberjack-logstash-and-elasticsearch
title: Centralizing Logs with Lumberjack, Logstash, and Elasticsearch
wordpress_id: 249
categories:
- Open Source
tags:
- Centralized
- Elasticsearch
- logs
- Logstash
- Lumberjack
---




## Elasticsearch


Elasticsearch is RESTful search server based on Apache Lucene. It's easily scalable and uses push replication in distributed environments. But really, the Logstash/Elasticsearch/Kibana integration is great! If it wasn't for how well these applications integrate with one another, I probably would have looked into putting my logs into SOLR (which I'm more familiar with).

As of Logstash 1.1.9, the Elasticsearch output plugin works with the 0.20.2 version of Elasticsearch, so even though Elasticsearch currently has a newer version available, we're going to use 0.20.2. (If you really need to use the most current version of Elasticsearch, you can use the elasticsearch_http output plugin to make Logstash interface with Elasticsearch's REST API. If you're using the elasticsearch output plugin, your versions must match between Logstash and Elasticsearch.)

First, you'll need to download a headless runtime environment. We run Squeeze around here, so we're using apt-get install default-jre-headless

Then, you'll need to download the .deb package.











<table cellpadding="0" cellspacing="0" border="0" >Download and install Elasticsearch
<tbody >
<tr >

<td >


1




2

</td>

<td >





`wget http:``//download``.elasticsearch.org``/elasticsearch/elasticsearch/elasticsearch-0``.20.2.deb`




`dpkg -i elasticsearch-0.20.2.deb`




</td>
</tr>
</tbody>
</table>






​



You should see that elasticsearch is now running on your server. Stop it for now /etc/init.d/elasticsearch stop - We might want to set up some configuration. For example, you're probably going to be generating a lot of data. And even if you're not, I highly recommend using external block storage if you're on cloud hosting. That way, your data stays when your instance dies.

On some hosts, you may need to run modprobe acpiphp before attaching the block storage device for the first time. By the way, keep in mind that you will generate a LOT of I/O requests. I do NOT recommend setting this up at places like Amazon or HP Cloud where you get billed per 1,000,000 requests. That 1,000,000 may sound like a lot of requests, but if you're stashing 100GB of logs per month, don't be surprised when your I/O bill is more than $3,000. Just a heads up.

Next, you'll want to edit the configuration in /etc/elasticsearch/elasticsearch.yml. If you're on cloud hosting and running more than one node, odds are multicast won't work so you'll have to specify the IPs of your Elasticsearch nodes. You'll also need to make sure that the data directory has been properly specified. By the way, Rackspace **does** support multicast using Cloud Networks. If you've got a multicast-enabled network, then all that you'll need to do for your nodes to communicate with each other is ensure they are configured with the same cluster name.

After configuring Elasticsearch, you can go ahead and start it. If you're running more than one node, I recommend waiting a few minutes between starting each one (or [check the health of the cluster](http://www.elasticsearch.org/guide/reference/api/admin-cluster-health.html) first).


## Logstash


Once again, you'll need a JRE to run this.

First, you'll want to generate self-signed SSL on logstash server:











<table cellpadding="0" cellspacing="0" border="0" >Generate SSL
<tbody >
<tr >

<td >


1

</td>

<td >





`openssl req -x509 -newkey rsa:2048 -keyout ``/etc/ssl/logstash``.key -out ``/etc/ssl/logstash``.pub -nodes -days 365`




</td>
</tr>
</tbody>
</table>








You will need the public key for your Lumberjack shipper.

Next, you'll need to get the Logstash jar.

 









<table cellpadding="0" cellspacing="0" border="0" >Download/Install Logstash
<tbody >
<tr >

<td >


1




2

</td>

<td >





`mkdir` `/opt/logstash`




`wget https:``//logstash``.objects.dreamhost.com``/release/logstash-1``.1.9-monolithic.jar -O ``/opt/logstash/logstash``.jar`




</td>
</tr>
</tbody>
</table>








You'll need to put together your logstash.conf file according to the documentation. Be sure that you have an output filter for Elasticsearch if you're going to store your logs there. If you put it in /etc/logstash/logstash.conf, you can use the following init script to run it. Otherwise, you'll need to run /usr/bin/java -jar /opt/logstash/logstash.jar agent -f <path-to-your.conf> -l <path-to-where-you-want-the.log>











<table cellpadding="0" cellspacing="0" border="0" >Logstash Init Script
<tbody >
<tr >

<td >


1




2




3




4




5




6




7




8




9




10




11




12




13




14




15




16




17




18




19




20




21




22




23




24




25




26




27




28




29




30




31




32




33




34




35




36




37




38




39




40




41




42




43




44




45




46




47




48




49




50




51




52




53




54




55




56




57




58




59




60




61




62




63




64




65




66

</td>

<td >





`#! /bin/sh`




 




`### BEGIN INIT INFO`




`# Provides:          logstash`




`# Required-Start:    $remote_fs $syslog`




`# Required-Stop:     $remote_fs $syslog`




`# Default-Start:     2 3 4 5`




`# Default-Stop:      0 1 6`




`# Short-Description: Start daemon at boot time`




`# Description:       Enable service provided by daemon.`




`### END INIT INFO`




 




`. ``/lib/lsb/init-functions`




 




`name=``"logstash"`




`logstash_bin=``"/usr/bin/java -- -jar /opt/logstash/logstash.jar"`




`logstash_conf=``"/etc/logstash/logstash.conf"`




`logstash_log=``"/var/log/logstash.log"`




`pid_file=``"/var/run/$name.pid"`




 




`start () {`




`        ``command``=``"${logstash_bin} agent -f $logstash_conf --log ${logstash_log}"`




 




`        ``log_daemon_msg ``"Starting $name"` `"$name"`




`        ``if` `start-stop-daemon --start --quiet --oknodo --pidfile ``"$pid_file"` `-b -m --``exec` `$``command``; ``then`




`                ``log_end_msg 0`




`        ``else`




`                ``log_end_msg 1`




`        ``fi`




`}`




 




`stop () {`




`        ``log_daemon_msg ``"Stopping $name"` `"$name"`




`        ``start-stop-daemon --stop --quiet --oknodo --pidfile ``"$pid_file"`




`}`




 




`status () {`




`        ``status_of_proc -p $pid_file ``""` `"$name"`




`}`




 




`case` `$1 ``in`




`        ``start)`




`                ``if` `status; ``then` `exit` `0; ``fi`




`                ``start`




`                ``;;`




`        ``stop)`




`                ``stop`




`                ``;;`




`        ``reload)`




`                ``stop`




`                ``start`




`                ``;;`




`        ``restart)`




`                ``stop`




`                ``start`




`                ``;;`




`        ``status)`




`                ``status && ``exit` `0 || ``exit` `$?`




`                ``;;`




`        ``*)`




`                ``echo` `"Usage: $0 {start|stop|restart|reload|status}"`




`                ``exit` `1`




`                ``;;`




`esac`




 




`exit` `0`




</td>
</tr>
</tbody>
</table>






​



By the way, any inputs in your conf file that are accepting data from a Lumberjack shipper need to use the Lumberjack input filter. While it may be tempting to receive logs over UDP due to reduce overhead, I highly recommend using TCP. UDP is great if you don't care if you actually receive it or not, but we want to receive all of our logs. TCP is a better choice in this case for better accounting since it will attempt to resend data that wasn't acknowledged to have been received.

Also, here is a sample config for /etc/logstash/logstash.conf:









<table cellpadding="0" cellspacing="0" border="0" >Logstash Conf
<tbody >
<tr >

<td >


1




2




3




4




5




6




7




8




9




10




11




12




13




14




15




16




17




18




19




20




21




22




23




24




25




26




27




28




29




30




31




32




33




34




35




36




37




38




39




40




41




42




43




44




45




46




47




48




49




50




51




52




53




54




55




56




57




58




59




60




61




62




63




64




65




66




67




68




69




70




71




72




73




74




75




76




77




78




79




80




81




82




83




84




85




86




87




88




89




90




91




92




93




94




95




96




97




98




99




100




101




102




103




104




105




106




107




108




109




110




111




112




113




114




115




116




117




118




119




120




121




122




123




124




125




126




127

</td>

<td >





`input {`




`  ``lumberjack {`




`    ``port => 5000`




`    ``type` `=> syslog`




`    ``ssl_certificate => ``"/etc/ssl/logstash.pub"`




`    ``ssl_key => ``"/etc/ssl/logstash.key"`




`  ``}`




`  ``lumberjack {`




`    ``type` `=> ``"nginx_access"`




`    ``port => 5001`




`    ``ssl_certificate => ``"/etc/ssl/logstash.pub"`




`    ``ssl_key => ``"/etc/ssl/logstash.key"`




`  ``}`




`  ``lumberjack {`




`    ``type` `=> ``"varnishncsa"`




`    ``port => ``"5002"`




`    ``ssl_certificate => ``"/etc/ssl/logstash.pub"`




`    ``ssl_key => ``"/etc/ssl/logstash.key"`




`  ``}`




`}`




 




`filter {`




`  ``# Lumberjack sends custom fields. We're going to use those for multi-user`




`  ``# Kibana access control.`




`  ``mutate {`




`    ``add_tag => [ ``"%{customer}"` `]`




`  ``}`




`  ``mutate {`




`    ``remove => [ ``"customer"` `]`




`  ``}`




 




`  ``# strip the syslog PRI part and create facility and severity fields.`




`  ``# the original syslog message is saved in field %{syslog_raw_message}.`




`  ``# the extracted PRI is available in the %{syslog_pri} field.`




`  ``#`




`  ``# You get %{syslog_facility_code} and %{syslog_severity_code} fields.`




`  ``# You also get %{syslog_facility} and %{syslog_severity} fields if the`




`  ``# use_labels option is set True (the default) on syslog_pri filter.`




`  ``grok {`




`    ``type` `=> ``"syslog"`




`    ``pattern => [ ``"<%{POSINT:syslog_pri}>%{SPACE}%{GREEDYDATA:message_remainder}"` `]`




`    ``add_tag => ``"got_syslog_pri"`




`    ``add_field => [ ``"syslog_raw_message"``, ``"%{@message}"` `]`




`  ``}`




`  ``syslog_pri {`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_pri"` `]`




`  ``}`




`  ``mutate {`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_pri"` `]`




`    ``replace => [ ``"@message"``, ``"%{message_remainder}"` `]`




`  ``}`




`  ``mutate {`




`    ``# XXX must not be combined with replacement which uses same field`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_pri"` `]`




`    ``remove => [ ``"message_remainder"` `]`




`  ``}`




 




`  ``# strip the syslog timestamp and force event timestamp to be the same.`




`  ``# the original string is saved in field %{syslog_timestamp}.`




`  ``# the original logstash input timestamp is saved in field %{received_at}.`




`  ``grok {`




`    ``type` `=> ``"syslog"`




`    ``pattern => [ ``"%{SYSLOGTIMESTAMP:syslog_timestamp}%{SPACE}%{GREEDYDATA:message_remainder}"` `]`




`    ``add_tag => ``"got_syslog_timestamp"`




`    ``add_field => [ ``"received_at"``, ``"%{@timestamp}"` `]`




`  ``}`




`  ``mutate {`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_timestamp"` `]`




`    ``replace => [ ``"@message"``, ``"%{message_remainder}"` `]`




`  ``}`




`  ``mutate {`




`    ``# XXX must not be combined with replacement which uses same field`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_timestamp"` `]`




`    ``remove => [ ``"message_remainder"` `]`




`  ``}`




`  ``date` `{`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_timestamp"` `]`




`    ``# season to taste for your own syslog format(s)`




`    ``syslog_timestamp => [ ``"MMM  d HH:mm:ss"``, ``"MMM dd HH:mm:ss"``, ``"ISO8601"` `]`




`  ``}`




 




`  ``# strip the host field from the syslog line.`




`  ``# the extracted host field becomes the logstash %{@source_host} metadata`




`  ``# and is also available in the filed %{syslog_hostname}.`




`  ``# the original logstash source_host is saved in field %{logstash_source}.`




`  ``grok {`




`    ``type` `=> ``"syslog"`




`    ``pattern => [ ``"%{SYSLOGHOST:syslog_hostname}%{SPACE}%{GREEDYDATA:message_remainder}"` `]`




`    ``add_tag => [ ``"got_syslog_host"``, ``"%{syslog_hostname}"` `]`




`    ``add_field => [ ``"logstash_source"``, ``"%{@source_host}"` `]`




`  ``}`




`  ``mutate {`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_host"` `]`




`    ``replace => [ ``"@source_host"``, ``"%{syslog_hostname}"` `]`




`    ``replace => [ ``"@message"``, ``"%{message_remainder}"` `]`




`  ``}`




`  ``mutate {`




`    ``# XXX must not be combined with replacement which uses same field`




`    ``type` `=> ``"syslog"`




`    ``tags => [ ``"got_syslog_host"` `]`




`    ``remove => [ ``"message_remainder"` `]`




`  ``}`




 




`  ``grok {`




`    ``type` `=> ``"nginx_access"`




`    ``pattern => ``"%{COMBINEDAPACHELOG}"`




`  ``}`




`  ``grok {`




`    ``type` `=> ``"varnishncsa"`




`    ``pattern => ``"%{COMBINEDAPACHELOG}"`




`  ``}`




`}`




 




`output {`




`#  stdout { debug => true debug_format => "json" }`




 




`  ``elasticsearch {`




`    ``cluster => ``"my-logstash-cluster"`




`  ``}`




`}`




</td>
</tr>
</tbody>
</table>






By the way, you'll notice that a lot of the filters have "type" set, as well as the inputs. This is so you can limit which type a filter is acting on. Additionally, you can add multiple outputs that send only certain types to different destinations. For example, you might want to send Varnish and Nginx logs to statsd in addition to Elasticsearch, or maybe you want to send certain events [to PagerDuty](http://logstash.net/docs/1.1.9/outputs/pagerduty), or perhaps even [to IRC](http://logstash.net/docs/1.1.9/outputs/irc). There are currently [over 45 outputs](http://logstash.net/docs/1.1.9/) that come with Logstash, and if what you need isn't there you can always write your own. By the way, Lumberjack isn't the only input; it's just the one we're focusing on here.

Also, the above logstash.conf assumes that you have Grok installed and that you're running on a multicast-enabled network. Also, don't get discouraged waiting for Logstash to start. It typically takes 70-90 seconds in my experience, although I've seen it take up to two minutes to start up and fully connect to Elasticsearch. I banged my head against the wall my first day with Logstash trying to figure out what was wrong - and then I went and got another cup of coffee and returned back to see it finally running.


## Start Shipping with Lumberjack


I know, you're probably thinking one of two things at this point.



	
  1. Logstash can ship logs, too.

	
  2. Rsyslog can ship logs and ships with most distros.


And you're right. But when you're shipping a lot of logs, especially on a system with limited resources, you need a lightweight solution that does nothing but ship. Enter Lumberjack.

Remember, Logstash is Java. Running as a shipper, its probably going to be using a significant part of your memory. While that might be acceptable on a 32GB server, if your deployment consists of mostly 512MB or 1GB cloud servers for web nodes then you're giving up a significant chunk of resources that can be put to better use for other tasks.

The next option might be to ship using Rsyslog. But even then, I've seen that grow to over 60MB of memory usage, and I've heard people talk about over 100MB of memory usage. For shipping logs.

Both of those scenarios are unacceptable to me. Lumberjack uses a whopping 64K in my experience. (For reference, the most busy server I have at this moment generates >1M entries per day from the Nginx logs.)

First, you'll need to clone the repo, and then it's just the usual "make". However, to make my life a little easier, I like to use a neat little Rubygem called "fpm". And with that, I run  "make deb" after running "make" and it gives me a Debian package that I can reuse over and over again.









<table cellpadding="0" cellspacing="0" border="0" >Install Lumberjack
<tbody >
<tr >

<td >


1




2




3




4




5




6




7




8

</td>

<td >





`apt-get ``install` `rubygems`




`gem ``install` `fpm`




`export` `PATH=$PATH:``/var/lib/gems/1``.8``/bin`




`git clone https:``//github``.com``/jordansissel/lumberjack``.git`




`cd` `lumberjack`




`make`




`make` `deb`




`dpkg -i lumberjack_0.0.8_amd64.deb`




</td>
</tr>
</tbody>
</table>






So now that we have Lumberjack, how do we run it? Basically, you just need to tell Lumberjack what files to ship to Logstash. The basic command is "/opt/lumberjack/bin/lumberjack --host your.logstash.host --port port-for-these-logs --ssl-ca-path /etc/ssl/logstash.pub /path/to/your.log /path/to/your/other.log". You can also pass custom fields using "--field key=value". (The custom fields can be useful in a multi-user Kibana environment.)

To make my life easier, I run the following init script:









<table cellpadding="0" cellspacing="0" border="0" >Lumberjack Init.d Script
<tbody >
<tr >

<td >


1




2




3




4




5




6




7




8




9




10




11




12




13




14




15




16




17




18




19




20




21




22




23




24




25




26




27




28




29




30




31




32




33




34




35




36




37




38




39




40




41




42




43




44




45




46




47




48




49




50




51




52




53




54




55




56




57




58




59




60




61




62




63




64




65




66




67




68




69

</td>

<td >





`#! /bin/sh`




`# BEGIN INIT INFO`




`# Provides:          lumberjack-nginx`




`# Required-Start:    $remote_fs $syslog`




`# Required-Stop:     $remote_fs $syslog`




`# Default-Start:     2 3 4 5`




`# Default-Stop:      0 1 6`




`# Short-Description: Start daemon at boot time`




`# Description:       Enable service provided by daemon.`




`### END INIT INFO`




 




`. ``/lib/lsb/init-functions`




 




`name=``"lumberjack-nginx"`




 




`# Read configuration variable file if it is present`




`[ -r ``/etc/default/``$name ] && . ``/etc/default/``$name`




 




`lumberjack_bin=``"/opt/lumberjack/bin/lumberjack.sh"`




`pid_file=``"/var/run/$name.pid"`




`cwd=```pwd````




 




`start () {`




`        ``command``=``"${lumberjack_bin}"`




 




`        ``if` `start-stop-daemon --start --quiet --oknodo --pidfile ``"$pid_file"` `-b -m -N 19 --``exec` `$``command` `-- $LUMBERJACK_OPTIONS; ``then`




`                ``log_end_msg 0`




`        ``else`




`                ``log_end_msg 1`




`        ``fi`




`}`




 




`stop () {`




`        ``start-stop-daemon --stop --quiet --oknodo --pidfile ``"$pid_file"`




`}`




 




`status () {`




`        ``status_of_proc -p $pid_file ``""` `"$name"`




`}`




 




`case` `$1 ``in`




`        ``start)`




`                ``if` `status; ``then` `exit` `0; ``fi`




`                ``echo` `-n ``"Starting $name: "`




`                ``start`




`                ``echo` `"$name."`




`                ``;;`




`        ``stop)`




`                ``echo` `-n ``"Stopping $name: "`




`                ``stop`




`                ``echo` `"$name."`




`                ``;;`




`        ``restart)`




`                ``echo` `-n ``"Restarting $name: "`




`                ``stop`




`                ``sleep` `1`




`                ``start`




`                ``echo` `"$name."`




`                ``;;`




`        ``status)`




`                ``status && ``exit` `0 || ``exit` `$?`




`                ``;;`




`        ``*)`




`                ``echo` `"Usage: $0 {start|stop|restart|status}"`




`                ``exit` `1`




`                ``;;`




`esac`




 




`exit` `0`




</td>
</tr>
</tbody>
</table>






You might have noticed it's called "lumberjack-nginx". That's because I run a separate daemon for shipping different log formats, such as MySQL, Varnish, Syslog, etc. The options are defined in /etc/default/lumberjack-nginx (or whatever you're shipping.)









<table cellpadding="0" cellspacing="0" border="0" >Lumberjack Options File
<tbody >
<tr >

<td >


1

</td>

<td >





`LUMBERJACK_OPTIONS=``"--field customer=clientname --host 172.16.0.52 --port 5001 --ssl-ca-path /etc/ssl/logstash.pub /var/log/nginx/access.log"`




</td>
</tr>
</tbody>
</table>








## Bonus: Kibana


Kibana is awesome. No really. It's a really great front-end for Elasticsearch and makes analyzing your logs much, much more enjoyable. It's super simple to install, too.

First, you'll need to clone the repo: git clone --branch=kibana-ruby [https://github.com/rashidkpc/Kibana.git](https://github.com/rashidkpc/Kibana.git)









<table cellpadding="0" cellspacing="0" border="0" >Install Kibana
<tbody >
<tr >

<td >


1




2




3




4




5

</td>

<td >





`git clone --branch=kibana-ruby https:``//github``.com``/rashidkpc/Kibana``.git ``/opt/kibana`




`apt-get ``install` `rubygems libcurl4-openssl-dev`




`export` `PATH=$PATH:``/var/lib/gems/1``.8``/bin`




`cd` `/opt/kibana`




`bundle ``install`




</td>
</tr>
</tbody>
</table>






By the way, there's also a "kibana-ruby-auth" branch for multiuser installs. It seems to work pretty good from my testing, although there have been several bugs in the interface. At last checkout, there is an issue with the "Home" button linking to "index.html" which needs to be changed to "/". I'm not up to speed on Ruby, so I don't know what the "right" fixes are, but I get by.

You'll also need to install the following packages:

apt-get install rubygems libcurl4-openssl-dev

If you want to play with the multiuser install (not recommended for production at this time), you'll also need the libpam0g-dev package.

After that, you just need to make some minor changes to KibanaConfig.rb (such as pointing it to the right Elasticsearch server and making sure Kibana will listen on the right ports) and run "ruby kibana.rb", or use this handy init script:









<table cellpadding="0" cellspacing="0" border="0" >Kibana Init Script
<tbody >
<tr >

<td >


1




2




3




4




5




6




7




8




9




10




11




12




13




14




15




16




17

</td>

<td >





`#!/bin/bash`




 




`# Author - Matt Mencel - 2012`




`# Borrowed heavily from Patrick McKenzie's example`




`# [http://www.kalzumeus.com/2010/01/15/deploying-sinatra-on-ubuntu-in-which-i-employ-a-secretary/](http://www.kalzumeus.com/2010/01/15/deploying-sinatra-on-ubuntu-in-which-i-employ-a-secretary/)`




`#`




`# kibana-daemon       Startup script for the Kibana Web UI running on sinatra`




`# chkconfig: 2345 20 80`




`#`




`## Copy to /etc/init.d/kibana and make it executable`




`## Add to system startup through chkconfig (CentOS/RHEL) or Upstart (Ubuntu)`




`KIBANA_PATH=``"/opt/kibana"`




 




`ruby1.8 $KIBANA_PATH``/kibana-daemon``.rb $1`




`RETVAL=$?`




 




`exit` `$RETVAL`




</td>
</tr>
</tbody>
</table>











## So... what does this have to do with Drupal?


It will make your Drupal logging life easier.

No, really. I'm sure that most people have their Drupal site send all log messages to the database, and that's fine if you have a low traffic site. But what about when you need to actually search for something, such as the messages associated with a particular issue your users are sporadically experiencing? If you've ever tried to attempt that through the DB log, you probably realize how painful it can be.

Why not take advantage of another built-in feature of Drupal? Drupal can send its log messages to syslog (via the core Syslog module). If you want to go to a specific file only for Drupal, [there's a contrib module for that](http://drupal.org/project/flog). With Drupal logging to the filesystem, you take a load off of the database and you're able to easily ship your logs elsewhere. They can be sent to be indexed in Elasticsearch for easy search and analysis through Kibana, or to several other places such as Graphite, Email, IRC, PagerDuty, etc. You get to decide how and where your logs are processed.

And if you have the spare RAM and aggressive log rotation, you could just send your logs to tmpfs and avoid disk load altogether. After all, you are shipping them out to be stored elsewhere.

Even if you can't log to a file, Logstash can help. Logstash has an [input for Drupal's DBLog](http://logstash.net/docs/1.1.9/inputs/drupal_dblog). All you have to do is tell it what database to connect to and it handles the rest. It only pulls the log messages that it hasn't received previously. This can be useful if you don't want to deal with configuring Drupal to log to Syslog or another file, or if you're one of those crazy types that runs multiple web heads without configuration management to ensure that everyone is shipping file-based logs properly and consistently.

One situation where I've found shipping Drupal logs useful is debugging. A good Drupal module logs errors (and optional debugging messages) through watchdog. If you're just leaving those watchdogs in DBLog or syslog, you've got a tough time ahead of you searching for issues. If you're having your watchdogs shipped through Logstash, you can have them indexed in Elasticsearch and search through the messages, or you can have them graphed by statsd to see if there's a trend for when your users are hitting errors, or you can have your developers nagged in IRC.

Another useful situation is easily tracking logins and user signups. Now you're able to easily build charts showing when users are signing up or how often they are logging in, as well as figure out if someone is setting up multiple fake accounts.





