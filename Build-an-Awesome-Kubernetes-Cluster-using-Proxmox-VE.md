---
author: "Marcin M"
title: "Build an Awesome Kubernetes Cluster Using Proxmox VE"
date: 2023-06-09T11:49:23+01:00
description: "Building an Impressive Kubernetes Cluster with Proxmox VE"
aliases: []
draft: true
categories: []
tags: []
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Build-an-Awesome-Kubernetes-Cluster-Using-Proxmox-VE.md"
---
### Requirements:

To complete this tutorial, you'll need the following:

* Two instances of Ubuntu Server 22.04 (one for the controller and at least one for a node)
* The instances should have either a static IP address or a DHCP reservation to prevent IP address changes
* The controller node should have a minimum of 2GB of RAM and 2 CPU cores
* The node instances can have 1GB of RAM and 1 CPU core (you can increase these resources as desired)

Please note that this tutorial is specifically tailored for Ubuntu Server 22.04. If you opt to use a different
distribution or a different version of Ubuntu, the commands provided may not be compatible.

### Setting up the cluster

#### Setting up a static IP

Please note that using a static lease for your network configuration is recommended. However, if you need to set up a
static IP, you can refer to the following example of a Netplan config as a starting point for your configuration:

```shell
network:    version: 2
    ethernets:
        eth0:
            addresses: [10.0.1.110/24]
            nameservers:
                addresses: [10.0.1.1]
            routes:
                - to: default
                  via: 10.0.1.1
```

At the bottom, I will include a script with a description of how to set up a quick VM.
--------------> This script you do on **Master** and **Node** VM <------------------------------

```shell
#!/bin/bash

#Update
sudo bash -c "apt update & apt upgrade -y && apt autoremove -y"

# Install containerd
sudo apt install containerd -y

# Create the containerd configuration directory
sudo mkdir /etc/containerd

# Generate the default containerd configuration
containerd config default | sudo tee /etc/containerd/config.toml

# Edit the containerd configuration file
# Within the file, find the line [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
# Underneath that, find the SystemdCgroup option and change it to true
# Save and exit the editor
sudo sed -i 's/^\(\s*SystemdCgroup\s*=\s*\)false/\1true/' /etc/containerd/config.toml

# Disable swap
sudo swapoff -a

# Edit the sysctl.conf file
# Within the file, look for the line #net.ipv4.ip_forward=1
# Uncomment that line by removing the # symbol in front of it
# Save and exit the editor
sudo sed -i '/\sswap\s/s/^/#/' /etc/fstab

#enable bridging, we only need to edit one config file
sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf

# Edit the k8s.conf file
# Add the line "br_netfilter" to the file
# Save and exit the editor
echo "br_netfilter" | sudo tee /etc/modules-load.d/k8s.conf

# Reboot the system
sudo reboot
```

#### Installing Kubernetes

The next step is to install the packages that are required for Kubernetes. First, we’ll add the required GPG key:

```shell
#!/bin/bash

# Add the Kubernetes repository key
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# Add the Kubernetes repository
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update the packages list
sudo apt update

# Install the Kubernetes components
sudo apt install -y kubelet kubeadm kubectl

#Source 
#https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
```

#### And now specific to proxmox or VM before cloning we need

To clean VM before clonning I run this cmmands

```shell
#!/bin/bash

# Script to perform system cleanup and shutdown

# Update package repositories and upgrade installed packages
sudo apt update && sudo apt -y upgrade

# Remove unused packages
sudo apt -y autoremove

# Clean package cache
sudo apt clean

# Reset the hostname
sudo truncate -s0 /etc/hostname
sudo hostnamectl set-hostname localhost

# Remove network configuration
sudo rm /etc/netplan/50-cloud-init.yaml

# Reset machine ID
sudo truncate -s0 /etc/machine-id
sudo rm /var/lib/dbus/machine-id
sudo ln -s /etc/machine-id /var/lib/dbus/machine-id

# Disable root password
sudo passwd -dl root

# Clear command history
sudo truncate -s0 ~/.bash_history
history -c

# Shutdown the system
sudo shutdown -h now

```

Additionally, I execute this script on the VM host to utilize the virt-sysprep command for image sanitization.

```shell

# Image Cleanup Script using virt-sysprep

# Find the disk
find / -name "*vm-9004*"

# Run virt-sysprep command to clean the image
virt-sysprep -a /dev/pve/vm-9004-disk-0 \
  --update \
  --delete '/etc/ssh/ssh_host_*'
  --delete '/var/log' \
  --delete '/var/cache/apt/archives' \
  --delete '/tmp' \
  --delete '/root/.bash_history' \
  --delete '/etc/netplan/50-cloud-init.yaml'
  --delete '/var/lib/dbus/machine-id'
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

### ------ control node only -----

#### Initialization:

On the controller node, execute the necessary steps to initialize the Kubernetes cluster: 
Set on your need **--control-plane-endpoint=** and **--node-name=** 
Remember **--node-name=** lowercase only.

```shell

sudo kubeadm init --control-plane-endpoint=10.0.1.110 --node-name=k8s-master --pod-network-cidr=10.244.0.0/16   

```

and output:

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join 10.0.1.110:6443 --token vtrg5w.3brmfalp9mkzy1bh \
        --discovery-token-ca-cert-hash sha256:54895c86a6ebcd531048181ce40f135b29bc3e384e389befe4de813c5f979124 \
        --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.0.1.110:6443 --token vtrg5w.3brmfalp9mkzy1bh \
        --discovery-token-ca-cert-hash sha256:54895c86a6ebcd531048181ce40f135b29bc3e384e389befe4de813c5f979124 
```

Deinitialization:
On the controller node, execute the necessary steps to deinitialize the Kubernetes cluster:

```shell

sudo kubeadm reset

sudo rm -r /etc/cni/net.d

rm -r .kube

```

#### Install an Overlay Network

The following command will install the Flannel overlay network (an overlay network is required for this to function).

```shell

kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml   

```

### ------ control node only end -----

#### Adding Nodes

The join command, which you will receive from the output once you initialize the cluster, can be run on your node instances now to get them joined to the cluster. The following command will help you monitor which nodes have been added to the controller (it can take several minutes for them to appear):

```shell

kubectl get nodes

```

output:

```shell

marcin@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES           AGE   VERSION
k8s-master   Ready    control-plane   16h   v1.27.2
k8s-node-1   Ready    <none>          75m   v1.27.2
k8s-node-2   Ready    <none>          63m   v1.27.2
k8s-node-3   Ready    <none>          38m   v1.27.2

```

If for some reason the join command has expired, the following command will provide you with a new one:

```shell

kubeadm token create --print-join-command

```

and pass this token to node VM.

![Screenshot.png](http://marcinmitruk.link/img/Build-an-Awesome-Kubernetes-Cluster-Using-Proxmox-VE/Screenshot1.png)

### Useful links:
