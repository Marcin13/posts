---
author: "Marcin M"
title: "How to Install Kubernetes Cluster OnOracle Always Free Ubuntu 22.04"
date: 2024-07-28T18:33:14+01:00
description: "Sample article showcasing basic skills. "
aliases: []
draft: true
categories: []
tags: []
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/How-to-Install-Kubernetes-Cluster-OnOracle-Always-Free-Ubuntu-22.04.md"
---
Kubernetes is a powerful container orchestration platform used for automating the deployment, scaling, and management of 
containerised applications. In this guide, we will walk you through the step-by-step process of installing Kubernetes on
Ubuntu 22.04. This cluster configuration includes a master node and worker nodes, allowing you to harness the full power
of Kubernetes.

Kubernetes Nodes

In a Kubernetes cluster, you will encounter two distinct categories of nodes:

Master Nodes: These nodes play a crucial role in managing the control API calls for various components within the
Kubernetes cluster. This includes overseeing pods, replication controllers, services, nodes, and more.

Worker Nodes: Worker nodes are responsible for providing runtime environments for containers. It’s worth noting that a 
group of container pods can extend across multiple worker nodes, ensuring optimal resource allocation and management.

### Prerequisites

Before diving into the installation, ensure that your environment meets the following prerequisites:

    An Ubuntu 22.04 system.
    Privileged access to the system (root or sudo user).
    Active internet connection.
    Minimum 2GB RAM or more.
    Minimum 2 CPU cores (or 2 vCPUs).
    20 GB of free disk space on /var (or more).



### Step 1: Update System Packages
```bash
sudo bash -c "apt update & apt upgrade -y && apt autoremove -y"
```
### Install containerd
```bash
sudo bash -c "apt install -y containerd"
```
### Step 2: Create the containerd configuration directory
```bash
sudo mkdir -p /etc/containerd
```
### Step 3: Generate the containerd configuration file
```bash
sudo containerd config default | sudo tee /etc/containerd/config.toml
```
### Step 4: Edit the containerd configuration file
```bash
sudo sed -i 's/^\(\s*SystemdCgroup\s*=\s*\)false/\1true/' /etc/containerd/config.toml
```
### Step 5:Disable swap 
```bash
sudo swapoff -a
```
### Step 6: Edit the sysctl.conf file
```bash 
sudo sed -i '/\sswap\s/s/^/#/' /etc/fstab
```
### Step 7: bridging, we only need to edit one config file
```bash
sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
```
### Step 8: Edit the k8s.conf file, add the line "br_netfilter" to the file
```bash
echo "br_netfilter" | sudo tee /etc/modules-load.d/k8s.conf
```
### Step 9: Reboot the system
```bash
sudo reboot
```

Or you can use a script to automate the process
```bash
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
Installing Kubernetes
## If something goes wrong with added repository, you can visit this link and check the latest version
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

The next step is to install the packages that are required for Kubernetes. First, we’ll add the required GPG key:
### Step 10: Add the Kubernetes GPG key
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```
### Step 12: Add the Kubernetes repository
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
### update the package list
```bash
sudo apt-get update
```
### Step 13: Install Kubernetes packages
```bash
sudo apt install -y kubelet kubeadm kubectl
```
### Step 14: Hold the Kubernetes packages
```bash
sudo apt-mark hold kubelet kubeadm kubectl
```
### Step 15: Enable and start the kubelet service
```bash
sudo systemctl enable --now kubelet
```
### Step 16: Open the required ports **and configure security groups**
```bash
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 6443 -j ACCEPT && sudo netfilter-persistent save
```
### Step 17: edit hosts file
```bash
echo "132.145.49.32 larablogger.com" | sudo tee -a /etc/hosts
```
### Step 18: Restart the kubeadm service
```bash
sudo kubeadm reset
```
### Step 19: Initialize the Kubernetes cluster
```bash
sudo kubeadm init --control-plane-endpoint=larablogger.com:6443 --apiserver-advertise-address=10.0.0.89 --node-name=k8s-master --pod-network-cidr=10.244.0.0/16
```
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    sudo systemctl restart kubelet && sudo systemctl enable kubelet && sudo systemctl restart containerd

sudo mkdir -p /etc/crictl
sudo nano /etc/crictl/config.yaml
sudo crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps -a
### Step 20 Restart services
```bash
sudo systemctl restart kubelet && sudo systemctl enable kubelet && sudo systemctl restart containerd
```
###
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

### and finally if everything is ok you should see the nodes
```bash
kubectl get nodes
```
### Now you can join the worker nodes to the cluster

##### If for some reason the join command has expired, the following command will provide you with a new one:
### Step 21: Generate the join command
```bash
kubeadm token create --print-join-command
```
### Step 22: List the nodes
```bash
### 
```bash
kubectl get nodes
```

![Screenshot.png](http://marcinmitruk.link/img/How-to-Install-Kubernetes-Cluster-OnOracle-Always-Free-Ubuntu-22.04/Screenshot1.png)






### Useful links:
https://hbayraktar.medium.com/how-to-install-kubernetes-cluster-on-ubuntu-22-04-step-by-step-guide-7dbf7e8f5f99

