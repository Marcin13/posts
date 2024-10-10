---
author: "Marcin M"
title: "Argocd With Kind"
date: 2024-10-10T11:39:29+01:00
description: "Sample article showcasing basic skills. "
aliases: []
draft: true
categories: ['DevOps', 'Automation']
tags: ['kind', 'argocd']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Argocd-with-kind.md"
---
![Screenshot.png](http://marcinmitruk.link/img/Argocd-with-kind/01.webp)

## Introduction

In this tutorial, I will show you how to install Argo CD locally on Kali Linux using a Kind environment. Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes, which simplifies managing and deploying applications.

### Prerequisites

Before you begin, ensure you have the following:

  - A system running Kali Linux.
  - Kubernetes in Docker (Kind) installed and configured on your Kali Linux system.
  - A foundational understanding of Linux, Kubernetes, and Argo CD.

#### Step 1: Run KIND with a Custom Setup

This step sets up a four-node Kubernetes cluster using Kind (Kubernetes in Docker), where one node is the control plane and the other three are worker nodes. This configuration also maps ports 80 and 443 on the host to the same ports in the cluster, which will be used to access Argo CD in the browser.
Create a new file called **kind-config.yaml** and add the following configuration:
Create folder for kind configuration, in this case, it will be **argocd-kind** and create a file **kind-config.yaml** with the following content:

```bash
# kind-config.yaml
# Four node (three workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
# Name Your Cluster, default kind
name: my-cluster 
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80          # this is the port that will be used in the browser
    protocol: TCP
  - containerPort: 443
    hostPort: 443         # this is the port that will be used in the browser
    protocol: TCP
- role: worker
- role: worker
- role: worker

```

 Soon after, run the following command to create the cluster:

 ```bash
kind create cluster --config argocd-kind/kind-config.yaml
 ```

if you **cd** into the folder where the kind-config.yaml is located, you can run the following command:

```bash
kind create cluster --config kind-config.yaml
```

now wait for freshly created cluster to be ready.
when the cluster is ready, you can check the status of the cluster by running the following command:

```bash
kubectl cluster-info --context kind-my-cluster
kubectl get nodes
```

#### Step 2: Install NGINX Ingress Controller

The NGINX Ingress Controller is needed to route external traffic to services inside the cluster. Run the following command to install it:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

To verify that the Ingress Controller is running, run the following command:

```bash
kubectl get pods -n ingress-nginx
```

#### Step 3: Configure Ingress for Argo CD

Go to **/etc/hosts** and add the following line to map the domain to your localhost:
Do this to use it with ingress:

```bash
127.0.0.1 argocd.local
# or ip of your computer
# 198.162.1.4 argocd.local
```

And before we start creating argo cd resources, we need to create a namespace for argo cd:

```bash
kubectl create namespace argocd
```

Create an Ingress resource to route traffic to the Argo CD server. This will allow you to access Argo CD in the browser using a domain or local host.
Create a new file called **argocd-ingress.yaml** and add the following configuration:

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod  # Use cert-manager to handle SSL certificates
    nginx.ingress.kubernetes.io/backend-protocol: "HTTP"  # Backend Argo CD server uses HTTP
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"  # Enforce HTTPS
  
spec:
  ingressClassName: nginx  # Specify NGINX as the ingress controller
  rules:
    - host: argocd.local  # Change this to your domain or local host
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: argocd-server  # Argo CD service handling the request
                port:
                  name: https         # Service uses HTTPS
  tls:
    - hosts:
        - argocd.local               # The same domain/host for TLS certificate
      secretName: argocd-server-tls  # TLS secret created automatically by cert-manager
```

Verify that the Ingress resource is created by running the following command:

```bash
kubectl get ingress -n argocd
```

#### Step 4 Install Argo CD

Now install Argo CD by applying the official installation manifest. This will create the necessary resources in the argocd namespace

```bash
kubectl create namespace argocd # if you haven't created it yet
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

verify that the Argo CD resources are created by running the following command:

```bash
kubectl get all -n argocd
```

#### Step 5 Install cert-manager

Cert-manager helps automatically generate and manage SSL certificates for your services. This will handle HTTPS for the Argo CD server.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.1/cert-manager.yaml
```
verify that the cert-manager resources are created by running the following command:

```bash
kubectl get all -n cert-manager
```

#### Step 6 Configure ClusterIssuer for Cert-Manager

Configure a ClusterIssuer resource to use Let's Encrypt as the certificate authority for generating SSL certificates.
Create a new file called **letsencrypt-prod-cluster-issuer.yaml** and add the following configuration:

```bash
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod  # Name of the ClusterIssuer
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory  # Use Let's Encrypt's production ACME server
    email: your@mail.com  # Replace with your actual email
    privateKeySecretRef:
      name: letsencrypt-prod-account-key  # Secret for storing account private key
    solvers:
      - http01:
          ingress:
            class: nginx  # Specify NGINX as the ingress class for solving HTTP challenges

```
Check if the ClusterIssuer resource is created by running the following command:

```bash
kubectl get clusterissuers
```

#### Step 7 Configure Argos ConfigMap

Modify the Argo CD ConfigMap to allow access without HTTPS verification. This is useful when running locally in a development environment.
Create a new file called **argocd-cmd-params-cm.yaml** and add the following configuration:

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cmd-params-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cmd-params-cm
    app.kubernetes.io/part-of: argocd
data:
   ## Server properties
   server.insecure: "true"  # Allow insecure access (disables HTTPS verification)

```

There is good idea to restart the argo cd deployment to apply the changes:

```bash
kubectl rollout restart deployment argocd-server -n argocd
```

##### Step 9 Access Argo CD in the Browser

Now your Argo CD setup is complete, and it should be accessible from your browser at http://argocd.local or https://argocd.local, depending on your configuration. You can log in to the Argo CD web interface using the default username **admin** and the password (usually auto-generated or obtained from a Kubernetes secret).

#### Obtaining the Argo CD Password

To obtain the password for the Argo CD web interface, run the following command:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Congratulations! You have successfully installed Argo CD on your local Kali Linux system using Kind. You can now use Argo CD to manage and deploy applications on your Kubernetes cluster.






### Useful links
