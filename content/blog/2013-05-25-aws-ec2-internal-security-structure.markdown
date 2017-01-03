---
author: vishnur66
comments: false
date: 2013-05-25 04:04:25+00:00
layout: post
link: http://www.vishnu-tech.com/2013/05/aws-ec2-internal-security-structure/
slug: aws-ec2-internal-security-structure
title: AWS EC2 Internal Security Structure
wordpress_id: 230
categories:
- Amazon EC2
---

[Reference](http://media.amazonwebservices.com/pdf/AWS_Security_Whitepaper.pdf)

An insight into internal structure of EC2.

**The Hypervisor**
Amazon EC2 currently utilizes a highly customized version of the Xen hypervisor, taking advantage of paravirtualization
(in the case of Linux guests). Because paravirtualized guests rely on the hypervisor to provide support for operations that
normally require privileged access, the guest OS has no elevated access to the CPU. The CPU provides four separate
privilege modes: 0-3, called rings. Ring 0 is the most privileged and 3 the least. The host OS executes in Ring 0. However,
rather than executing in Ring 0 as most operating systems do, the guest OS runs in a lesser-privileged Ring 1 and
applications in the least privileged Ring 3. This explicit virtualization of the physical resources leads to a clear separation
between guest and hypervisor, resulting in additional security separation between the two.

**Paravirtualization**: In computing, paravirtualization is a virtualization technique that presents a software interface to
virtual machines that is similar but not identical to that of the underlying hardware.

**Instance Isolation**
Different instances running on the same physical machine are isolated from each other via the Xen hypervisor. Amazon
is active in the Xen community, which provides awareness of the latest developments. In addition, the AWS firewall
resides within the hypervisor layer, between the physical network interface and the instance’s virtual interface. All
packets must pass through this layer, thus an instance’s neighbors have no more access to that instance than any other
host on the Internet and can be treated as if they are on separate physical hosts. The physical RAM is separated using
similar mechanisms.

[![awssecurity](https://www.vishnu-tech.com/wp-content/uploads/2013/05/awssecurity.png)](https://www.vishnu-tech.com/wp-content/uploads/2013/05/awssecurity.png)

Customer instances have no access to raw disk devices, but instead are presented with virtualized disks. The AWS proprietary disk virtualization layer automatically resets every block of storage used by the customer, so that one customer’s data are never unintentionally exposed to another. AWS recommends customers further protect their data using appropriate means. One common solution is to run an encrypted file system on top of the virtualized disk device.

**Guest Operating System:** Virtual instances are completely controlled by you, the customer. You have full root access or administrative control over accounts, services, and applications. AWS does not have any access rights to your instances or the guest OS. AWS recommends a base set of security best practices to include disabling password-only access to your guests, and utilizing some form of multi-factor authentication to gain access to your instances (or at a minimum certificate-based SSH Version 2 access). Additionally, you should employ a privilege escalation mechanism with logging on a per-user basis. For example, if the guest OS is Linux, after hardening your instance you should utilize certificatebased SSHv2 to access the virtual instance, disable remote root login, use command-line logging, and use ‘sudo’ for privilege escalation. You should generate your own key pairs in order to guarantee that they are unique, and not shared with other customers or with AWS.

You also control the updating and patching of your guest OS, including security updates. Amazon-provided Windows and Linux-based AMIs are updated regularly with the latest patches, so if you do not need to preserve data or customizations on your running Amazon AMI instances, you can simply relaunch new instances with the latest updated AMI. In addition, updates are provided for the Amazon Linux AMI via the Amazon Linux yum repositories.

Well-informed traffic management and security design are still required on a perinstance basis. AWS further encourages you to apply additional per-instance filters with host-based firewalls such as  IPtables or the Windows Firewall and VPNs. This can restrict both inbound and outbound traffic.

**Why take snapshots if EBS is storing data redundantly?**
Well-informed traffic management and security design are still required on a perinstance basis. AWS further encourages you to apply additional per-instance filters with host-based firewalls such as IPtables or the Windows Firewall and VPNs. This can restrict both inbound and outbound traffic.

**Security Features on S3**
1.Identity and Access Management (IAM) Policies.
2.Access Control Lists (ACLs).
3.Bucket Policies.
4.Encryption/Decryption
Server-side encryption (SSE): An option for S3 storage for automatically encrypting data at rest. With Amazon S3 SSE,
customers can encrypt data on upload simply by adding an additional request header when writing the object.
Decryption happens automatically when data is retrieved.

**Security Features on RDS**
Amazon RDS has multiple features that enhance reliability for critical production databases, including DB security
groups, permissions, SSL connections, automated backups, DB snapshots, and multi-AZ deployments. DB instances can
also be deployed in an Amazon VPC for additional network isolation.
