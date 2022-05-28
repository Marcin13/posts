---
author: "Marcin"
title: "How To Install Webmin on Amazon Linux/CentOS/RHEL"
date: 2022-05-29T10:34:52+01:00 
description: "Webmin is a web-based interface for system administration for Unix"
aliases: ["Webmin"]
draft: false 
categories: ["aws", "EC2", "Webmin"]
tags: ["aws", "Webmin"]
ShowToc: true 
TocOpen: false 
searchHidden: false
---
**Webmin** is a web-based management interface for system administration of *nix systems. It allows remote management of
your system without having to manually modify the configuration files. It lets you install packages like LAMP stack,
mail servers, WordPress etc. and setup & configure – firewalls, DNS, FTP servers, users, file sharing etc. all through a
remote web interface. It is a must - have tool for systems and web administrators.

## Download Webmin

Create Webmin Yum Repository Create the `/etc/yum.repos.d/webmin.repo` 
```nano
sudo nano /etc/yum.repos.d/webmin.repo
```
Add the following text into the file once repository is created
```
[Webmin]
name=Webmin Distribution Neutral
#baseurl=http://download.webmin.com/download/yum
mirrorlist=http://download.webmin.com/download/yum/mirrorlist
enabled=1
```

You should also fetch and install GPG key with which the packages are signed, with the commands:
```shell
wget http://www.webmin.com/jcameron-key.asc
```
```shell
sudo rpm -import jcameron-key.asc
```

Update instance:
```shell
sudo yum update -y & yum upgrade -y
```

Output:
![Screenshot](../../img/webmin/Screenshot2.png)

You will now be able to install **webmin** with the command:
```shell
sudo yum install webmin
```

Press `Y` on output to continue installation:
![Screenshot](../../img/webmin/Screenshot3.png)

Output of successfully installation:
![inbound rouls](../../img/webmin/Screenshot1.png)
> **All dependencies** should be resolved automatically.

## Configure Webmin
After installation of Webmin on Linux and when you try to access it over the internet
using https://www.example.com:10000/ or https://ipaddress:10000, the browser shows following error:
> **The Web Server** is running in SSL mode. Try the URL https://SERVER IP ADDRESS:10000/ instead.

To fix this problem we have to disable the SSL mode for the control panel. Following are the steps to disable SSL mode and run webmin in normal mode.
Update the `/etc/webmin/miniserv.conf` file and change `ssl=1` to `ssl=0`:
```shell
sudo nano /etc/webmin/miniserv.conf
```

change line with `#ssl=1 to ssl=0`
```shell
logtime=168
#ssl=1
ssl=0
no_ssl2=1
no_ssl3=1
no_tls1=1
no_tls1_1=1
```

Restart Webmin
```shell
sudo /etc/webmin/restart
```
Now you can access Webmin in browser using following url. Change domain name with your server IP.
https://www.example.com:10000

## Important 
>Update you security group inbound to allow `TCP` from port `10000`

![inbound rouls](../../img/webmin/Screenshot4.png)


## Wait! What is my login password?
By default, the password of Webmin root user is the same as Linux root user. 
However, you cannot log in as a root user since there is no password for Amazon Linux root user. 
But you may force update the password of Webmin root user in order to login Webmin, Run the following command:
```shell
sudo /usr/libexec/webmin/changepass.pl /etc/webmin root NEWPASSWORD
```
Now restart the webmin, go back to browser and try to log in with the new password, it should work.
```shell
sudo /etc/webmin/restart
```
## Conclusion
Now you know how to install Webmin on Amazon Linux/CentOS/RHEL. After following this tutorial, 
you should have a fully functioning copy of Webmin set up and ready to use.
