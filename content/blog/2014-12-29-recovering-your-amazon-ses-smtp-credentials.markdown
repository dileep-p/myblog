---
author: vishnur66
comments: true
date: 2014-12-29 07:55:50+00:00
layout: post
link: http://www.vishnu-tech.com/2014/12/recovering-your-amazon-ses-smtp-credentials/
slug: recovering-your-amazon-ses-smtp-credentials
title: Recovering your AMAZON SES SMTP credentials!!
wordpress_id: 327
categories:
- Amazon AWS
tags:
- amazon
- amazon ses
- AWS
- email
- ses
- smtp
---

**How to recover your Amazon SES smtp credentials:-**


**Important Note:**


**Your SMTP password is not the same as your AWS secret access key. Do not attempt to use your AWS credentials to authenticate yourself against the SMTP endpoint. **

There are two ways to generate your SMTP credentials. You can either use the Amazon SES console or you can generate your SMTP credentials from your AWS credentials.




Use the Amazon SES console to generate your SMTP credentials if:






	
  * You want to get your SMTP credentials using the simplest method.

	
  * You do not need to automate SMTP credential generation using code or a script.





Generate your SMTP credentials from your AWS credentials if:






	
  * You have an existing  IAM  user and you want that user to be able to send emails using the Amazon SES SMTP interface.

	
  * You want to automate SMTP credential generation using code or a script.





_** A user's SMTP username is the same as their AWS Access Key ID, so you just need to generate the SMTP password. **_

Here I am doing a Java implementation  that converts an AWS SECRET ACCESS KEY to an Amazon SES SMTP password.

**For example:  I am creating a file called "smtp.java"  and in this file I create a class named "smtp" and inside you need to give your AWS SECRET ACCESS KEY **

[![smtp](https://www.vishnu-tech.com/wp-content/uploads/2014/12/smtp.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/12/smtp.png)

You can download this file from [here](https://gist.github.com/vishnunamshi/c650aad1510c0123f8be).

You  Need to execute this file as:

[![ex](https://www.vishnu-tech.com/wp-content/uploads/2014/12/ex.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/12/ex.png)

**_This will create a file called "smtp" and you need to execute again as :_**

[![sm](https://www.vishnu-tech.com/wp-content/uploads/2014/12/sm.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/12/sm.png)

_**And this will prompt your AMAZON SES SMTP PASSWORD :)**_





