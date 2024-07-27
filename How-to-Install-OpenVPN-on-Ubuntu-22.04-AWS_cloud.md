---
author: "Marcin M"
title: "How to Install OpenVPN on Ubuntu 22.04-AWS-EC2"
date: 2024-06-07T21:26:00+01:00
description: "A step-by-step guide to installing OpenVPN on Ubuntu 22.04 running on AWS EC2."
aliases: [ "openvpn-ubuntu-22.04", "aws-ec2-openvpn" ]
draft: true
categories: [ "Tutorials", "Networking" ]
tags: [ "OpenVPN", "Ubuntu", "AWS", "EC2", "VPN" ]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/How-to-Install-OpenVPN-on-Ubuntu-22.04-AWS_cloud.md"
---

## Introduction

This tutorial will guide you through the installation of OpenVPN on Ubuntu 22.04. OpenVPN is a popular open-source VPN
server and client software. It allows you to create a secure connection to another network over the internet. In this
tutorial, we will install OpenVPN on an Ubuntu 22.04 server running on AWS EC2.

### Prerequisites

An Ubuntu 22.04 server running on AWS EC2 in your desired region.

SSH access with root privileges or a user account with sudo capabilities.

#### Step 1. Log in to the Server

First, log in to your Ubuntu 22.04 VPS through SSH as the root user:
In the case of AWS, the user is **ubuntu**.

```bash
ssh ubuntu@[IP_Address] -p [Port_number]
```

Replace root with a user that has sudo privileges, and [IP_Address] and [Port_number] with your server’s IP address and SSH
port number.

Verify you are on Ubuntu 22.04:

```bash
lsb_release -a 
```

### Expected output:

    Distributor ID: Ubuntu
    Description:    Ubuntu 22.04 LTS
    Release:        22.04
    Codename:       Jammy

#### Step 2. Update the System

Ensure all packages are up-to-date:

```bash
sudo bash -c "apt update & apt upgrade -y && apt autoremove -y"
```

#### Step 3. Install OpenVPN Server

Download the OpenVPN installation script from GitHub:

```bash
cd /opt
```

Change to the root user because the script requires root permissions:
```shell
sudo -i
```
Download the script:
```shell
curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
```
List the files in the directory:
```shell
ls
````
Make the script executable and run it:
```bash
chmod +x openvpn-install.sh
```

#### Step 3. Run the script and Install OpenVPN Server

```shell
./openvpn-install.sh
```

### The output should be like this:

    
    Welcome to the OpenVPN installer!
    The git repository is available at: https://github.com/angristan/openvpn-install
    
    I need to ask you a few questions before starting the setup.
    You can leave the default options and just press enter if you are OK with them.
    
    I need to know the IPv4 address of the network interface you want OpenVPN listening to.
    Unless your server is behind NAT, it should be your public IPv4 address.
    IP address: 13.38.123.0
    
    Checking for IPv6 connectivity...
    
    Your host does not appear to have IPv6 connectivity.
    
    Do you want to enable IPv6 support (NAT)? [y/n]: n
    
    What port do you want OpenVPN to listen to?
       1) Default: 1194
          2) Custom
          3) Random [49152-65535]
    Port choice [1-3]: 1
    
    What protocol do you want OpenVPN to use?
    UDP is faster. Unless it is not available, you shouldn't use TCP.
       1) UDP
          2) TCP
    Protocol [1-2]: 1
    
    What DNS resolvers do you want to use with the VPN?
       1) Current system resolvers (from /etc/resolv.conf)
          2) Self-hosted DNS Resolver (Unbound)
          3) Cloudflare (Anycast: worldwide)
          4) Quad9 (Anycast: worldwide)
          5) Quad9 uncensored (Anycast: worldwide)
          6) FDN (France)
          7) DNS.WATCH (Germany)
          8) OpenDNS (Anycast: worldwide)
          9) Google (Anycast: worldwide)
          10) Yandex Basic (Russia)
          11) AdGuard DNS (Anycast: worldwide)
          12) NextDNS (Anycast: worldwide)
          13) Custom
    DNS [1-12]: 10
    
    Do you want to use compression? It is not recommended since the VORACLE attack makes use of it.
    Enable compression? [y/n]: n
    
    Do you want to customize encryption settings?
    Unless you know what you're doing, you should stick with the default parameters provided by the script.
    Note that whatever you choose, all the choices presented in the script are safe. (Unlike OpenVPN's defaults)
    See https://github.com/angristan/openvpn-install#security-and-encryption to learn more.
    
    Customize encryption settings? [y/n]: n
    
    Okay, that was all I needed. We are ready to set up your OpenVPN server now.
    You will be able to generate a client at the end of the installation.
    Press any key to continue...
    Hit:1 http://eu-west-3.ec2.archive.ubuntu.com/ubuntu noble InRelease
    Hit:2 http://eu-west-3.ec2.archive.ubuntu.com/ubuntu noble-updates InRelease 
    Hit:3 http://eu-west-3.ec2.archive.ubuntu.com/ubuntu noble-backports InRelease
    Hit:4 http://security.ubuntu.com/ubuntu noble-security InRelease           
    Reading package lists... Done                                              
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    ca-certificates is already the newest version (20240203).
    gnupg is already the newest version (2.4.4-2ubuntu17).
    The following package was automatically installed and is no longer required:
      libpkcs11-helper1t64
    Use 'sudo apt autoremove' to remove it.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    iptables is already the newest version (1.8.10-3ubuntu2).
    openssl is already the newest version (3.0.13-0ubuntu3.1).
    wget is already the newest version (1.21.4-1ubuntu4).
    ca-certificates is already the newest version (20240203).
    curl is already the newest version (8.5.0-2ubuntu10.1).
    Suggested packages:
      openvpn-dco-dkms openvpn-systemd-resolved easy-rsa
    The following NEW packages will be installed:
      openvpn
    0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
    Need to get 679 kB of archives.
    After this operation, 1797 kB of additional disk space will be used.
    Get:1 http://eu-west-3.ec2.archive.ubuntu.com/ubuntu noble/main amd64 openvpn amd64 2.6.9-1ubuntu4 [679 kB]
    Fetched 679 kB in 0s (20.8 MB/s)
    Preconfiguring packages ...
    Selecting previously unselected package openvpn.
    (Reading database ... 102471 files and directories currently installed.)
    Preparing to unpack .../openvpn_2.6.9-1ubuntu4_amd64.deb ...
    Unpacking openvpn (2.6.9-1ubuntu4) ...
    Setting up openvpn (2.6.9-1ubuntu4) ...
    Created symlink /etc/systemd/system/multi-user.target.wants/openvpn.service → /usr/lib/systemd/system/openvpn.service.
    Processing triggers for man-db (2.12.0-4build2) ...
    Scanning processes...                                                                                                                          
    Scanning linux images...                                                                                                                       
    
    Running kernel seems to be up-to-date.
    
    No services need to be restarted.
    
    No containers need to be restarted.
    
    No user sessions are running outdated binaries.
    
    No VM guests are running outdated hypervisor (qemu) binaries on this host.
    --2024-06-21 12:29:20--  https://github.com/OpenVPN/easy-rsa/releases/download/v3.1.2/EasyRSA-3.1.2.tgz
    Resolving github.com (github.com)... 140.82.121.4
    Connecting to github.com (github.com)|140.82.121.4|:443... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/4519663/c2688102-7cd5-4fcc-b272-083d48dc4b4d?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20240621%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240621T122920Z&X-Amz-Expires=300&X-Amz-Signature=bcfe2f643edef15f2951460bd65bf2203d0a300ee0be870dfde42ea55d29d829&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=4519663&response-content-disposition=attachment%3B%20filename%3DEasyRSA-3.1.2.tgz&response-content-type=application%2Foctet-stream [following]
    --2024-06-21 12:29:20--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/4519663/c2688102-7cd5-4fcc-b272-083d48dc4b4d?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20240621%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240621T122920Z&X-Amz-Expires=300&X-Amz-Signature=bcfe2f643edef15f2951460bd65bf2203d0a300ee0be870dfde42ea55d29d829&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=4519663&response-content-disposition=attachment%3B%20filename%3DEasyRSA-3.1.2.tgz&response-content-type=application%2Foctet-stream
    Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.108.133, 185.199.109.133, 185.199.110.133, ...
    Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.108.133|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 68984 (67K) [application/octet-stream]
    Saving to: ‘/root/easy-rsa.tgz’
    
    /root/easy-rsa.tgz                   100%[===================================================================>]  67.37K  --.-KB/s    in 0.001s  
    
    2024-06-21 12:29:20 (47.1 MB/s) - ‘/root/easy-rsa.tgz’ saved [68984/68984]
    
    
    Notice
    ------
    \'init-pki\' complete; you may now create a CA or requests.
    
    Your newly created PKI dir is:
    * /etc/openvpn/easy-rsa/pki
    
      * Using Easy-RSA configuration: /etc/openvpn/easy-rsa/vars
    
      * The preferred location for 'vars' is within the PKI folder.
        To silence this message, move your 'vars' file to your PKI
        or declare your 'vars' file with option: --vars=<FILE>
    
      * Using x509-types directory: /etc/openvpn/easy-rsa/x509-types
    
    
    * Using SSL: openssl 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024)
    
      * Using Easy-RSA configuration: /etc/openvpn/easy-rsa/vars
    
      * The preferred location for 'vars' is within the PKI folder.
        To silence this message, move your 'vars' file to your PKI
        or declare your 'vars' file with option: --vars=<FILE>
      Using configuration from /etc/openvpn/easy-rsa/pki/9cf8b5d9/temp.25125003
    -----
    
    Notice
    ------
    CA creation complete, and you may now import and sign cert requests.
    Your new CA certificate file for publishing is at:
    /etc/openvpn/easy-rsa/pki/ca.crt
    
    * Using SSL: openssl 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024)
    
      * Using Easy-RSA configuration: /etc/openvpn/easy-rsa/vars
    
      * The preferred location for 'vars' is within the PKI folder.
        To silence this message, move your 'vars' file to your PKI
        or declare your 'vars' file with option: --vars=<FILE>
    -----
    
    Notice
    ------
    Keypair and certificate request completed. Your files are:
    req: /etc/openvpn/easy-rsa/pki/reqs/server_hzpDk8MQtv2XfLfF.req
    key: /etc/openvpn/easy-rsa/pki/private/server_hzpDk8MQtv2XfLfF.key
    Using configuration from /etc/openvpn/easy-rsa/pki/94526a9c/temp.0712f246
    Check that the request matches the signature
    Signature OK
    The Subjec\'s Distinguished Name is as follows
    commonName            :ASN.1 12:'server_hzpDk8MQtv2XfLfF'
    Certificate is to be certified until Sep 24 12:29:21 2026 GMT (825 days)
    
    Write out a database with 1 new entries
    Database updated
    
    Notice
    ------
    Certificate created at:
    * /etc/openvpn/easy-rsa/pki/issued/server_hzpDk8MQtv2XfLfF.crt
    
    Notice
    ------
    Inline file created:
    * /etc/openvpn/easy-rsa/pki/inline/server_hzpDk8MQtv2XfLfF.inline
    
      * Using SSL: openssl OpenSSL 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024)
    
      * Using Easy-RSA configuration: /etc/openvpn/easy-rsa/vars
    
      * The preferred location for 'vars' is within the PKI folder.
        To silence this message, move your 'vars' file to your PKI
        or declare your 'vars' file with option: --vars=<FILE>
      Using configuration from /etc/openvpn/easy-rsa/pki/50c28f45/temp.50027e43
    
    Notice
    ------
    An updated CRL has been created.
    CRL file: /etc/openvpn/easy-rsa/pki/crl.pem
    
    2024-06-21 12:29:21 DEPRECATED OPTION: The option --secret is deprecated.
    2024-06-21 12:29:21 WARNING: Using --genkey --secret filename is DEPRECATED.  Use --genkey secret filename instead.
    * Applying /usr/lib/sysctl.d/10-apparmor.conf ...
      * Applying /etc/sysctl.d/10-console-messages.conf ...
      * Applying /etc/sysctl.d/10-ipv6-privacy.conf ...
      * Applying /etc/sysctl.d/10-kernel-hardening.conf ...
      * Applying /etc/sysctl.d/10-magic-sysrq.conf ...
      * Applying /etc/sysctl.d/10-map-count.conf ...
      * Applying /etc/sysctl.d/10-network-security.conf ...
      * Applying /etc/sysctl.d/10-ptrace.conf ...
      * Applying /etc/sysctl.d/10-zeropage.conf ...
      * Applying /etc/sysctl.d/50-cloudimg-settings.conf ...
      * Applying /usr/lib/sysctl.d/50-pid-max.conf ...
      * Applying /etc/sysctl.d/99-cloudimg-ipv6.conf ...
      * Applying /etc/sysctl.d/99-openvpn.conf ...
      * Applying /usr/lib/sysctl.d/99-protect-links.conf ...
      * Applying /etc/sysctl.d/99-sysctl.conf ...
      * Applying /etc/sysctl.conf ...
      kernel.apparmor_restrict_unprivileged_userns = 1
      kernel.printk = 4 4 1 7
      net.ipv6.conf.all.use_tempaddr = 2
      net.ipv6.conf.default.use_tempaddr = 2
      kernel.kptr_restrict = 1
      kernel.sysrq = 176
      vm.max_map_count = 1048576
      net.ipv4.conf.default.rp_filter = 2
      net.ipv4.conf.all.rp_filter = 2
      kernel.yama.ptrace_scope = 1
      vm.mmap_min_addr = 65536
      net.ipv4.neigh.default.gc_thresh2 = 15360
      net.ipv4.neigh.default.gc_thresh3 = 16384
      kernel.pid_max = 4194304
      net.ipv6.conf.all.use_tempaddr = 0
      net.ipv6.conf.default.use_tempaddr = 0
      net.ipv4.ip_forward = 1
      fs.protected_fifos = 1
      fs.protected_hardlinks = 1
      fs.protected_regular = 2
      fs.protected_symlinks = 1
      Created symlink /etc/systemd/system/multi-user.target.wants/openvpn@server.service → /etc/systemd/system/openvpn@.service.
      Created symlink /etc/systemd/system/multi-user.target.wants/iptables-openvpn.service → /etc/systemd/system/iptables-openvpn.service.
    
    Tell me a name for the client.
    The name must consist of alphanumeric character. It may also include an underscore or a dash.
    Client name: paris_client
    
    Do you want to protect the configuration file with a password?
    (e.g. encrypt the private key with a password)
       1) Add a passwordless client
          2) Use a password for the client
    Select an option [1-2]: 1
    
          * Using SSL: openssl OpenSSL 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024)
    
          * Using Easy-RSA configuration: /etc/openvpn/easy-rsa/vars
    
          * The preferred location for 'vars' is within the PKI folder.
            To silence this message, move your 'vars' file to your PKI
            or declare your 'vars' file with option: --vars=<FILE>
    -----
    
    Notice
    ------
    Keypair and certificate request completed. Your files are:
    req: /etc/openvpn/easy-rsa/pki/reqs/paris_client.req
    key: /etc/openvpn/easy-rsa/pki/private/paris_client.key
    Using configuration from /etc/openvpn/easy-rsa/pki/986a52b3/temp.0eb5a16b
    Check that the request matches the signature
    Signature ok
    The Subject\'s Distinguished Name is as follows
    commonName            :ASN.1 12:'paris_client'
    Certificate is to be certified until Sep 24 12:29:43 2026 GMT (825 days)
    
    Write out database with 1 new entries
    Database updated
    
    Notice
    ------
    Certificate created at:
    * /etc/openvpn/easy-rsa/pki/issued/paris_client.crt
    
    Notice
    ------
    Inline file created:
    * /etc/openvpn/easy-rsa/pki/inline/paris_client.inline
    Client paris_client added.
    
    The configuration file has been written to /home/ubuntu/paris_client.ovpn.
    Download the .ovpn file and import it in your OpenVPN client.


### You can check the OpenVPN service status with:

```shell
 systemctl status openvpn@server
