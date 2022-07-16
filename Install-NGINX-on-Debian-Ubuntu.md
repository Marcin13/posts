---
author: "Marcin M"
title: "Install NGINX on Debian Ubuntu"
date: 2022-06-25T11:13:13+01:00
description: "You need to install NGINX Open Source on a Debian or Ubuntu machine.."
aliases: []
draft: false
categories: ["NGINX","server"]
tags: ["nginx", "server"]
ShowToc: true
TocOpen: true
searchHidden: false
---
To get started with NGINX Open Source or NGINX Plus, you first need to install it
on a system and learn some basics.

# Installing on Debian/Ubuntu

Create a file named /etc/apt/sources.list.d/nginx.list that contains the following
contents:
```shell
sudo nano /etc/apt/sources.list.d/nginx.list
```
content of _nginx.list_
```text
deb http://nginx.org/packages/mainline/OS/ CODENAME nginx
deb-src http://nginx.org/packages/mainline/OS/ CODENAME nginx
```
check you system OS and CODENAME 
```shell
lsb_release -a
```
the file _nginx.list_ should be look like below
```shell
# comment
deb http://nginx.org/packages/mainline/ubuntu/ focal nginx
deb-src http://nginx.org/packages/mainline/ubuntu/ focal nginx
```

Alter the file, replacing OS at the end of the URL with ubuntu or debian, depending
on your distribution. Replace CODENAME with the code name for your distribu‐
tion; jessie or stretch for Debian, or trusty, xenial, artful, or bionic for
Ubuntu. Then, run the following commands:

Get key
```shell
sudo wget http://nginx.org/keys/nginx_signing.key
```
add key
```shell
sudo apt-key add nginx_signing.key
```
full update
```shell
sudo bash -c "apt update & apt upgrade -y && apt autoremove -y"
```
and now install NGINX
```shell
sudo apt-get install -y nginx
```
start NGINX
```shell
sudo /etc/init.d/nginx start
```

## Verifying Your Installation

```shell
nginx -v
```
You can confirm that NGINX is running by using the following command:
```shell
ps -ef | grep nginx
```
To verify that NGINX is returning requests correctly, use your browser to make a
request to your machine or use curl.
```shell
curl localhost
```
## NGINX commands

**nginx -h** -- Shows the NGINX help menu.

**nginx -v** --  Shows the NGINX version.

**nginx -V** --  Shows the NGINX version, build information, and configuration arguments,
which shows the modules built into the NGINX binary.

**nginx -t** --  Tests the NGINX configuration.

**nginx -T** --  Tests the NGINX configuration and prints the validated configuration to the
screen. This command is useful when seeking support.

**nginx -s signal** --  The -s flag sends a signal to the NGINX master process. You can send signals
such as **stop**, **quit**, **reload**, and **reopen**. The stop signal discontinues the
NGINX process immediately. The quit signal stops the NGINX process after it
finishes processing inflight requests. The reload signal reloads the configuration.
The reopen signal instructs NGINX to reopen logfiles.

## Discussion
The file you just created instructs the advanced package tool (APT) package manage‐
ment system to utilize the Official NGINX package repository. Modifying the file to
provide the correct endpoint and code name for your distribution ensures that the
APT utility receives the correct .deb packages for your system. The following com‐
mands download the NGINX GPG package signing key and import it into APT. Pro‐
viding APT the signing key enables the APT system to validate packages from the
repository. The apt-get update command instructs the APT system to refresh its
package listings from its known repositories. After the package list is refreshed, you
can install NGINX Open Source from the Official NGINX repository. After you
install it, the final command starts NGINX.