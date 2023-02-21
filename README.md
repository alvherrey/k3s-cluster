# k3s-cluster
Quick start guide, and manifest for setup a k3s cluster with traefik ingress

## Requirements
https://docs.k3s.io/installation/requirements

 - At least 2 or more nodes 
 - Domain 
 - Open 6443 port on the router

## Install k3s
Install **server** with config file

    curl -sfL https://get.k3s.io | K3S_CONFIG_FILE=./config.yaml sh -

Get token

    sudo cat /var/lib/rancher/k3s/server/node-token
    
Install **agent**

    curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -

File kube.config

    /etc/rancher/k3s/k3s.yaml
Check config

    k3s check-config  

  
Status service  

    systemctl status k3s.service
## Uninstall k3s
Uninstall **server**

    /usr/local/bin/k3s-uninstall.sh  

Uninstall **agent**

    /usr/local/bin/k3s-agent-uninstall.sh

## Add monitoring
This deploy a stack with prometheus-operator, grafana dashboards, and some rules.

    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    kubectl create namespace monitoring
    helm install [RELEASE_NAME] --namespace monitoring prometheus-community/kube-prometheus-stack
    helm uninstall [RELEASE_NAME] --namespace monitoring
Add Grafana Ingress

    kubectl apply -f grafana.yaml

## Add cert-manager
Needed for auto-generate letsencrypt certificates.

    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
    helm repo add jetstack https://charts.jetstack.io
    kubectl create namespace cert-manager
    helm install [RELEASE_NAME] --namespace cert-manager --version v1.11.0 jetstack/cert-manager
    helm uninstall [RELEASE_NAME] --namespace cert-manager

Deploy ClusterIssuer

    kubectl apply -f prod_issuer.yaml
    kubectl apply -f staging_issuer.yaml

## Add hello-app

Deploy hello-app

    kubectl create namespace hello-app    
    kubectl apply -f hello-app.yaml

> Written with [StackEdit](https://stackedit.io/).
