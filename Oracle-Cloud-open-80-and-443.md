---
author: "Marcin M"
title: "Oracle Open Port 80 and 443"
date: 2025-07-07T18:32:02+01:00
description: "How to open ports 80 and 443 on an Oracle Cloud instance"
aliases: []
categories: ['Networking', 'Linux', 'Oracle Cloud']
tags: ['Oracle', 'Cloud', 'Networking', 'Linux', 'Security']
draft: false
ShowToc: true
TocOpen: true
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Oracle-Open-Port-80-and-443.md"
---

## Oracle Open Port 80 and 443

To open ports 80 and 443 on an Oracle Cloud instance, you need to modify the security list associated with your instance's virtual cloud network (VCN). Here’s how to do it:

## Step 1: Log in to the Oracle Cloud Console

1. Go to the [Oracle Cloud Console](https://cloud.oracle.com/).
2. Log in with your Oracle Cloud account credentials.
3. Select the appropriate compartment where your instance is located.
4. Navigate to the **Networking** section in the left sidebar.
5. Click on **Virtual Cloud Networks**.
6. Select the VCN associated with your instance.
7. Click on **Security Lists** in the left sidebar.
8. Select the security list that is associated with the subnet where your instance resides. If you are unsure, you can check the subnet details in the **Subnets** section.
9. Click on the **Add Ingress Rules** button.
10. In the **Add Ingress Rules** dialog, fill in the following details:
    - **Source Type**: CIDR
    - **Source CIDR**: `
    - **IP Protocol**: TCP
    - **Destination Port Range**: 80, 443
    - **Description**: Open ports 80 and 443 for HTTP and HTTPS traffic
    - **State**: Enabled
    - Click on the **Add Ingress Rules** button to save the changes.

## Step 2: Verify the Changes

- After adding the ingress rules, you can verify that ports 80 and 443 are open by using a tool like `telnet` or `curl` from another machine:

## Check if ports 80 and 443 are open

```sh
# Replace <your-instance-public-ip> with the public IP address of your Oracle Cloud instance
  telnet <your-instance-public-ip> 80
  ## and 
  telnet <your-instance-public-ip> 443
```

The Oracle instance after the editing a VCN security list should be able to respond to requests on ports 80 and 443. If you see a message like "Connected to <your-instance-public-ip>", it means the ports are open and accessible, `but more then sure you need edit iptables rules to allow traffic on ports 80 and 443.`

## Step 3: Login to the Oracle Cloud instance

You can log in to your Oracle Cloud instance using SSH. Open a terminal and run the following command:

`Switch to root user`

```sh
sudo su -
```

## Step 4: Check if server is running on ports 80 and 443

```bash
sudo netstat -tuln | grep -E '(:80|:443)'
```

or

```sh
ss -nltp
```

example output:

``` sh
LISTEN    0         4096                     *:22                      *:*        users:(("sshd",pid=1011,fd=3),("systemd",pid=1,fd=99))                                      
LISTEN    0         511                      *:80                      *:*        users:(("apache2",pid=15131,fd=4),("apache2",pid=15130,fd=4),("apache2",pid=12702,fd=4))  
```

## Step 5: Modify the iptables rules file

```sh
nano /etc/iptables/rules.v4
```

This file contains the iptables rules that are applied at boot time. You can add rules to allow traffic on ports 80 and 443.
Add the following lines to allow traffic on ports 80 and 443:
Add the following lines just after below  routes for ssh 22 port opening:

```bash
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
```

Make sure to save the file after editing.

## Step 6: Restore the iptables rules

After modifying the rules file, you need to restore the iptables rules to apply the changes. You can do this by running:

```bash
iptables-restore < /etc/iptables/rules.v4
```

## Step 7: Check if the ports are open and the server is running and accessible from the outside
