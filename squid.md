---
author: "Marcin M"
title: "Squid"
date: 2022-06-25T22:16:46+01:00
description: "Useful Squid Proxy Command Reference."
aliases: []
draft: false
categories: ["SQUID"]
tags: ["squid"]
ShowToc: true
TocOpen: true
searchHidden: false
---
get the basic status of Squid
```shell
sudo squid -k check | echo $?
```
check the squid configuration for errors
```shell
squid -k parse
```
test connectivity to the chosen site
```shell
squidclient -h <squidendpoint> -p3128 http://<testurl>
```
get list of recent attempts to reach forbidden sites
```shell
sudo grep -nR '/403' /var/log/squid/access.log
```
get real-time list of sites forbidden by Squid
```shell
sudo tail -f /var/log/squid/access.log | grep '/403'
```
get useful information about the proxy process and tail logs
```shell
systemctl status squid
```
if you change the squid config and want to run it on the existing server
```shell
sudo systemctl reload squid
```
restart the proxy
```shell
sudo systemctl restart squid
```
get the feed of Squid logs
```shell
sudo journalctl -u squid
```
## Proxy configuration example
### _/etc/squid/squid.conf_

```text
# A simplified quickstart Squid config
# This is what you get when you first install Squid
# Also included is the spec for an outbound traffic whitelist

# ...
acl allowed_ips src "/etc/squid/allowed_ips.txt"
# ...
#http_access allow localnet
http_access allow localhost
http_access allow allowed_ips

# acl localnet src your.ip.add.es

# Example rule allowing access from your local networks.
# Adapt to list your (internal) IP networks from where browsing should be allowed
#acl localnet src 10.0.0.0/8	# RFC1918 possible internal network
#acl localnet src 172.16.0.0/12	# RFC1918 possible internal network
#acl localnet src 192.168.0.0/16	# RFC1918 possible internal network
#acl localnet src fc00::/7       # RFC 4193 local private network range
#acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged) machines

# standard allowed outbound ports
acl SSL_ports port 443
acl Safe_ports port 80		# http
acl Safe_ports port 21		# ftp
acl Safe_ports port 443		# https
acl Safe_ports port 70		# gopher
acl Safe_ports port 210		# wais
acl Safe_ports port 1025-65535	# unregistered ports
acl Safe_ports port 280		# http-mgmt
acl Safe_ports port 488		# gss-http
acl Safe_ports port 591		# filemaker
acl Safe_ports port 777		# multiling http
acl CONNECT method CONNECT

# Deny requests to certain unsafe ports
http_access deny !Safe_ports

# Deny CONNECT to other than secure SSL ports
http_access deny CONNECT !SSL_ports

# Only allow cachemgr access from localhost
acl manager proto cache_object
http_access allow localhost manager
http_access deny manager

# allow outbound if from on the Squid host
http_access allow localhost

# only allow outbound from the whitelist in /etc/squid/
acl egress_domains dstdomain "/etc/squid/whitelist.txt"
http_access allow localnet egress_domains

# allow egress to an IP from the internal network
acl outbound_ip dst 1.2.3.4
http_access allow localnet outbound_ip

# And finally deny all other access to this proxy
http_access deny all

# Squid normally listens to port 3128
http_port 3128

# Caching patterns for squid cache objects
refresh_pattern ^ftp:		1440	20%	10080
refresh_pattern ^gopher:	1440	0%	1440
refresh_pattern -i (/cgi-bin/|\?) 0	0%	0
refresh_pattern (Release|Packages(.gz)*)$      0       20%     2880
# example lin deb packages
#refresh_pattern (\.deb|\.udeb)$   129600 100% 129600
refresh_pattern .		0	20%	4320
```
### /etc/squid/whitelist.txt
```text
.aws.amazon.com
.amazons.com
.aws.ce.redhat.com
```
### /etc/squid/allowed_ips.txt
```text
# All other allowed IPs
86.190.253.183
```
https://www.appservgrid.com/paw92/index.php/2019/03/15/sarg-squid-analysis-report-generator-and-internet-bandwidth-monitoring-tool/