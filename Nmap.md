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

We perform a quick port scan using the **nmap** command as follows:
```shell
nmap 64.226.97.50 
```
The result of the scan shows that **port **22**** is open:
```shell
PORT   STATE SERVICE
22/tcp open  ssh
```
To determine what is running on port 22, we use the **-sV** option to enable version detection:
```shell
nmap -p 22 -sV  64.226.97.50

 ```
The output of this command shows that the service running on **port 22** is OpenSSH version 8.9p1 on an Ubuntu Linux system:
```shell
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


```
Next, we check if we can log in using a _username_ and _password_ or an SSH key:

```shell
nmap -p 22 -d --script ssh-auth-methods 64.226.97.50

```
The output shows that the supported authentication methods are publickey and password:
```shell
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 64
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
Final times for host: srtt: 3164 rttvar: 8926  to: 100000
```

Finally, we can try to brute-force the login by running the following command:
```shell
sudo nmap -p 22 --script ssh-brute --script-args userdb=usernames.txt,passdb=passwords.txt 64.226.97.50
```
If the username and password combination exists in the provided lists, we can successfully log in to the system.

Good luck!! with hunting.
![Screenshot.png](http://marcinmitruk.link/img/Nmap/Screenshot1.png)






### Useful links:

