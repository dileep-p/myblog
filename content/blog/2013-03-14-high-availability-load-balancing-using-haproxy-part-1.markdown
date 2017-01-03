---
author: vishnur66
comments: false
date: 2013-03-14 11:22:51+00:00
layout: post
link: http://www.vishnu-tech.com/2013/03/high-availability-load-balancing-using-haproxy-part-1/
slug: high-availability-load-balancing-using-haproxy-part-1
title: High availability load balancing using HAProxy PART-1
wordpress_id: 176
categories:
- HA PROXY
tags:
- HA Proxy
- High Availability
- load balancing
---

HAProxy is a free, **_very_** fast and reliable solution offering [high availability](http://en.wikipedia.org/wiki/High_availability), [load balancing ](http://en.wikipedia.org/wiki/Load_balancer)and proxying for TCP and HTTP-based applications. It is particularly suited for web sites crawling under very high loads while needing persistence or Layer7 processing. Supporting **tens of thousands** of connections is clearly realistic with todays hardware. Its mode of operation makes its integration into existing architectures very easy and riskless, while still offering the possibility not to expose fragile web servers to the Net,such as below:

[![haproxy-pmode](https://www.vishnu-tech.com/wp-content/uploads/2013/03/haproxy-pmode.png?w=300)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/haproxy-pmode.png)

In this post I will show you how to easily setup load balancing for your web application. Imagine you currently have your application on one webserver called Webserver1.

[![1](https://www.vishnu-tech.com/wp-content/uploads/2013/03/1.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/1.png)But traffic has grown and you’d like to increase your site’s capacity by adding more webservers (WebServer2 and WebServer3), aswell as eliminate the single point of failure in your current setup (if web01 has an outage the site will be offline).


[![2](https://www.vishnu-tech.com/wp-content/uploads/2013/03/21.png?w=584)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/21.png)


In order to spread traffic evenly over your three web servers, we could install an extra server to proxy all the traffic an balance it over the webservers. In this post we will use HAProxy, an open source TCP/HTTP load balancer. (see: http://haproxy.1wt.eu/) to do that:

[![3](https://www.vishnu-tech.com/wp-content/uploads/2013/03/3.png?w=584)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/3.png)

So our setup now is:
- Three webservers, WebServer1 (192.168.0.1), WebServer2 (192.168.0.2 ), and WebServer3 (192.168.0.3) each serving the application
- A new server (LoadBalancer-1, ip: (192.168.0.100 )) with Ubuntu installed.

Allright, now let’s get to work:

Start by installing haproxy on your loadbalancing machine:





**`LoadBalancer01$ ``sudo` `apt-get ``install` `haproxy`**










Now let’s backup the original haproxy configuration file and create a new one with our config which will tell haproxy to listen for incoming http requests on port 80 and balance them between the three webservers:










**`loadb01$ ``sudo` `mv` `/etc/haproxy/haproxy``.cfg ``/etc/haproxy/backup_haproxy``.cfg`**







**`loadb01$ ``sudo` `vi` `/etc/haproxy/haproxy``.cfg`**




**Paste the following configuration there:**





<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


**1**




**2**




**3**




**4**




**5**




**6**




**7**




**8**




**9**




**10**




**11**




**12**




**13**




**14**




**15**




**16**




**17**




**18**




**19**




**20**




**21**




**22**




**23**




**24**




**25**




**26**




**27**




**28**




**29**




**30**

</td>

<td >





**`global`**




**`        ``maxconn 4096`**




**`        ``user haproxy`**




**`        ``group haproxy`**




**`        ``daemon`**




** **




**`defaults`**




**`        ``log     global`**




**`        ``mode    http`**




**`        ``option  httplog`**




**`        ``option  dontlognull`**




**`        ``retries 3`**




**`        ``option  redispatch`**




**`        ``maxconn 2000`**




**`        ``contimeout      5000`**




**`        ``clitimeout      50000`**




**`        ``srvtimeout      50000`**




** **




**`listen webcluster *:80`**




**`        ``mode    http`**




**`        ``stats   ``enable`**




**`        ``stats   auth us3r:passw0rd`**




**`        ``balance roundrobin`**




**`        ``option httpchk HEAD / HTTP``/1``.0`**




**`        ``option forwardfor`**




**`        ``cookie LSW_WEB insert`**




**`        ``option httpclose`**




**`        ``server web01 192.168.0.1:80 cookie LSW_WEB01 check`**




**`        ``server web02 192.168.0.2:80 cookie LSW_WEB02 check`**




**`        ``server web03 192.168.0.3:80 cookie LSW_WEB03 check`**




</td>
</tr>
</tbody>
</table>














**``Enable HAproxy by editing the /etc/default/haproxy file``**``````````













**`loadb01$ ``sudo` `nano ``/etc/default/haproxy`**




and setting ENABLED to 1











<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1




2




3




4

</td>

<td >





`# Set ENABLED to 1 if you want the init script to start haproxy.`




`ENABLED=1`




`# Add extra flags here.`




`#EXTRAOPTS="-de -m 16"`




</td>
</tr>
</tbody>
</table>






Then, start HAProxy:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`loadb01$ ``sudo` `/etc/init``.d``/haproxy` `start`




</td>
</tr>
</tbody>
</table>






Now open your webbrowser and browse to http://192.168.0.100/ (or whatever IP you have set for loadb01), you should be served a file from one of the webservers! The loadbalancing is now working, but let’s take a closer look at some of the things we configured in the HAProxy configuration:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`listen webcluster *:80`




</td>
</tr>
</tbody>
</table>






Listen for incoming connections on all interfaces, port 80 (the * can also be replaced with a single ip address)






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1




2

</td>

<td >





`stats   ``enable`




`stats   auth us3r:passw0rd`




</td>
</tr>
</tbody>
</table>






This enables HAProxy’s statistics interface which you can access by browsing to http://192.168.0.100/haproxy?stats login with the username and password given and you should see a nice statistics report like this:


[![4](https://www.vishnu-tech.com/wp-content/uploads/2013/03/4.png?w=584)](https://www.vishnu-tech.com/wp-content/uploads/2013/03/4.png)


The first line in this block enables the use of cookies, basically, when a user reaches the webcluster group, the cookie LSW_WEB will be created and the server id (LSW_WEB01, LSW_WEB02, LSW_WEB03) will be stored in it. For all next requests in the same session, HAProxy will look at the cookie and redirect that user to the same webserver (unless it’s down).

The last three lines define the backend webservers which HAProxy will use, you can easily add more lines here as the infrastructure grows.

Allright the loadbalancing is working and we are almost there, just one thing left to do in this article and that’s fixing your webserver logs on the web01/web02/web03 servers. Since requests now changed from:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`user --> webserver`




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

</td>

<td >





`user --> HAProxy --> webserver`




</td>
</tr>
</tbody>
</table>






You will see the loadbalancer’s ip in the access log on your webservers. In order to fix this when you are using Apache webserver open your /etc/apache2/apache2.conf file and replace this line:






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`LogFormat ``"%h %l %u %t "%r" %>s %O "%{Referer}i" "%{User-Agent}i""` `combined`




</td>
</tr>
</tbody>
</table>






By






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1




2

</td>

<td >





`#LogFormat "%h %l %u %t "%r" %>s %O "%{Referer}i" "%{User-Agent}i"" combined`




`LogFormat ``"%{X-Forwarded-For}i %l %u %t "%r" %>s %b "%{Referer}i" "%{User-Agent}i""` `combined`




</td>
</tr>
</tbody>
</table>






Then restart/reload apache and the logging should be fixed, it will now include the IP address which is send in the X-Forwarded-For header (This header contains a value representing the client’s IP address.) that HAProxy includes in all requests to the backend webserver. We enabled that earlier by setting the






<table cellpadding="0" cellspacing="0" border="0" >
<tbody >
<tr >

<td >


1

</td>

<td >





`option forwardfor`




</td>
</tr>
</tbody>
</table>






option in the HAPRoxy configuration.

If there’s anything else you’d like to cover, or if you have any questions please leave a comment!




















