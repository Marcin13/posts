---
author: "Marcin M"
title: "Can't Access Oracle Cloud Always Free Compute Http Port 80"
date: 2022-06-25T17:26:41+01:00
description: "You need to allow Firewall for the port you want."
aliases: ["oracle"]
draft: false
categories: ["oracle", "port"]
tags: ["oracle","port"]
ShowToc: true
TocOpen: true
searchHidden: false
---
Can't access Oracle Cloud Always Free Compute http port

- configure ingress route for port 80
- ``curl localhost`` should bring back webpage in text format, however fails over internet.

```shell
$ sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
$ sudo netfilter-persistent save
```
 - test your web page over internet