```

### The output should be like this:



    ● openvpn.service - OpenVPN service
    Loaded: loaded (/usr/lib/systemd/system/openvpn.service; enabled; preset: enabled)
    Active: active (exited) since Fri 2024-06-21 12:29:17 UTC; 9min ago
    Docs: man:openvpn(8)
    Main PID: 2644 (code=exited, status=0/SUCCESS)
    CPU: 3ms
    
    Jun 21 12:29:17 [IP_Address] systemd[1]: Starting openvpn.service - OpenVPN service...
    Jun 21 12:29:17 [IP_Address] systemd[1]: Finished openvpn.service - OpenVPN service.
    If it is not working, you can use the following commands:


### More commands:

    systemctl status openvpn@server
    systemctl start openvpn@server
    systemctl restart openvpn@server
    systemctl enable openvpn@server

### Step 4. Download the Client Configuration File
    The configuration file has been written to /home/ubuntu/paris_pc.ovpn.
    Download the .ovpn file and import it in your OpenVPN client.


![Screenshot.png](http://marcinmitruk.link/img/How-to-Install-OpenVPN-on-Ubuntu-22.04/Screenshot1.png)

### Conclusion

In this tutorial, we have successfully installed OpenVPN on an Ubuntu 22.04 server running on AWS EC2.
We also created a client configuration file for connecting to the OpenVPN server.
You can now use the OpenVPN client to connect to your server securely
write conclusion based on the test results.

### References

[OpenVPN](https://openvpn.net/),
[Ubuntu](https://ubuntu.com/),
[AWS-EC2](https://aws.amazon.com/ec2/),
[OpenVPN-install](https://openvpn.net/vpn-software-packages/),
[Ubuntu-install](https://ubuntu.com/download/server),
[AWS-EC2-install](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html),
[Scrip-install](https://github.com/angristan/openvpn-install),
[Terraform-install](https://github.com/dumrauf/openvpn-terraform-install)
