---
author: "Marcin M"
title: "Proxmox Cloud Ubuntu Template Creation"
date: 2022-06-24T13:16:41+01:00
description: "Lets create fresh Ubuntu template for Proxmox"
aliases: []
draft: false
categories: ["Proxmox"]
tags: ['proxmox','cloud-init']
ShowToc: true
TocOpen: true
searchHidden: false
---
# Cloud-Init Support

## Preparing Cloud-Init Templates

Already many distributions provide ready-to-use Cloud-Init images (provided as .qcow2 files), so alternatively you can simply download and import such images. For the following example,
we will use the cloud image provided by Ubuntu at [https://cloud-images.ubuntu.com][https://cloud-images.ubuntu.com]

download the image
```shell
wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
```
create a new VM
```shell
qm create 9000 --memory 2048 --net0 virtio,bridge=vmbr0
```
import the downloaded disk to local-lvm storage
```shell
qm importdisk 9000 bionic-server-cloudimg-amd64.img local-lvm
```
finally attach the new disk to the VM as scsi drive
```shell
qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-1
```
>> Ubuntu Cloud-Init images require the virtio-scsi-pci controller type for SCSI drives.

### Add Cloud-Init CD-ROM drive
The next step is to configure a CD-ROM drive, which will be used to pass the Cloud-Init data to the VM.
```shell
qm set 9000 --ide2 local-lvm:cloudinit
```
To be able to boot directly from the Cloud-Init image, set the bootdisk parameter to scsi0, and restrict BIOS to boot from disk only. This will speed up booting, because VM BIOS skips the testing for a bootable CD-ROM.
```shell
qm set 9000 --boot c --bootdisk scsi0
```
Also configure a serial console and use it as a display. Many Cloud-Init images rely on this, as it is an requirement for OpenStack images.
```shell
qm set 9000 --serial0 socket --vga serial0
```
In a last step, it is helpful to convert the VM into a template. From this template you can then quickly create linked clones. The deployment from VM templates is much faster than creating a full clone (copy).
```shell
qm template 9000
```




























```shell
root@pve:~# locate –i *base-9001*
```
clear template
```shell
virt-sysprep -a /dev/pve/base-9001-disk-0
[   0.0] Examining the guest ...
virt-sysprep: error: libguestfs error: guestfs_launch failed.
This usually means the libguestfs appliance failed to start or crashed.
Do:
  export LIBGUESTFS_DEBUG=1 LIBGUESTFS_TRACE=1
and run the command again.  For further information, read:
  http://libguestfs.org/guestfs-faq.1.html#debugging-libguestfs
You can also run 'libguestfs-test-tool' and post the *complete* output
into a bug report or message to the libguestfs mailing list.

If reporting bugs, run virt-sysprep with debugging enabled and include the 
complete output:

  virt-sysprep -v -x [...]
root@pve:~# 
```

z sukcesem 
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

[https://pve.proxmox.com/wiki/Cloud-Init_Support]: https://pve.proxmox.com/wiki/Cloud-Init_Support

[https://cloud-images.ubuntu.com]: https://cloud-images.ubuntu.com

Using virt-customize to install packages on a guest
[run cmd before conwerting to template][https://codingpackets.com/blog/proxmox-import-and-use-cloud-images/]

Install Software packages inside an image
```shell
$ virt-customize -a rhel-server-7.6.qcow2 --install [vim,bash-completion,wget,curl,telnet,unzip]
[   0.0] Examining the guest ...
[   2.1] Setting a random seed
[   2.1] Installing packages: [vim bash-completion wget curl telnet unzip]
[ 563.2] Finishing off

$ virt-customize -a rhel-server-7.6.qcow2 --install net-tools
```
https://computingforgeeks.com/customize-qcow2-raw-image-templates-with-virt-customize/