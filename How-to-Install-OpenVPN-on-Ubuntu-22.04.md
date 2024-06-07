---
author: "Marcin M"
title: "How to Install OpenVPN on Ubuntu 22.04"
date: 2024-06-07T21:26:00+01:00
description: "Sample article showcasing basic skills. "
aliases: []
draft: true
categories: []
tags: []
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/How-to-Install-OpenVPN-on-Ubuntu-22.04.md"
---
### Prerequisites

    An Ubuntu server 22.04
    SSH root access or regular user with root privileges

#### Step 1. Log in to the Server

First, log in to your Ubuntu 22.04 VPS through SSH as the root user:

```bash 
ssh root@IP_Address -p Port_number
```

Replace root with a user that has sudo privileges, and IP_Address and Port_number with your server’s IP address and SSH port number.

Verify you are on Ubuntu 22.04:

```bash 
lsb_release -a 
```

Expected output:


    No LSB modules are available.
    Distributor ID: Ubuntu
    Description: Ubuntu 22.04.1 LTS
    Release: 22.04
    Codename: jammy

#### Step 2. Update the System

Ensure all packages are up-to-date:

```bash
sudo bash -c "apt update & apt upgrade -y && apt autoremove -y"
```
#### Step 3. Install OpenVPN Server

Download the OpenVPN installation script from GitHub:

```bash 
cd /opt
wget https://raw.githubusercontent.com/Nyr/openvpn-install/master/openvpn-install.sh -O openvpn-install.sh
```

Make the script executable and run it:

```bash 
chmod +x openvpn-install.sh
./openvpn-install.sh
```

During the installation, you will be prompted with several questions:

Choose the protocol (UDP is recommended):

     Which protocol should OpenVPN use?

     1) UDP (recommended)
     2) TCP
        Protocol [1]: 1

Select the port (default is 1194):

     What port should OpenVPN listen to?
        Port [1194]:

Choose a DNS server:

     Select a DNS server for the clients:
     1) Current system resolvers
     2) Google
     1) 1.1.1.1
     2) OpenDNS
     3) Quad9
     4) AdGuard
        DNS server [1]:

Enter a name for the first client:

     Enter a name for the first client:
        Name [client]: userone

Start the installation:

     Press any key to continue...

The script will install the OpenVPN server, generate a certificate, create the VPN user specified, and configure the firewall. The OpenVPN configuration file can be found at /etc/openvpn/server/server.conf.

You can check the OpenVPN service status with:

bash

systemctl status openvpn-server@server.service

This summarizes the process of setting up OpenVPN on an Ubuntu 22.04 server using a bash script.






![Screenshot.png](http://marcinmitruk.link/img/How-to-Install-OpenVPN-on-Ubuntu-22.04/Screenshot1.png)






### Useful links:

