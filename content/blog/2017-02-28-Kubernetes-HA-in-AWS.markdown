---
author: Vishnu Nair
comments: true
date: 2017-02-28 21:23:25+00:00
layout: post
slug: Kubernetes-HA-in-AWS
title: Kubernetes with HA in AWS
categories:
- Amazon AWS
- Amazon EC2
- Kubernetes
- docker
tags:
- amazon
- amazon s3
- AWS
- aws ec2
- container
- docker
- kubernetes
- ec2
- linux
---

*In this blog, we are setting up a highly available Kubernetes cluster in AWS using Kops*

*I am creating our Kubernetes cluster in a private VPC.*

*I hope everyone knows how to create a VPC architecture in AWS, so I will be mainly focusing on K8s.*

![K8s setup in AWS](/images/aws-k8s.png)

### Prerequisites:

* *AWS:*
    * *Cli*
    * *VPC*
    * *Public Subnets*
    * *Private Subnets*
    * *Route Tables*
    * *Internet Gateway*
    * *NAT Gateway*
    * *Route53 domain*

* *Kops:*

     *You can easily download from [here](https://github.com/kubernetes/kops/releases)*

* *Kubectl:*

    *You can easily install the package from [here](https://kubernetes.io/docs/user-guide/prereqs/)*

* *AWS S3 bucket:*

    * *You need to create a unique AWS S3 bucket to manage your clusters even after installation.*
    * *Kops keep track of the clusters that you have created, along with their configuration, the keys they are using etc. This information is stored in an S3 bucket.*

* *K8s HA setup:*    

```

kops create cluster \
    --node-count 3 \
    --zones us-east-1a,us-east-1b,us-east-1c \
    --master-zones us-east-1a,us-east-1b,us-east-1c \
    --dns-zone example.com \
    --node-size m4.large \
    --master-size m4.xlarge \
    --topology private \
    --networking weave \
    --vpc vpc-idxxxxx \
    --name k8s.example.com \
    --state s3://kops-state-k8s \
    --bastion

```

* ```--node-count:-```  *Specify the number of nodes*
* ```--zones:-``` *Run nodes in multiple zones*
* ```--master-zones:-``` *Run with a HA master*
* ```--dns-zone:-``` *Specify your AWS DNS zone*
* ```--node-size:-``` *Specify our K8s nodes to a defined instance type*
* ```--master-size:-``` *Specify our K8s masters to a defined instance type*
* ```--topology:-``` *Specify our nodes to launch in private/public subnets in VPC*
* ```--networking:-``` *Specify which overlay network to use*
* ```--vpc:-``` *Specify your VPC*
* ```--name:-```  *Specify name of your cluster*
* ```--state:-``` *Specify where to store the cluster state information*
* ```--bastion:-``` *Jump host*

#### You can edit the cluster by running the below command:

```

root# kops edit cluster <put your cluster name>

```

#### Change the VPC, Subnet configuration and you can update the cluster by running the below command

```

root# kops update cluster <put your cluster name>

```

#### Once you are finalized the AWS setup, you can execute it by running the below command

```

root# kops update cluster <put your cluster name> --yes

```

* *Once your cluster is up, you can play around with [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-cheatsheet/)*
