---
author: "Marcin M"
title: "Nohup With Busybox httpd"
date: 2022-09-04T09:30:34+01:00
description: "Quick usage cases Nohup With Busybox httpd "
aliases: ["Busybox"]
draft: true
categories: ["Server"]
tags: ["Busybox","Server"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/nohup-with-busybox_httpd.md"
---


```shell
nohup busybox httpd -f -p 8080 &

ps -ef | grep httpd

sudo kill -9 1193

and run in difrent location

nohup busybox httpd -f -p 8080 &

```








### Useful links:

