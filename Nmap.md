---
author: "Marcin M"
title: "Nmap"
date: 2022-12-01T18:03:31Z
description: "Sample nmap usage cases"
aliases: []
draft: true
categories: ["scan"]
tags: ["nmap"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Nmap.md"
---

```
nmap -Pn -p80 -oX logs/pb-port80scan.xml -oG logs/pb-port80scan.gnmap 216.163.128.20/20
```
This scans 4096 IPs for any web servers (without pinging them) and saves the output in grepable and XML formats.

```
nmap -p 80,443 192.168.1.1
```
Scan two ports

```
nmap -p "*" 192.168.1.1
```
Scan all ports with * wildcard

More will come soon...

![Screenshot.png](http://marcinmitruk.link/img/Nmap/Screenshot1.png)






### Useful links:

