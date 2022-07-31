---
author: "Marcin M"
title: "Using Route 53 Routing Policies"
date: 2022-07-30T17:55:02+01:00
description: "Sample article showcasing Using Route 53 Routing Policies"
aliases: ["AWS","DNS"]
draft: true
categories: ['AWS','DNS Caching and Performance Optimization']
tags: ['AWS']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
    URL: "https://github.com/Marcin13/posts/blob/master/Using-Route-53-Routing-Policies.md"
---
## We will create Route 53 Routing policies base on latency.
### Where there is no red arrow or description, leave the default value.

> #### First we need to create two web servers with sample web code in to different region.
To do this exercise, we need running to servers
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_1.png)
***

> #### Use sample user data code to create it.
Copy code and run as user data when launch servers
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_2.png)
***

> #### Copy and past code 
To use this code you have to use AWS Amazon Linux ISO!
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



> #### And which one will show the instance location.

![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_3.png)
***

> #### Go to Route 53, hosted zone.

![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_4.png)
***

> #### At this point you have to have domain
We need a registered domain to use
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_5.png)
***

> #### Create record
the record must be A record
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_6.png)
***

> #### it hast to be A record

![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_7.png)
***

> #### Routing policy overview, and create record

![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_8.png)
***

> #### Create second record for another web
We do exactly the same steps as with first record
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_9.png)
***

> #### Check if it is working
Check if it is working use web browser and proxy servers
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_10.png)
***

> #### you can enable health check
optional you can enable health check
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_11.png)
***

> #### Clean up your A records
Clean up yours records
![Screenshot.png](http://marcinmitruk.link/img/Using-Route-53-Routing-Policies/Screenshot_12.png)
***


### Useful links
