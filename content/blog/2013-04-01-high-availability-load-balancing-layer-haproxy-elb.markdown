---
author: vishnur66
comments: false
date: 2013-04-01 07:23:25+00:00
layout: post
link: http://www.vishnu-tech.com/2013/04/high-availability-load-balancing-layer-haproxy-elb/
slug: high-availability-load-balancing-layer-haproxy-elb
title: High Availability @ Load Balancing Layer-HAProxy / ELB
wordpress_id: 217
categories:
- HA PROXY
tags:
- amazon
- ec2
- ELB
- HA Proxy
- High Availability
- load balancing
- Route53
---

Architecting High Availability at the Load Balancing layer is one of the important aspects in the web scale systems in AWS. We can follow multiple strategies for achieving the same. I am listing some of the designs for achieving the same.


# _Pattern 1: Route 53 DNS RR + HAProxy_


Route53 is a Managed DNS service provided by Amazon Web Services. Route 53 supports Round robin and weighted algorithms. If the Route53 DNS server has several entries for a given hostname, it will return all of them in a rotating order. This way, various users will see different addresses for the same name and will be able to reach different EC2 instances in LB Tier.


$ host -t a HAProxyTestXYZ.com




HAProxyTestXYZ.com. has address **50.19.82.183 (Primary EIP)**


HAProxyTestXYZ.com. has address **23.23.174.254 (Secondary EIP)**

[![1](https://www.vishnu-tech.com/wp-content/uploads/2013/04/1.jpg)](https://www.vishnu-tech.com/wp-content/uploads/2013/04/1.jpg)


Example, if we attach the Elastic IP’s of 2 HAProxy EC2 instances under the Route 53, both the IP’s are sent to the user browsers by the Route 53 DNS. In case, the algorithm configured is Round Robin at the Route 53 level, then browser- 1 will get **EIP-1(50.19.82.183**) of HAProxy-1 as the primary IP and browser -2 will get **EIP-2** **(23.23.174.254)** of HAProxy-2 as the primary IP in rotation basis.  The browser- 1 will contact the HAProxy-1 and in case HAProxy-1 is not reachable it will contact the secondary EIP which is HAproxy-2 and so forth. This is an age old technique generally used by search engines, content servers (or) web scale systems for achieving scalability in LB layer. But this method does not provide any means of High availability @ LB layer. It requires additional measures to permanently check the HAProxy EC2 LB instances status and switch a failed EC2 instance EIP to another HAProxy EC2 LB. For this reason, this pattern is generally used as a complementary solution in High Availability, not as a primary one.  For achieving better stability at this layer in AWS, I usually recommend having 2 or more HAProxies distributed on multiple AZ’s inside the Amazon EC2 region. This way if one of the HAProxy is down, the website still functions with the help of other HAProxies and even if the entire Amazon EC2 AZ is down still the HAProxies in the other AZ can handle the requests and keep the website active. Some load tests have proven that HAProxy on m1.large EC2 instance can handle close to ~4500+ HTTP requests/second. So depending upon the number of concurrent requests/sec needed on your application you can go ahead and attach multiple HAProxy EC2 instances to the Route 53. Now that we achieved availability horizontally using the Route53 DNS Round Robin in HAProxy layer let us try to understand the intricacies behind this architecture.  Since we now have 2 or more HAProxies what will happen to the contextual web sessions data that resides in the application servers. HAProxies need to know in which application server the session data of the user resides else the requests will have authorization failures.







There are 2 architecture designs we can follow for solving this contextual problem they are:







**Stateless Application design: ** This is the recommended and widely used design. The web session data is separated out from the Web/App server memory and they are kept in common cache stores like MemCacheD, TerraCotta etc.Since the session data is now kept in a common store like MemCacheD, HAProxies can direct their requests to any of the web/app servers attached under it without knowing where the session state is mapped. Whenever any web/app server receives the request from any of the HAProxies, it will validate and authorize the session data from the common store.  In event of any HAProxy or Web/App EC2 failure still the website functions without problems because other HAProxies and Web/App servers are still able to handle the subsequent requests. Thus we achieve availability and scalability on the HAProxy/Load Balancing layer following this model.







**Sticky Application design:** Things are usually not ideal and the way we assume to be in real world. Some applications are still designed with stateful nature and they store the session data, cache data etc. in their web/app server memory.  We can always recommend the application teams to re-architect this model to stateless, but not always this suggestion works for short term migrations, inter dependencies etc.  So as architects we need to find way to live and cope up with this design and still try to achieve availability on the load balancing layer. HAProxy follows a technique called as “**Cookie Learning”** and **“Cookie Insertion”** to help state full applications. HAProxy can be configured to learn the application cookie ("JSESSIONID"), when HAProxy receives the user's request, it checks if it contains this particular cookie and a known value. If this is not the case, it will direct the request to any Web/App EC2 server, according to the load balancing algorithm configured. HAProxy will then extract the cookie value from the response and add it along with the server's identifier to a local table. When the user request comes back again, the load balancer sees the cookie, lookups the table and finds the Web/App EC2 server to which it forwards the request. Let me detail this important flow a little bit;







HAProxy-1 EC2 instance will receive client’s requests from the browser. If a request does not contain a cookie, it will be forwarded to a valid Web/App EC2 Instance Apache-A. In return, if a JESSIONID cookie is seen, the Web/App EC2 Instance name (Example “A”)will be prefixed into it, followed by a delimiter ('~') like "JSESSIONID=A~xxx".When the browser client requests again with the cookie  JSESSIONID=A~xxx", HAProxy-1 will know that it must be forwarded to Web/App Instance Apache-A. The EC2 Instance name ”A” will then be extracted from cookie before it is sent to the Web/App EC2 Instance Apache-A.







If Web/App EC2 Instance Apache-A dies, then requests will be sent to another valid server Web/App EC2 Instance Apache-B by LB and the cookie will be reassigned.







If HAProxy-1 itself dies, then requests will be sent to HAProxy-2 which will identify the Web/App EC2 instance to forward the request. This way even if the subsequent requests moves from HAProxy-1 to HAProxy-2 in event of HAProxy-1 failure, still the requests are sent to the same Web/App instance Apache-A by the cookie learning/insertion mechanism of HAProxy.










Sample HAProxy Settings to achieve this



    
    <span style="color:#0000ff;">listen webfarm 192.168.1.1:80</span>
    <span style="color:#0000ff;">       mode http</span>
    <span style="color:#0000ff;">       balance roundrobin</span>
    <span style="color:#0000ff;">       cookie JSESSIONID prefix</span>
    <span style="color:#0000ff;">       option httpclose</span>
    <span style="color:#0000ff;">       option forwardfor</span>
    <span style="color:#0000ff;">       option httpchk HEAD /index.html HTTP/1.0</span>
    <span style="color:#0000ff;">       server Apache-A 192.168.1.11:80 cookie A check</span>
    <span style="color:#0000ff;">       server Apache-B 192.168.1.12:80 cookie B check</span>


Note: You can use more sophisticated DNS services like UltraDNS , DNSMadeEasy etc also in this architecture to better control the Load balancing and traffic direction at the DNS level.

Pattern 2: Route 53 DNS RR + HAProxy in Active-Passive mode


This is an extension of the Route53 DNS RR pattern and everything discussed in the previous pattern still applies to this context. In addition to associating HAProxies horizontally under Route53, we will build availability for every HAProxy vertically as well in this pattern. High Availability is built taking into consideration HAProxy process failure and HAProxy EC2 instance failure.







2 or more HAProxies from multiple AZ’s are taken and they are attached with Amazon Elastic IP’s. These Elastic IP’s are then associated in Route 53 with DNS RR. These HAProxies are now “Active” and are ready to handle the user requests. For HA, another equivalent set of HAProxies are launched in the respective AZ’s as “Standby”. In event of the “Active” HAProxy failure, the Standby HAProxy remaps to the same Amazon Elastic IP takes over the subsequent requests from the client.




[![2](https://www.vishnu-tech.com/wp-content/uploads/2013/04/2.jpg)](https://www.vishnu-tech.com/wp-content/uploads/2013/04/2.jpg)







In the above diagram, there are 2 HAProxies in “Active” state with Elastic IP’s **50.19.82.183** **&** **23.23.174.254**. They are deployed across Multiple Availability Zones inside an Amazon EC2 region. Another 2 HAProxies are launched in respective AZ’s, but they are kept idle in “Standby” state. In event of **HAProxy-1 (EIP: 50.19.82.183)** failure the Elastic IP is remapped to Standby HAProxy-3 in the same AZ. The remapping takes ~60 seconds and the HAPorxy-3 will be handling the subsequent requests directed by the browsers to the **50.19.82.183**** IP.**







Broadly there are 2 levels of failure in this pattern;







**Failure @ HAProxy Process level**







**Failure @ HAProxy EC2 instance level**




[![3](https://www.vishnu-tech.com/wp-content/uploads/2013/04/3.jpg)](https://www.vishnu-tech.com/wp-content/uploads/2013/04/3.jpg)
















 **Failure @HAProxy Process level:**  When HAProxy Process at the “Active” server fails; we can detect this using **KeepAliveD **and switch the Elastic IP from Active -> Standby. We have observed it takes ~60-120 seconds for the standby to takeover. During this time the **particular HAProxy alone** will be unreachable. KeepAliveD script is configured in both the Active and standby HAProxy EC2 instance. KeepAliveD implements a set of checkers to dynamically and adaptively maintain and manage load balanced server pool according to their health. High availability is achieved by Virtual Router Redundancy Protocol [VRRP](http://datatracker.ietf.org/wg/vrrp/) protocol of the KeepAliveD. Since Amazon EC2 currently does not support Multicast protocol we need to configure KeepAliveD with Unicast TCP in this scenario.  For more details refer [http://www.keepalived.org/](http://www.keepalived.org/). Mean time manually we can bring the failed HAProxy Process up and make this as the new standby.










**Script Name: “/etc/keepalived/keepalived.conf”**










vrrp_script chk_haproxy {           # Requires keepalived-1.1.13




script "killall -0 haproxy"     # cheaper than pidof




interval 20                      # check every 2 seconds




weight 20                        # add 2 points of prio if OK




}




vrrp_instance VI_1 {




interface eth0




state MASTER




virtual_router_id 51




ipriority 101                    # 101 on master, 100 on backup




vrrp_unicast_bind 10.215.31.4




      #internal IP address of EC2 instance 01




vrrp_unicast_peer 10.85.110.252




  #internal IP address of EC2 instance 02




notify_master "/etc/keepalived/vrrp.sh"




track_script {




chk_haproxy weight 20




}




 




}







**Script Name: /etc/keepalived/vrrp.sh**










#vrrp.sh




#!/bin/bash




cd /opt/aws/apitools/ec2/bin




#DisAssociate EIP from this instance.




./ec2-disassociate-address --aws-access-key XXXXXXX –aws-secret-key XXXXXXX  [EIP]




#Mapping EIP to secondary server




./ec2-associate-address --aws-access-key XXXXXXX  –aws-secret-key XXXXXXX  [EIP] -i [ec2_instance_id_of_primary_or_secondary]
















**Failure @ HAProxy EC2 instance level: **When the Active HAProxy EC2 instance itself fails; we can detect this using **Heartbeat **and switch the Elastic IP from Active -> Standby. **"[Heartbeat](http://en.wikipedia.org/wiki/Heartbeat_%28program%29)"** tool connects two servers and checks the regular "pulse" or "heartbeat" between them. The standby server takes over the work of the “Active” as soon as it detects an alteration in the "heartbeat" of the former. We have observed it takes ~120+ seconds for the standby to takeover. During this time the **particular HAProxy alone** will be unreachable. Heartbeat has to be configured in both the Active and standby HAProxy EC2 instance. Since Amazon EC2 currently does not support Multicast protocol we need to configure Heartbeat with Unicast UDP in this scenario. Mean time manually we can bring the failed HAProxy EC2 instance up and make this as the new standby.







**Script Name: /etc/ha.d/ha.cf**










logfile /var/log/ha-log




logfacility local0




keepalive 2




deadtime 30




initdead 120




udpport 694




ucast eth0 xx.xxx.xxx.xxa #Internal IP of EC2 instance 01




ucast eth0 xx.xxx.xxx.xxb #Internal IP of EC2 instance 02




auto_failback off










**Script Name: Create a script named “elastic_ip” in both the servers.**










#!/bin/bash




I_ID="[ec2_instance_id" # different for each EC2 servers.




ELASTIC_IP="X.X.X.X"




case $1 in




    start)




ec2-associate-address --aws-access-key XXXXX --aws-secret-key XXXXX "$ELASTIC_IP" -i "$I_ID" > /dev/null




       echo $0 started




       ;;




    stop)




ec2-disassociate-address --aws-access-key XXXXX --aws-secret-key XXXXX "$ELASTIC_IP" > /dev/null




    echo $0 stopped




       ;;




    status)




ec2-describe-addresses --aws-access-key XXXXX --aws-secret-key XXXXX | grep "$ELASTIC_IP" | grep "$I_ID" > /dev/null




    # grep will return true if this ip is mapped to this instance




    [ $? -eq 0 ] && echo $0 OK || echo $0 FAIL




    ;;




 




esac










**Why do we need this redundancy in the HAProxy layer?**










Not all the times the DNS RR with LB Cookie Insertion alone is enough for ensuring availability;







**Case 1:** Imagine you have not automated the scalability @ Load Balancing Layer and one of your Load balancer is down. You do not want to be waked up in the middle of the night rather it is better to have a standby Load Balancer automatically replacing the failed one. Manually you can replace the faulty LB next day.







**Case 2: **You have a gaming site where long running TCP sockets are established from flash gaming clients to the LB layer. You have planned the capacity of Front end Load Balancers with concurrent connections/sec. Now couple of your Load balancers are down, the new connections will be established to other running LB, but overall your site will now start performing poorly and chances are new connections are exhausted after few hours of heavy traffic. It is better to automatically detect and replace the faulty LB EC2 instance with the standby.







**Case 3: **Some clients cache the IP address of the Load Balancer, Some of them have long running sticky sessions with web/app, Some hardware devices can take only IP address to push data into the Server infrastructure. Though it is suggested to resolve the IP using DNS, still in reality some use cases does not work the same way.










**Pattern 3: Use ELB**







Do not worry about all the above patterns, just go and configure Amazon Elastic Load Balancing (ELB). For most of the use cases ELB is more than sufficient.




Amazon Elastic Load Balancer can distribute incoming traffic across your Amazon EC2 instances in a single Availability Zone or multiple Availability Zones. Amazon Elastic Load Balancing automatically scales its request handling capacity in response to incoming application traffic. It can handle 20k+ concurrent requests/sec with ease. It enables you to achieve even greater fault tolerance in your applications, seamlessly providing the amount of load balancing capacity needed in response to incoming application traffic. Elastic Load Balancing detects unhealthy instances within a pool and automatically reroutes traffic to healthy instances until the unhealthy instances have been restored. Any faulty Load balancers in the ELB tier are automatically replaced.




Though for most of the common use cases ELB is more than sufficient in AWS. There are some unique cases which demand the use of Load balancers like HAProxy, Nginx and NetScaler in our architecture in the AWS infrastructure.

































