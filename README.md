# ssl-with-gke-nginx-ingress

## GKE SSL Offloading with Nginx Ingress

This README outlines the process of setting up SSL offloading in Google Kubernetes Engine (GKE) with an HTTPS Load Balancer and a backend Nginx Ingress controller.

## Prerequisites

- A running GKE cluster
- `kubectl` configured for cluster interaction
- A domain with DNS access
- Helm, if preferred for Nginx Ingress installation

## Step-by-Step Guide

### Step 1: Install Nginx Ingress Controller

Install the Nginx Ingress controller using Helm:

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install my-nginx ingress-nginx/ingress-nginx
```
### Step 2: Reserve a Static IP Address
```
gcloud compute addresses create <address-name> --global
```

### Step 3: Create a Managed SSL Certificate 
create file with below content managed-cert.yaml
```
apiVersion: networking.gke.io/v1
kind: ManagedCertificate
metadata:
  name: my-ssl-cert
spec:
  domains:
    - yourdomain.com
    - yourdomain2.com
```

```
kubectl apply -f managed-cert.yaml
```

### Step 4: Configure the Ingress Resource
```
metadata:
  name: nginx-ingress
  annotations:
    kubernetes.io/ingress.global-static-ip-name: <address-name>
    networking.gke.io/managed-certificates: my-ssl-cert
    nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
```

###  Step 5: Configure DNS
Point your domain's DNS records to the static IP reserved earlier.

