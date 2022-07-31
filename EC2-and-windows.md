---
author: "Marcin M"
title: "EC2 and Windows"
date: 2022-07-30T20:53:43+01:00
description: "How do I configure an EC2 Windows instance to allow file downloads using Internet Explorer?"
aliases: ['EC2','Windows Server']
draft: false
categories: ['EC2','Windows Server']
tags: ['EC2','Windows Server']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
    URL: "https://github.com/Marcin13/posts/blob/master/EC2-and-Windows.md"
---
### How do I configure an EC2 Windows instance to allow file downloads using Internet Explorer?
I need to download third-party software from the internet to my Amazon Elastic Compute Cloud (Amazon EC2) Windows instance.
The Internet Explorer security configuration is blocking my attempts. How can I enable downloads?

> #### Connect to your EC2 Windows instance.
Open the Windows Start menu, and then open Server Manager.
Follow the instructions for the Windows Server version that your EC2 Windows instance is running:
Windows Server 2012 R2, Windows Server 2016, or Windows Server 2019: Choose Local Server from the left navigation pane. For IE Enhanced Security Configuration, choose On.
Windows Server 2008 R2: Choose Server Manager from the navigation pane. In the Server Summary - Security Information section, choose Configure IE ESC.

For **Administrators**, select **Off**.

For **Users**, select **Off**.

Choose OK.

Close **Server Manager**.

![Screenshot.png](http://marcinmitruk.link/img/EC2-and-windows/Screenshot_1.png)
***


### Useful links
https://aws.amazon.com/premiumsupport/knowledge-center/ec2-windows-file-download-ie/
