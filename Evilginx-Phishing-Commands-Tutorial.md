---
author: "Marcin M"
title: "Evilginx Phishing Commands Tutorial"
date: 2024-05-31T16:01:59+01:00
description: "Evilginx Phishing Commands Tutorial."
aliases: ['Evilginx', 'Phishing']
draft: true
categories: ['Hacking']
tags: ['Evilginx']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Evilginx-Phishing-Commands-Tutorial.md"
---

![Screenshot.png](http://marcinmitruk.link/img/Evilginx-Phishing-Commands-Tutorial/Screenshot_1.png)
***

## Install Dependencies
### Update and upgrade repo and install make
```shell
sudo apt update
sudo apt install git make -y
sudo apt install gcc libpcap-dev libnetfilter-queue-dev -y
```

## Steps to Install Go and Build Evilginx

### Install Go

First, you need to install the Go programming language. You can do this by downloading and installing the latest version of Go.

```bash
sudo apt install -y golang-go -y
```
### Ensure that Go is installed correctly by checking its version.

```shell
go version
```

## Set Up Go Environment

Set the GOPATH environment variable to point to your Go workspace. Add the following lines to your .bashrc or .profile file:

```shell
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc
```
### Clone Evilginx Repository, build Evilginx
```shell
git clone https://github.com/BakkerJan/evilginx2.git
cd evilginx2
make
sudo make install
```

### Start Evilginx
```shell
sudo evilginx
```
## Check for DNS Service on Port 53

Ensure that no other service is using port 53. You can check this with:

```bash
sudo lsof -i :53
```
### To stop systemd-resolved temporarily:
```shell
sudo systemctl stop systemd-resolved
```
### To disable it permanently (use with caution):
```shell
sudo systemctl disable systemd-resolved
```
### Start again Evilginx
```shell
sudo evilginx
```

## Basic Commands:
### Show Help
```shell
help
```

### Set Up Phishing Site
```shell
config domain yourphishingdomain.com
```
```shell
 config ip your.server.ip
```

### Check Configuration
```shell
 config
```

### List Available Phishlets
```shell
 phishlets
```

### Enable a Phishlet
```shell
 phishlets enable <phishlet_name>
```

### Disable a Phishlet
```shell
 phishlets disable <phishlet_name>
```

### List Active Phishlets
``` shell
 phishlets list
```

### Create a Lure
```shell
 lures create <phishlet_name>
```

### Get Lure URL
```shell
 lures get-url <lure_id>
```

### List Lures
```shell
 lures
```

### Delete a Lure
```shell
 lures delete <lure_id>
```

### List Sessions
```shell
 sessions
```

### Show Session Details
```shell 
sessions <session_id>
```

### Configure SSL/TLS Certificates
```shell 
config certs yourphishingdomain.com /path/to/ssl/certificate /path/to/private/key
```

### Reload Phishlets
```shell
 phishlets reload
```


> #### Let's create S3 bucket

Click create bucket to create one
![Screenshot.png](http://marcinmitruk.link/img/Evilginx-Phishing-Commands-Tutorial/Screenshot_1.png)
***






### Links:
[janbakker.tech](https://janbakker.tech/how-to-set-up-evilginx-to-phish-office-365-credentials/)