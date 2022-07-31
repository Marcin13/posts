---
author: "Marcin M"
title: "Create an Auto Scaling Group"
date: 2022-07-16T18:26:57+01:00
description: "Create lunche template and autoscaling group with AWS console."
aliases: ['AWS','Autoscaling group']
draft: true
categories: ['AWS','Autoscaling group']
tags: ['AWS']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
    URL: "https://github.com/Marcin13/posts/blob/master/Create-an-Auto-Scaling-Group.md"
---
## We will create lunche template and auto-scaling group 
### Where there is no red arrow or description, leave the default values.

> #### Under the instances select Lunch Template
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_1.png)
***

> #### We do not have any templates so create one
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_2.png)
***
> #### Give it a name and scroll down
We put LT1 witch stands for **L**unch **T**emplate **1**
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_3.png)
***

> #### Chose AMI and instance type
In our case is Amazon Linux 2 & t2.micro
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_4.png)
***

> #### Select your Key pair your VPC and security group
You have to have you key pair created or click _create new key pair_.
Select VPC and Security group
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_5.png)
***

> #### Select your Security Group and live default Volume settings
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_6.png)
***

> #### N0 add tags or network interface at this stage, go to advance details
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_7.png)
***

> #### Past User data and CREATE LUNCH TEMPLATE.
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_8.png)
***

> #### This code run when Instance launch
This code will create an Apache web server and index.html with content.
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_9.png)
***

> #### Copy User data
This code will work with Amazon linux

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
INTERFACE=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNETID=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/${INTERFACE}/subnet-id)
echo '<center><h1>This instance is in the subnet wih ID: SUBNETID </h1></center>' > /var/www/html/index.txt
sed "s/SUBNETID/$SUBNETID/" /var/www/html/index.txt > /var/www/html/index.html
```

> #### Past code and Create Lunch Template
and all done you create very own launch template
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_10.png)
***

### Crete Auto Scaling Group

> #### On the bottom menu find Auto Scaling Group
We do not have any so create one
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_11.png)
***

> #### Give it the name
In our scenario ASG1 and from the drop menu select our Launch template, click next
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_12.png)
***

> #### Select VPC
and public subnets
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_13.png)
***

> #### Select desired capacity
we chose minimum and maximum running instances, and we do not at this stage chose scaling polices
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_14.png)
***

> #### Click next
no notification
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_15.png)
***

> #### and next
no tags and create
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_16.png)
***

> #### We create Auto scaling group
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_17.png)
***

> #### Check EC2 instance
and notices we got two running instances
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_18.png)
***

> #### Autoscaling group triger two instance
we got to fresh instance running
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_19.png)
***

> #### Autoscaling group activity
Under the activity we see we lunch two instances success
![Screenshot.png](http://marcinmitruk.link/img/Create-an-Auto-Scaling-Group/Screenshot_20.png)
***



