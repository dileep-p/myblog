---
author: vishnur66
comments: true
date: 2015-05-09 19:53:55+00:00
layout: post
link: http://www.vishnu-tech.com/2015/05/how-to-get-your-client-ipaddress-with-nginx-if-you-are-using-tcp-on-aws-elb/
slug: how-to-get-your-client-ipaddress-with-nginx-if-you-are-using-tcp-on-aws-elb
title: How to get your Client IPaddress with Nginx if you are using TCP on AWS ELB
wordpress_id: 468
categories:
- Amazon AWS
- Amazon EC2
tags:
- AWS
- aws ec2
- ELB
- load balancing
- log
- nginx
- proxy
---

If your Application is running behind an AWS Load Balancer ([ELB](http://aws.amazon.com/elasticloadbalancing/)) and you are using TCP ( for both 80 and 443).

You can run AWS ELB in different modes.

_1) You can do Load balance with HTTP/HTTPS traffic and if you use this one, the AWS ELB injects an X-Forwarded-For  header  that has the original client ipaddress. _



_2)  You can do Load balance straight with TCP traffic. If you use this mode, your application can use Websockets. But it won't provide the X-Forwarded-For  header because the load balancer doesn’t know anything about HTTP headers in this case. So it's hard to track the Client IP address. However we can do this with an AWS ELB feature called "[Proxy Protocol](http://aws.amazon.com/about-aws/whats-new/2013/07/30/elastic-load-balancing-now-supports-proxy-protocol/)" _

_ _

_**Here I am using Ubuntu distro with Nginx version 1.9.0 **_

[color-box color="green"]

_**sudo add-apt-repository ppa:chris-lea/nginx-devel**_

_**sudo apt-get update**_

_**sudo apt-get -y install nginx-full**_

[/color-box]



_Your AWS ELB Listeners Looks like the below one_

[![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/05/elb.png)](https://www.vishnu-tech.com/wp-content/uploads/2015/05/elb.png)



_**Now you need to enable Proxy Protocol on the AWS ELB**_

_First create a load balancer policy using the below command_

[color-box color="green"]

_aws elb create-load-balancer-policy --load-balancer-name vishnudxb  --policy-name vishnudxb-ProxyProtocol-policy --policy-type-name ProxyProtocolPolicyType --policy-attributes AttributeName=ProxyProtocol,AttributeValue=true_

[/color-box]

_Now we need to enable the Newly created policy on the specified ports. (ie 80 & 443)_

[color-box color="green"]

_aws elb set-load-balancer-policies-for-backend-server --load-balancer-name vishnudxb --instance-port 80 --policy-names vishnudxb-ProxyProtocol-policy_

_aws elb set-load-balancer-policies-for-backend-server --load-balancer-name vishnudxb --instance-port 443 --policy-names vishnudxb-ProxyProtocol-policy_

[/color-box]

_**Now we need to configure Nginx for Proxy Protocol**_

_Add the below entries on your **/etc/nginx/nginx.conf**_

[color-box color="green"]

_log_format elb_log '$proxy_protocol_addr - $remote_user [$time_local] ' '"$request" $status $body_bytes_sent "$http_referer" ' '"$http_user_agent"';_

[/color-box]

Now you need to do some changes on your _**sites-enabled like below:**_



![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/05/nginx1.png)





![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/05/nginx2.png)





_**And finally you can see the Client IP address on your Nginx logs  when you use AWS ELB TCP traffic straight. **_

[![vu](https://www.vishnu-tech.com/wp-content/uploads/2015/05/log.png)](https://www.vishnu-tech.com/wp-content/uploads/2015/05/log.png)
