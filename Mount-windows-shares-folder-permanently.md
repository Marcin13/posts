---
author: "Marcin M"
title: "Mount Windows Shares Folder Permanently"
date: 2022-07-12T17:34:02+01:00
description: "This document describes how to mount **CIFS** shares permanently."
aliases: []
draft: true
categories: []
tags: []
ShowToc: true
TocOpen: false
searchHidden: false
---
This document describes how to mount **CIFS** shares permanently.
The shares might be hosted on a **Windows** computer/server, or on a **Linux/UNIX** server running Samba.

<!--more-->
### Prerequisites

_We're assuming that:_
+ Network connections have been configured properly.
+ Share username on Windows computer is YourWinUser.
+ Share password on Windows computer is YouUserPass.
+ The Windows computer's name is servername (this can be either an IP address or an assigned name).
+ The name of the share is sharename.
+ You want to mount the share in /media/windowsshare.

### CIFS installation
We run  update and upgrade to make sure our repo is up to date
``` cmd
sudo apt-get update && sudo apt-get upgrade
```
you’ll need the cifs-utils package in order to mount SMB shares. Just type the following command at the terminal:
``` cmd
sudo apt-get install cifs-utils
```
***
 >>### Create a share folder in Windows system
> Create in your system folder which one you want to share with Linux via CIFS
***
### Mounting unprotected (guest) network folders

First, let's create the mount directory. You will need a separate directory for each mount.
```cmd
sudo mkdir /media/windowsshare
```
Then edit your /etc/fstab file (with root privileges)

```cmd
sudo nano -l /etc/fstab
```
to add this line:

```cmd
//servername/sharename /media/windowsshare cifs username=YourWinUser,password=YouUserPass,iocharset=utf8  0  0
```
Where:
+ **iocharset=utf8** allows access to file with names in non-English languages. This doesn't work with shares of devices like the Buffalo Tera Station, or Windows machines that export their shares using ISO8895-15.
+ If there is any **space in the server path**, you need to replace it by \040, for example //servername/My\040Documents
  After you add the entry to /etc/fstab type:

### My working entry
```cmd
##my entry
//MARCIN-PC/Users/marcin/ansible /home/marcin/ansible cifs username=msusername,password=mspassword,gid=marcin,uid=1000,iocharset=utf8,  0  0
``` 
AND DO NOT FORGER TO MONT EVRY TIME YOU RESTART I THINK

```cmd
sudo mount -a
```
This will (re)mount all entries listed in /etc/fstab.


https://wiki.ubuntu.com/MountWindowsSharesPermanently

http://manpages.ubuntu.com/manpages/jammy/en/man8/mount.cifs.8.html

https://stackoverflow.com/questions/61554465/cannot-run-docker-from-network-mounted-directory
