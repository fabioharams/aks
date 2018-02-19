# Azure AKS (managed Kubernetes) - Small guide to test #

This guide will demonstrate how to test Azure Container Service - Kubernetes as a Managed Service (AKS). 

AKS offer a nice feature to manage Kubernetes cluster without complexity. One important feature that differs from ACS (using Kubernetes) and AKS is about the absence of the Master node as a management endpoint for the Kubernetes cluster. The role for the Master Node is responsibility of Azure and simplifies the management. If you are new in kubernetes we recommend this solution to be evaluated.


Before you follow the procedures it's very important to understand all the options to use Containers on Azure. 

## Understand each choice ##

1) Virtual Machine with Docker

This scenario is recommended for companies evaluating Containers. You have options to use here:
- Virtual Machines using Linux: There is a lot of distros supporting Docker engine (including Ubuntu, CoreOS, CentOS, SUSE, Red Hat, etc) 
- Virtual Machines using Windows: Windows 10 and Windows Server 2016 support Windows Containers using Docker engine. Also Hyper-V Containers is supported. 
---
Important: you must understand that one container running Linux cannot be run on Windows Containers  just because both are using Docker engine. Remember that the container use a shared kernel from host. 

If you are deploying containers based on .Net Core 2.0 then you can move between these 2 different hosts with minor adjusts. 

Another option: Windows Server 2016 build 1709 (With Hyper-v Containers and Linux Subsystem) allow Hyper-V Containers running Linux (using Ubuntu). This is another great alternative to run Linux Containers on Windows Server 2016 without using Virtual Machines. 

---

2) Containers with Management/Orchestrator

This scenario use more than 1 virtual machine (with Docker) running some management system / orchestration. You have a lot of options like Docker Swarm, DCOS, Rancher, Kubernetes, Portainer, OpenShift, etc. It requires a lot of work because you need to manually setup the networking, storage, etc. 


3) Container Service

The scenario for Container Service solves a lot of work to setup storage, nodes, networking, etc. Still use virtual machines as nodes but you don't need to care about the difficult part of the setup because it's normally a solution from many Cloud Providers. 
Microsoft offers the Azure Container Service (ACS) using 3 options: Docker Swarm, DCOS and Kubernetes. 


4) Containers as a Service | Serverless Containers

The last option is very attractive for some scenarios. You don't need to setup the nodes because you cannot manage or access. This option allows you to run your containers without the requirement to setup neyworking, storage, nodes, etc. 
Microsoft Azure offers 2 solutions:
- Azure Container Instance (ACI): you can run your containers on Azure and manage using Azure CLI and Docker
- Azure Container service (AKS) Managed Kubernetes: you can run your containers on Azure and manage using Azure CLI and Kubectl (with the most part of the benefits from Kubernetes)


## Option 1 | Start using AKS via Azure Portal ##

1) Open the Azure Portal using the browser. Open the Cloud Shell and select BASH



2) Create a Resource Group

```
az group create -l westus2 -n HaraRG10
``` 

This command will create a Resource Group with the name HararRG10 on WestUS2 (this region is currently supporting AKS)

3) Create the AKS Service

```
az aks create -g HaraRG10 -n hararg10 --generate-ssh-keys
```

You will create the AKS service using the Resource Group HararRG10 with the name hararg10

4) Copy the credentials

```
az aks get-credentials -g HaraRG10 -n hararg10
```

This step will copy the required credentials to manage correctly

5) Check the installation

```
kubectl cluster-info
```
```
kubectl get node
```

6) Create a deployment using NGINX to test

```
kubectl run my-nginx --image=nginx --replicas=2 --port=80
```

A deployment called my-nginx will be created using 2 replicas with port 80

7) Check the creation of the deployment

```
kubectl get pod
```
```
kubectl get deployment
```

8) expose the service to test the internet access

```
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

9) Check the external IP address to access via browser

```
kubectl get services
```

This could take some seconds to expose this IP Address

10) Open the browser and insert the external IP address. You will see something like this:


11) Scale the deployment to 10 pods

```
kubectl scale deployment my-nginx --replicas=10
```

12) Check the scale

```
kubectl get pods
```
```
kubectl get deployment
```

12) Scale the number of nodes to 10

```
az aks scale -g hararg11 -n hararg11 -c 10
```

13) Check the number of nodes

```
kubectl get node
```


## Option 2| Start using AKS via BASH on Windows 10 ##

Thgis procedure is recommended for anyone that want to use the Linux Subsystem on Windows 10. First you need to install Linux Subsystem and select the prefered Shell (Ubuntu, SLES or OpenSUSE)

Procedures here: https://docs.microsoft.com/en-us/windows/wsl/install-win10

1) Open the selected BASH (in this case using OpenSUSE) 


2) Create a Resource Group

```
az group create -l westus2 -n HaraRG10
``` 

This command will create a Resource Group with the name HararRG10 on WestUS2 (this region is currently supporting AKS)

3) Create the AKS Service

```
az aks create -g HaraRG10 -n hararg10 --generate-ssh-keys
```

You will create the AKS service using the Resource Group HararRG10 with the name hararg10

4) Install the KubeCTL command to manage Kubernetes. You have 2 options here:

- using Azure CLI

```
az acs kubernetes install-cli
```

- using Google SDK


4a) Download de Google SDK

```
curl https://sdk.cloud.google.com | bash
```

4b) Restart the shell

```
exec -l $SHELL
```


4c) Initialize

```
gcloud init
```

4d) After that you can install KUBECTL

```
gcloud components install kubectl
```


4) Copy the credentials

```
az aks get-credentials -g HaraRG10 -n hararg10
```

This step will copy the required credentials to manage correctly


5) Check the installation

```
kubectl cluster-info
```
```
kubectl get node
```

6) Create a deployment using NGINX to test

```
kubectl run my-nginx --image=nginx --replicas=2 --port=80
```

A deployment called my-nginx will be created using 2 replicas with port 80

7) Check the creation of the deployment

```
kubectl get pod
```
```
kubectl get deployment
```

8) expose the service to test the internet access

```
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

9) Check the external IP address to access via browser

```
kubectl get services
```

This could take some seconds to expose this IP Address

10) Open the browser and insert the external IP address. You will see something like this:


11) Scale the deployment to 10 pods

```
kubectl scale deployment my-nginx --replicas=10
```

12) Check the scale

```
kubectl get pods
```
```
kubectl get deployment
```

12) Scale the number of nodes to 10

```
az aks scale -g hararg11 -n hararg11 -c 10
```

13) Check the number of nodes

```
kubectl get node
```

More information about this resource is here:

--- 

https://azure.microsoft.com/en-us/blog/introducing-azure-container-service-aks-managed-kubernetes-and-azure-container-registry-geo-replication/?cdn=disable

--- 
## Manage AKS using Weave

You can use [Weave](https://www.weave.works/) to display information about Pods, Deployments, Nodes, etc. 

1) Execute de following command to deploy Weave
```
kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

2) Execute the command to forward Port 4040
```
kubectl port-forward -n weave "$(kubectl get -n weave pod --selector=weave-scope-component=app -o jsonpath='{.items..metadata.name}')" 4040
```

3) Open browser using the following URL
```
http://127.0.0.1:4040
```

![image1](/img/weave.PNG)



