# GeoServer On Kubernetes 
## Description

This is a simple way of deploying your GeoServer to on Kubernetes.The GeoServer is hosted on 
AKS and ingress to loadbalance and to route the HTTP traffic to multiple hostname at 
the same ip address eg. nz.ip.nip.io and aus.ip.nip.io. Each Pods in the GeoServer will be 
connected to the blob storage using blobfuse for simple update data.  

## Requirement
I will be using Azure Kubernetes Service to deploy the application. Since, LoadBalancer is required.
1. AKS
2. kartoza/geoserver https://github.com/kartoza/docker-geoserver
3. nginx-ingress.
4. BlobStorage
## Infrastructure


## Getting Started
#### First, you will need a AKS. 
You can do this by following the AKS tutorial.
https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough

#### Second, you can install your own Nginx-ingress.
You can also do this by following the AKS ingress installation. 
https://docs.microsoft.com/en-us/azure/aks/ingress-basic

#### Third, clone the repository or copy everything in from the folder.
mygeoserver.yaml and mygeoserver2.yaml: is used to deploy GeoServer onto the Cluster. 
service.yaml and service2.yaml: is used to exposed the deployments with the type of ClusterIP.
ingress.yaml: Since ClusterIP can only be access by the cluster, ingress.yaml is used to route 
the HTTP request from outside the cluster to within the cluster. 
Volume.yaml: this file create a persistent Volume using Azure Blobstorage.

#### Forth, lets deploy.
Assuming you follow the AKS nginx-ingress tutorial, you should have the a namespaces of 
ingress-basic.
```
kubectl apply -f mygeoserver.yaml -n ingress-basic
kubectl apply -f mygeoserver2.yaml -n ingress-basic
kubectl apply -f service.yaml -n ingress-basic
kubectl apply -f service2.yaml -n ingress-basic
kubectl apply -f ingress.yaml -n ingress-basic
```
#### Note
When replicate more than 1 deployment for example, scale mygeoserver = 2, you will not be able 
to login to GeoServer because for some reason the session is not sticky. To make the session 
sticky, you will need to add some nginx-ingress annotation as shown in k8s/ingress.yaml. 

This should deploys everything. You can check the detail by typing
```
#get all pods
kubectl get pods --all-namespaces
#get ingress
kubectl get ing -n ingress-basic
#get all the services who has the namespace of ingress-basic 
kubectl get svc -n ingress-basic
```
Now you should able to access aus.ip.nip.io/geoserver and nz.ip.nip.io/geoserver.
