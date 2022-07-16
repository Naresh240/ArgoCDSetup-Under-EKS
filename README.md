# Argo CD on Kubernetes

## Pre-Requisites:

```bash
1. EKS Cluster
2. Install ArgoCD
```

## SetUP EKS Cluster

[EKS Cluster Setup](https://github.com/Naresh240/kubernetes/blob/main/eks-cluster-setup/eks-cluster-with-eksctl/README.md)

## Install ArgoCD CLI tool

```bash
curl -sSL -o /usr/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v1.8.3/argocd-linux-amd64
chmod +x /usr/bin/argocd
argocd version
```

## Deploy Argo CD

1. Run the following command to create a namespace. Name it argocd.

```bash
kubectl create namespace argocd
```

2. Run the following command to deploy Argo CD.

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v1.8.3/manifests/install.yaml
```

```Note：```
The Argo CD community also provides an HA deployment method by running the command kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v1.8.3/manifests/ha/install.yaml. It’s for deployment in production.

3. Run the following command to set the Service type to NodePort.

```bash
kubectl patch svc argocd-server -p '{"spec": {"type": "LoadBalancer"}}' -n argocd
```

4. Get service using following command
  
```bash
kubectl get svc -n argocd
```

![image](https://user-images.githubusercontent.com/58024415/179356882-c6a6df53-4f79-4da5-8ba3-d2bc57cbe3f3.png)

5. Trigger LoadBalancer DNS in UI

![image](https://user-images.githubusercontent.com/58024415/179356965-d5741d7a-43e9-46e7-9079-64692d371f1b.png)

## Log in to Argo CD

1. Provide Username as ```admin``` Password as PodID

![image](https://user-images.githubusercontent.com/58024415/179357037-a32a3f7e-7227-45a5-863a-a826909e3830.png)

2. Reset Password in UI or else with following command

```bash
## Login with admin with default password
argocd login <Loadbalancer-of-argocd-service> --username admin --password <POD ID>

e.g:
  argocd login a973d9a400db742188192fcad673eaa0-1823655890.us-east-1.elb.amazonaws.com --username admin --password argocd-server-67f58479-j8wgp

## update admin password
argocd account update-password --account admin --current-password argocd-server-67f58479-j8wgp --new-password password
```

## Create an application

We surely can create an application through the Web UI, but let’s use the CLI tool here so that you can better understand Argo CD by using the commands below.

1. Run the following command to create an application

```bash
argocd app create nginx-application \
   --repo https://github.com/Naresh240/argocd-example-application.git \
   --path kubernetes \
   --dest-server https://kubernetes.default.svc \
   --dest-namespace default
```

2. After a while, you can view the service created shown on the Argo CD web page

![image](https://user-images.githubusercontent.com/58024415/179357612-a39ae720-b270-42aa-8b4d-bf5ba3e914ac.png)

```Note:``` It will be in out of sync for the first time, so we need to click on ```SYNC``` option

3. Click it to see its details. You can also click the icon in the upper right corner to view its topology diagram

![image](https://user-images.githubusercontent.com/58024415/179357674-829e99af-c96c-4b8a-9fe6-d56328705da4.png)

## CleanUp of Application

```bash
argocd app list
argocd app delete nginx-application
```

![image](https://user-images.githubusercontent.com/58024415/179357837-20237643-9e73-44ee-92e2-b63024795dbc.png)
