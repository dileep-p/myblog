---
author: vishnur66
comments: false
date: 2013-03-14 11:48:56+00:00
layout: post
link: http://www.vishnu-tech.com/2013/03/setting-up-keepalived-load-balancing-using-haproxy-part-2/
slug: setting-up-keepalived-load-balancing-using-haproxy-part-2
title: Setting up keepalived-Load balancing using HAProxy Part 2
wordpress_id: 202
categories:
- HA PROXY
tags:
- HA Proxy
- High Availability
- load balancing
---

In our previous post we have set up a HAProxy loadbalancer to balance the load of our web application between three webservers, here’s the diagram of the situation we have ended up with:

[![3](https://www.vishnu-tech.com/wp-content/uploads/2013/03/3.png?w=584)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/3.png)

As we already concluded in the last post, there’s still a single point of failure in this setup. If the loadbalancer dies for some reason the whole site will be offline. In this post we will add a second loadbalancer and setup a virtual IP address shared between the loadbalancers. The setup will look like this:

[![5](https://www.vishnu-tech.com/wp-content/uploads/2013/03/5.png?w=584)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/5.png)

So our setup now is:
- Three webservers, WebServer1 (192.168.0.1), WebServer2 (192.168.0.2 ), and WebServer3 (192.168.0.3) each serving the application
- The first load balancer (loadb01, ip: (192.168.0.100 ))
- The second load balancer (loadb02, ip: (192.168.0.101 )), configure this in the same way as we configured the first one.

To setup the virtual IP address we will use keepalived






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





**`loadb01$ ``sudo` `apt-get ``install` `keepalived`**




</td>
</tr>
</tbody>
</table>






Good, keepalived is now installed. Before we proceed with configuring keepalived itself, edit the following file:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`loadb01$ ``sudo` `vi` `/etc/sysctl``.conf`




</td>
</tr>
</tbody>
</table>






And add this line to the end of the file:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`net.ipv4.ip_nonlocal_bind=1`




</td>
</tr>
</tbody>
</table>






This option is needed for applications (haproxy in this case) to be able to bind to non-local addresses (ip adresses which do not belong to an interface on the machine). To apply the setting, run the following command:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`loadb01$ ``sudo` `sysctl -p`




</td>
</tr>
</tbody>
</table>






Now let’s add the configuration for keepalived, open the file:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`loadb01$ ``sudo` `vi` `/etc/keepalived/keepalived``.conf`




</td>
</tr>
</tbody>
</table>






And add the following contents
<table cellpadding="0" cellspacing="0" border="0" >
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

</td>

<td >





`# Settings for notifications`




`global_defs {`




`    ``notification_email {`




`        ``your@emailaddress.com     ``# Email address for notifications `




`    ``}`




`    ``notification_email_from loadb01@domain.ext  ``# The from address for the notifications`




`    ``smtp_server 127.0.0.1     ``# You can specifiy your own smtp server here`




`    ``smtp_connect_timeout 15`




`}`




` ` 




`# Define the script used to check if haproxy is still working`




`vrrp_script chk_haproxy { `




`    ``script ``"killall -0 haproxy"`




`    ``interval 2 `




`    ``weight 2 `




`}`




` ` 




`# Configuation for the virtual interface`




`vrrp_instance VI_1 {`




`    ``interface eth0`




`    ``state MASTER        ``# set this to BACKUP on the other machine`




`    ``priority 101        ``# set this to 100 on the other machine`




`    ``virtual_router_id 51`




` ` 




`    ``smtp_alert          ``# Activate email notifications`




` ` 




`    ``authentication {`




`        ``auth_type AH`




`        ``auth_pass myPassw0rd      ``# Set this to some secret phrase`




`    ``}`




` ` 




`    ``# The virtual ip address shared between the two loadbalancers`




`    ``virtual_ipaddress {`




`        ``192.168.0.200`




`    ``}`




`    `




`    ``# Use the script above to check if we should fail over`




`    ``track_script {`




`        ``chk_haproxy`




`    ``}`




`}`




</td>
</tr>
</tbody>
</table>
And start keepalived:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`loadb01$ ``/etc/init``.d``/keepalived` `start`




</td>
</tr>
</tbody>
</table>






Now the next step is to install and configure keepalived on our second loadbalancer aswell, redo the steps starting from apt-get install keepalived. In the configuration step for keepalived, be sure change these two settings:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1




2

</td>

<td >





`state MASTER        ``# set this to BACKUP on the other machine`




`priority 101        ``# set this to 100 on the other machine`




</td>
</tr>
</tbody>
</table>






To:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1




2

</td>

<td >





`state BACKUP      `




`priority 100      `




</td>
</tr>
</tbody>
</table>






That’s it! We have now configured a virtual IP shared between our two loadbalancers, you can try loading the haproxy statistic page on the virtual IP adddress and should get the statistics for loadb01, then switch off loadb01 and refresh, the virtual IP address will now be assigned to the second loadbalancer and you should see the statistics page for that.

If there’s anything else you’d like us to cover, or if you have any questions please leave a comment!




<table cellpadding="0" width="222" cellspacing="0" border="0" >
<tbody >
<tr >

<td >
</td>

<td >
</td>
</tr>
</tbody>
</table>
