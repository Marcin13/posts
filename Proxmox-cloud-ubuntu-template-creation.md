---
author: "Marcin M"
title: "Proxmox Cloud Ubuntu Template Creation"
date: 2022-06-24T13:16:41+01:00
description: "Lets create fresh Ubuntu template for Proxmox"
aliases: ["Proxmox"]
draft: false
categories: ["Proxmox"]
tags: ['proxmox','cloud-init']
ShowToc: true
TocOpen: true
searchHidden: false
---
# Cloud-Init Support

## Preparing Cloud-Init Templates

The first step is to prepare your VM. You can use any VM. Simply install the Cloud-Init packages inside the VM that you want to prepare. On Debian/Ubuntu-based systems this is as simple as:

```shell
apt-get install cloud-init
```

Already many distributions provide ready-to-use Cloud-Init images (provided as .qcow2 files), so alternatively you can simply download and import such images. For the following example,
we will use the cloud image provided by Ubuntu at [https://cloud-images.ubuntu.com]

download the image

```shell
wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
```

Create a new VM using Proxmox:

```shell
qm create 9000 --memory 2048 --net0 virtio,bridge=vmbr0
```

Import the downloaded disk to local-lvm storage:

```shell
qm importdisk 9000 bionic-server-cloudimg-amd64.img local-lvm
```

Attach the new disk to the VM as a SCSI drive:

```shell
qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-1
```

>> Note: Ubuntu Cloud-Init images require the virtio-scsi-pci controller type for SCSI drives.
>>

Configure a CD-ROM drive to pass the Cloud-Init data to the VM:
The next step is to configure a CD-ROM drive, which will be used to pass the Cloud-Init data to the VM.

```shell
qm set 9000 --ide2 local-lvm:cloudinit
```

To enable direct boot from the Cloud-Init image and improve booting speed, set the bootdisk parameter to scsi0 and restrict the BIOS to boot from the disk only:

```shell
qm set 9000 --boot c --bootdisk scsi0
```

Configure a serial console as a display, which is often required for many Cloud-Init images:

```shell
qm set 9000 --serial0 socket --vga serial0
```

Convert the VM into a template for faster deployment of linked clones:

```shell
qm template 9000
```

#### Image Cleaning with virt-sysprep

##### To clean the VM image before cloning, you can use the virt-sysprep command on the VM host

```shell
root@pve:~# locate –i *base-9001*
```

Run virt-sysprep on the VM disk to clean it:

```shell
 virt-sysprep -a /dev/pve/vm-9004-disk-0 \
  --update \
  --delete '/var/log' \
  --delete '/var/cache/apt/archives' \
  --delete '/tmp' \
  --delete '/root/.bash_history' \
  --delete '/etc/netplan/50-cloud-init.yaml'
  --delete '/var/lib/dbus/machine-id'
  --delete-ssh-hostkeys \
  --run-command 'truncate -s 0 /etc/hostname' \
  --run-command 'hostnamectl set-hostname localhost' \
  --run-command 'truncate -s 0 /etc/machine-id' \
  --run-command 'ln -s /etc/machine-id /var/lib/dbus/machine-id' \
  --run-command 'passwd -d root' \
  --run-command 'truncate -s 0 ~/.bash_history' \
  --run-command 'history -c'
  --run-command 'sudo apt clean'
  --run-command 'sudo apt autoremove'
```

with success!

```shell
root@pve:~# virt-sysprep -a /dev/pve/vm-103-disk-0
[   0.0] Examining the guest ...
[   7.7] Performing "abrt-data" ...
[   7.8] Performing "backup-files" ...
[   9.0] Performing "bash-history" ...
[   9.0] Performing "blkid-tab" ...
[   9.1] Performing "crash-data" ...
[   9.2] Performing "cron-spool" ...
[   9.3] Performing "dhcp-client-state" ...
[   9.3] Performing "dhcp-server-state" ...
[   9.3] Performing "dovecot-data" ...
[   9.3] Performing "ipa-client" ...
[   9.4] Performing "kerberos-hostkeytab" ...
[   9.4] Performing "logfiles" ...
[   9.9] Performing "machine-id" ...
[   9.9] Performing "mail-spool" ...
[   9.9] Performing "net-hostname" ...
[  10.0] Performing "net-hwaddr" ...
[  10.1] Performing "pacct-log" ...
[  10.1] Performing "package-manager-cache" ...
[  10.2] Performing "pam-data" ...
[  10.3] Performing "passwd-backups" ...
[  10.3] Performing "puppet-data-log" ...
[  10.3] Performing "rh-subscription-manager" ...
[  10.4] Performing "rhn-systemid" ...
[  10.5] Performing "rpm-db" ...
[  10.5] Performing "samba-db-log" ...
[  10.5] Performing "script" ...
[  10.5] Performing "smolt-uuid" ...
[  10.6] Performing "ssh-hostkeys" ...
[  10.6] Performing "ssh-userdir" ...
[  10.6] Performing "sssd-db-log" ...
[  10.7] Performing "tmp-files" ...
[  10.8] Performing "udev-persistent-net" ...
[  10.8] Performing "utmp" ...
[  10.8] Performing "yum-uuid" ...
[  10.9] Performing "customize" ...
[  10.9] Setting a random seed
[  11.0] Setting the machine ID in /etc/machine-id
[  11.1] Performing "lvm-uuids" ...
root@pve:~# 
```

```shell
root@pve:~# locate –i *vm-9001*
/dev/pve/vm-9001-cloudinit
/dev/pve/vm-9001-disk-0
/run/udev/links/\x2fpve\x2fvm-9001-cloudinit
/run/udev/links/\x2fpve\x2fvm-9001-cloudinit/b253:14
/run/udev/links/\x2fpve\x2fvm-9001-disk-0
/run/udev/links/\x2fpve\x2fvm-9001-disk-0/b253:13
root@pve:~# 
```

[virto-sysprep-ubuntu][https://manpages.ubuntu.com/manpages/jammy/man1/virt-sysprep.1.html]

[Cloud-Init_Support][https://pve.proxmox.com/wiki/Cloud-Init_Support]

you can install it, it shouldn't break your install.

I'd install it with apt install --no-install-recommends --no-install-suggests libguestfs-tools.

just be aware that you can't manage proxmox guests with libvirt.

[https://pve.proxmox.com/wiki/Cloud-Init_Support]: https://pve.proxmox.com/wiki/Cloud-Init_Support
[https://cloud-images.ubuntu.com]: https://cloud-images.ubuntu.com
