

![Argo]ArgoCD\argo_setup.webp
# Introduction
 Argocd is a declarative,GitOps continuous deployment tool that you can use to deploy and managed applications on a Kubernetes cluster.
 In this article, we will install ArgoCD on Kubernetes cluster and deploy an application from a GitHub repository using ArgoCD.

# Prerequisites
. A running Kubernetes cluster
. kubectl CLI tool installed and configured to access your cluster
. A Git repository that contains your deploment yaml code in this case I'm using github.
. A container registry such as Docker Hub or Google Container Registry (GCR) to store your container images.

# install ArgoCD in k8s

To install ArgoCD using kubectl, run the following command:

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
This will create a new namespace called argocd and install the latest version of ArgoCD on your cluster.

#  Access the ArgoCD UI

 By default, the Argo CD UI is not exposed externally. To access the UI, you can create a Kubernetes service of type LoadBalancer or NodePort, or you can use port forwarding to access it locally from your cluster node

```
kubectl get svc -n argocd
kubectl apply -n argocd -f argocd-ui-service.yaml
```

This will create a NodePort service named argocd-server in the argocd namespace. The service will be exposed on a high port number (e.g., 30007), which can be accessed on any Kubernetes node in your cluster.

To access the Argo CD UI, you need to determine the IP address of any of your Kubernetes nodes. You can use the following command to find the IP address:
 ```
 kubectl get nodes -o wide
 ```
Look for the External-IP or Internal-IP column to find the IP address of a node.
Once you have the IP address and the NodePort value (e.g., 30007), you can access the Argo CD UI in a web browser using the following URL:

```
http://<node-ip>:<node-port>
```
Replace <node-ip> with the IP address of the Kubernetes node, and <node-port> with the NodePort value you specified in the service configuration.

# login with admin user and below token (as in documentation):
The default username and password for ArgoCD is admin and password. To login, navigate to the ArgoCD UI and enter these credentials.
or you could pull the secret to get the password as outlined below.

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo
```



#  Create a new Application
Once you are logged in, you can create a new application in ArgoCD. Click on the + New App button on the top right corner of the UI. This will open a form where you can specify the details of your application or you rollout the application.yaml to your cluster to create a new application.

In the General tab, specify the following details:

- Application name: a unique name for your application
- Project: the project to which this application belongs
- Sync Policy: how often to sync the application state with the desired state

In the Source tab, specify the following details:

- Repository URL: the URL of your Git repository
- Revision: the branch or tag to use for this application
- Path: the directory within the repository containing your Kubernetes manifests
  Helm Chart: if your application is using Helm charts, specify the chart name and version here

In the Destination tab, specify the following details:

- Namespace: the Kubernetes namespace to deploy your application to
- Server: the Kubernetes cluster to deploy your application to

# Sync the Application

  Once you have created your application, ArgoCD will automatically start syncing it with the desired state. You can view the status of your application by clicking on its name in the UI. If there are any errors or warnings, ArgoCD will display them in the UI.

# Roll out a New Version

  To roll out a new version of your application, simply push your changes to your Git repository and ArgoCD will automatically detect the changes and sync your application with the new state.
