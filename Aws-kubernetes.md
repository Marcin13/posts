---
author: "Marcin M"
title: "Deploying PrestaShop with NGINX and SSL on AWS EKS"
subtitle: "A step-by-step guide using Kubernetes, Helm, and cert-manager"
date: 2024-09-26T17:11:56+01:00
description: "Learn how to deploy PrestaShop on AWS EKS using NGINX Ingress, cert-manager for SSL, and best practices for cost-effective, production-ready setups."
aliases: ['PrestaShop']
draft: true
categories: ['nginx', 'kubernetes', 'aws']
tags: ['prestashop', 'nginx', 'helm', 'cert-manager', 'eks', 'kubernetes', 'docker']
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Prestashop-with-nginx.md"
  Text: "Suggest changes to this post"
---

![Screenshot.png](http://marcinmitruk.link/img/Prestashop-with-nginx/01.webp)


# This is a file that contains cmd I run in the terminal
## The purpose of this file is to deploy PrestaShop on AWS EKS using NGINX Ingress, cert-manager and best practices for cost-effective, production-ready setups.

Update kubeconfig


    aws eks update-kubeconfig --region eu-central-1 --name myr-eks


Aws get user, to check if the user is the one we expect


    aws iam get-user


Verify Configuration, to check if the configuration is correct


    kubectl get nodes


Add & update Helm Repo, jetstack is the repo for cert-manager


    helm repo add jetstack https://charts.jetstack.io && helm repo update

Install cert-manager, with **-f** we can pass custom values


    helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace  -f oc-cert-manager-values.yaml


Expected output for cluster-issuers


    NAME: cert-manager
    LAST DEPLOYED: Fri Jan 17 09:10:18 2025
    NAMESPACE: cert-manager
    STATUS: deployed
    REVISION: 1
    TEST SUITE: None
    NOTES:
    ⚠️  WARNING: `installCRDs` is deprecated, use `crds.enabled` instead.
    cert-manager v1.16.3 has been deployed successfully!
    
    In order to begin issuing certificates, you will need to set up a ClusterIssuer
    or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).
    
    More information on the different types of issuers and how to configure them
    can be found in our documentation:
    
    https://cert-manager.io/docs/configuration/
    
    For information on how to configure cert-manager to automatically provision
    Certificates for Ingress resources, take a look at the `ingress-shim`
    documentation:
    
    https://cert-manager.io/docs/usage/ingress/

✅ Important: 

If anything goes wrong, you can uninstall cert-manager with the following command

### {[ Uninstall cert-manager ]}


    helm uninstall cert-manager --namespace cert-manager




Verify cert-manager installation


    kubectl get pods --namespace cert-manager


Expected output from cert-manager installation


    NAME                                       READY   STATUS    RESTARTS   AGE
    cert-manager-56d4c7dfb7-zjw9t              1/1     Running   0          9m4s
    cert-manager-cainjector-6dc54dcd78-6xnwr   1/1     Running   0          9m4s
    cert-manager-webhook-5d74598b49-htv6m      1/1     Running   0          9m4s


If everything is OK, let's install cluster-issuers


    kubectl apply -f cluster-issuers.yaml # one for prod and one for staging


Verify cluster-issuers installation


    kubectl get clusterissuers


Expected output for cluster-issuers


    NAME                     READY   AGE
    letsencrypt-production   True    52s
    letsencrypt-staging      True    70s


Now we need to install the ingress controller, in this case, we will use nginx


    helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace -f oc-nginx-ingress-values.yaml


### {[ Uninstall nginx-ingress ]}


    helm uninstall nginx-ingress --namespace ingress-nginx


Verify nginx-ingress installation


    kubectl get pods --namespace ingress-nginx


Expected output for nginx-ingress installation


    NAME                                                      READY   STATUS    RESTARTS   AGE
    nginx-ingress-ingress-nginx-controller-69786fcbcf-ns7bz   1/1     Running   0          70s


But the most important, we need to verify that the aws load balancer is created (and is not for free)


    aws elb describe-load-balancers --region eu-central-1


also, we can get public ip of the load balancer


    kubectl get svc -n ingress-nginx


or more like DNS name


     NAME                                               TYPE           CLUSTER-IP       EXTERNAL-IP                                                                  PORT(S)                      AGE
    nginx-ingress-ingress-nginx-controller             LoadBalancer   10.100.187.190   a1f89970798f0470994dcbff9157a272-1612871129.eu-central-1.elb.amazonaws.com   80:32072/TCP,443:30113/TCP   8m14s
    nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.100.195.55    <none>                                                                       443/TCP                      8m14s


when you pass this url to the browser, you should see the default nginx page; in my case, 404 pages are not found.

Finally, we can start deploying our apps

    kubectl apply -f deployment.yaml

✅ Important: `Disclaimer about services and AWS`

When using LoadBalancer, AWS will create a new load balancer for each service, so be careful with the costs.
NodePort is a good option for testing, but not for production, you need to manage the ports and the security groups.
Cluster IP is a good option for internal services, but you need to manage the ingress controller.
Ingress controller when you create new ingress, it will create a new rule in the load balancer, so you can have multiple services in the same load balancer.











### Useful links:

