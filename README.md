# k3s-cert-manager

The goal of this project is to authorize SSL certificates on my site or application hosted in k3s.
In this project I directly use the domain name provided free of charge.
You can get one for free on your freebox panel in the tab > Freebox settings > domain name.

## Prerequisites

- Have an operational K3s cluster.
- Have Traefik installed (K3s generally deploys it by default).
- Having a domain name in our example we will call it **tuto.freeboxos.fr**.
- Port 80 and 443 redirect to your server's IP.
    >At free: freebox panel in the tab > Freebox settings > domain name > Port management.

## Install Cert-Manager

Cert-Manager is a Kubernetes certificate manager that automates obtaining and renewing SSL certificates.

````bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.3/cert-manager.yaml
````

The url may change depending on when you follow this readme: [cert-manager](https://cert-manager.io/docs/installation/kubectl/).

Verify that the Cert-Manager pods are running:

````bash
kubectl get pods --namespace cert-manager
````

Cert-Manager uses Issuers or ClusterIssuers to obtain certificates. We will configure a ClusterIssuer using ACME to obtain the certificates via Let's Encrypt.

````bash
kubectl apply -f cluster-issuer.yaml
````

## Request SSL certificate

Then we will request the SSL certificate for our domain name, this will be stored as secret and will be automatically renewed by Cert-Manager.

````bash
kubectl apply -f certificate.yaml
````

After a few minutes, check that the certificate is generated.

````bash
kubectl describe certificate tuto-tls
````

If the certificate is not generated, check the Cert-Manager pod logs.

````bash
kubectl logs -n cert-manager deploy/cert-manager
````

After that there is an example in ingress.yml to use your secret to apply your SSL certificate.