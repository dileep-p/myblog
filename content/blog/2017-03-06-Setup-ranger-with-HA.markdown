---
author: Vishnu Nair
comments: true
date: 2017-03-06 19:43:25+00:00
layout: post
slug: Setup-ranger-with-HA
title: Setup Ranger with HA & running Kubernetes on top of it.
categories:
- Amazon AWS
- Amazon EC2
- Kubernetes
- Docker
- Ranger
tags:
- amazon
- AWS
- aws ec2
- container
- docker
- kubernetes
- ranger
- rangeros
- ec2
- linux
---

*In this blog, we are setting up a highly available Ranger cluster in AWS and running Kubernetes on top of it*


### Why Rancher?

*Rancher is a complete, open source platform for deploying and managing containers in production. It includes commercially-supported distributions of Kubernetes, Mesos, and Docker Swarm, making it easy to run containerized applications on any infrastructure.*

### Prerequisites:

* *Here I will be creating the AWS setup as below:*

![Ranger setup in AWS](/img/ranger-aws-setup.png)

* *Create 3 instance for Ranger Server*

![Ranger Server in AWS](/images/rancher-server.png)

* *Create 3 instance for Ranger Host*

![Ranger Host in AWS](/images/rancher-host.png)

* *Create ELB for Ranger Server*

![ELB for Ranger Server in AWS](/images/rancher-elb.png)

* *Put ELB Listeners as below:*

![ELB Listeners for Rancher Server](/images/rancher-elb-listeners.png)

* *Put ELB Healthcheck as below:*

![ELB Healthcheck for Rancher Server](/images/rancher-elb-healthcheck.png)

* *Once you setup the ELB, you need to enable the proxy protocol mode*

```
$ aws elb create-load-balancer-policy \
     --load-balancer-name rancher-elb \  
     --policy-name rancher-elb-policy \
     --policy-type-name ProxyProtocolPolicyType \
     --policy-attributes AttributeName=ProxyProtocol,AttributeValue=true \
     --region eu-west-1

```   


```

$ aws elb set-load-balancer-policies-for-backend-server \
      --load-balancer-name rancher-elb \
      --instance-port 80 \
      --policy-names rancher-elb-policy  \
      --region eu-west-1

```

```

$ aws elb set-load-balancer-policies-for-backend-server \
      --load-balancer-name rancher-elb \
      --instance-port 8080 \
      --policy-names rancher-elb-policy \
      --region eu-west-1

```

* *Create a MySql RDS instance*

![Mysql RDS instance](/images/rancher-mysql.png)

* *Once the Ranger Server is UP, Login to those servers and run the below command:*

```

docker run -d --restart=unless-stopped -p 8080:8080 -p 9345:9345 \
       rancher/server:latest \
       --db-host <your rds endpoint> \
       --db-port 3306 --db-user <your db user> \
       --db-pass <your db password> --db-name <your db name> \
       --advertise-address <put your rancher private server ip>

```

* *Once the cluster is UP, you can authorize it with Github*

![Rancher access](/images/rancher-access.png)

![Rancher github](/images/rancher-oauth-github.png)


* *Once you authorized, you can add host like below:*

![Rancher custom host](/images/add-custom-host.png)

* *You can also add host by running the command on east host nodes like below:*

```
sudo docker run -e CATTLE_AGENT_IP="172.31.42.207"  \
    -d --privileged \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /var/lib/rancher:/var/lib/rancher \
    rancher/agent:v1.2.1 http://rancher-elb-1978581874.eu-west-1.elb.amazonaws.com/v1/scripts/79081F08916A15D4F9F8:1483142400000:k2tRsgImqrdonHsdFYEtpI2ss

```

* *You can also bootup AWS instance if you want*

![Rancher aws host](/images/add-aws-host.png)

* *You can see once the host is UP*

![Rancher active host](/images/active-host.png)

* *You can choose different type of applications to install*

![Rancher catalog1](/images/catalog1.png)

![Rancher catalog2](/images/catalog2.png)

* *You can easily install Kubernetes from the catalog*

![Rancher Kubernetes](/images/k8s-template.png)

* *You can verify the installation by selecting the stack*

![Rancher Kubernetes stack](/images/starting-k8s.png)

* *You can also list the containers to get more info*

![Rancher Kubernetes containers](/images/container-info.png)
