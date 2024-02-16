# k8s-proj Deployment Guide

This guide provides instructions on how to deploy the Kaufland Game application
on Kubernetes using Minikube. The application is containerized and available on
Docker Hub as `spearx/kaufland-game:main`.

## Prerequisites

- **minikube**: Ensure Minikube is installed on your system. Minikube allows
  you to run Kubernetes locally.
- **kubectl**: The Kubernetes command-line tool, kubectl, allows you to
  run commands against Kubernetes clusters.
- **k9s**: An optional terminal-based UI to interact with your
  Kubernetes clusters, providing a more intuitive way to monitor and manage your
  deployments.
- **lazydocker**: A simple terminal UI for both docker and docker-compose,
  aimed at making managing containers easier.
- **argocd cli**: A command-line tool that enables interaction with Argo CD for
  managing Kubernetes applications through GitOps. It allows you to execute Argo
  CD server operations from your terminal.
- **eksctl**: A CLI for Amazon EKS that simplifies the process of creating,
  managing, and deleting EKS clusters, streamlining the deployment of Kubernetes
  workloads.

## Getting Started

### Step 1: Start Minikube

First, start your Minikube cluster:

```bash
minikube start
```

This command starts a local Kubernetes cluster. Minikube will be the
environment where your application is deployed.

### Step 2: Deploy the Application

Deploy the kaufland-game application using the provided YAML file:

```bash
kubectl apply -f kaufland-game.yaml
```

This command instructs Kubernetes to create the deployment and service a
s defined in kaufland-game.yaml. The deployment ensures your application
is running, and the service exposes it on a specific port.

### Step 3: Access the Application

To access the Kaufland Game application, obtain the URL provided by Minikube:

```bash
minikube service kaufland-service --url
```

This command returns a URL that you can use in your web browser to access the
application. The service is exposed on node port 30100,
as specified in the YAML file.

### Step 4: Inspect the Application

**Using k9s**
For a detailed inspection and management of your application, `k9s`
is a powerful tool. It provides an interactive terminal UI to interact with
your Kubernetes cluster, allowing you to:

Monitor live logs from your containers.
SSH into containers for direct command execution.
View and manage Kubernetes resources (deployments, pods, services, etc.).
To use `k9s`, simply run:

```bash
k9s
```

Navigate through the UI using your keyboard to explore the different views and
manage your Kubernetes resources.

**Using lazydocker**
`lazydocker` is a simple terminal UI for managing your docker and docker-compose
environments. It's especially useful for inspecting the Minikube Docker
environment, where you can:

Quickly view logs from your containers.
Manage container lifecycle (start, stop, restart).
Explore container and volume details.
To start `lazydocker`, run:

```bash
lazydocker
```

This command will launch the UI, where you can navigate using your keyboard to
inspect and manage your Docker containers.

### Step 5: Using the Minikube Dashboard

The Minikube dashboard is a web-based Kubernetes user interface that allows you
to manage and monitor your Kubernetes cluster and applications running on it.
It provides a comprehensive overview of the various resources within your
cluster, making it easier to understand and manage your deployments
, pods, services, and more.

#### Accessing the Dashboard

To access the Minikube dashboard, run the following command:

```bash
minikube dashboard
```

This command will automatically open your default web browser and navigate to
the dashboard's URL. If it does not open automatically,
the command will print the URL, which you can manually enter in your browser.

Within the Minikube dashboard, you will find:

**Overview of Cluster and Workloads**: Get a high-level overview of the health
and status of your Kubernetes cluster, including the number of nodes,
pods, deployments, and services.

**Resource Management**: View, create, modify, and delete Kubernetes resources
such as deployments, services, pods, etc. This is useful for managing
your application's lifecycle directly from the dashboard.

**Logs and Metrics**: Access logs for individual pods, helping you troubleshoot
issues or monitor the behavior of your applications. You can also view
basic metrics like CPU and memory usage for your cluster and workloads.

**Namespace Management**: Switch between and manage resources in
different Kubernetes namespaces, making it easier to organize and control
access to parts of your cluster.

**Search and Filter**: Easily search for specific resources and filter
them based on various criteria, simplifying the process of
finding the information you need.

The Minikube dashboard provides a user-friendly interface to interact with your
Kubernetes cluster, offering a convenient way to deploy, manage, and
troubleshoot applications without needing to use command-line tools.
Whether you're new to Kubernetes or an experienced user, the dashboard
can significantly enhance your workflow and productivity.

### Understanding the YAML Configuration

The `kaufland-game.yaml` file contains two main parts:

Deployment: Defines the desired state of your application. It specifies the
Docker image to use (spearx/kaufland-game:main) and the port the
application listens on (3000).

Service: Exposes the application to the network. In this case, a NodePort
service is used to make the application accessible on a specific port of
the Minikube node.

### Cleaning Up

To remove the deployed resources from your cluster, run:

```bash
kubectl delete -f kaufland-game.yaml
```

And to stop Minikube:

```bash
minikube stop
```

## GitOps & ArgoCD Setup Instructions

This guide walks you through the installation of ArgoCD, a declarative, GitOps
continuous delivery tool for Kubernetes, and how to access its dashboard.

### Install ArgoCD

First, create a dedicated namespace for ArgoCD in your Kubernetes cluster and
install ArgoCD with the following commands:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Verify ArgoCD Pods

After installing ArgoCD, check that all the ArgoCD pods are up and running:

```bash
kubectl get pod -n argocd -w
```

### Access the ArgoCD Dashboard

To access the ArgoCD dashboard, you need to connect to the argocd-server service.
First, identify the service:

```bash
kubectl get svc -n argocd
```

Next, use port forwarding to expose the argocd-server service locally:

```bash
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

You can now access the ArgoCD dashboard by navigating to <http://localhost:8080>
in your web browser.

From there you can just copy and paste the url in the browser.

### Login to the Dashboard

The default username for the ArgoCD dashboard is `admin`. To obtain the initial
admin password, first retrieve the encrypted password from the Kubernetes secret:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

This command outputs the encrypted password. Copy the password hash and decode it:

```bash
echo password_from_previous_command | base64 --decode
```

With the decoded password, you can now login to the ArgoCD dashboard as the
admin user.

### Apply application to the cluster

In order for ArgoCD to know about our app we need to apply `application.yaml`:

```bash
kubectl apply -f application.yaml
```

Now we can see in ArgoCD Dashboard status of our app and its progress.

### Access the application with Minikube

In order to get url to preview app in the browser first we need to run this:

```bash
minikube service kaufland-service -n argo-app --url
```

Now just copy and paste console output to your browser.

### Create EKS cluster with `eksctl`

Use t2.medium - 2 core, 4 gb min req in Kubernetes environment.
This will create all resources in AWS (VPC, EC2, EKS)

```bash
eksctl create cluster -n cluster-soldo --nodegroup-name node-group-name-soldo
--region eu-central-1 --node-type t2.medium --nodes 2
--version 1.28 --profile k8s-user
```

Use this to delete all resources in AWS config.

```bash
eksctl delete cluster -n cluster-soldo
```

Use these to switch between `Minikube` and `EKS` with aws credentials

```bash
kubectl config get-contexts
kubectl config current-context
kubectl config use-context minikube
```

### Add EKS cluster to ArgoCD

Resource: YT - [Add external cluster to ArgoCD](https://www.youtube.com/watch?v=El7zRS5w0dc)

To manage multiple clusters we can add our EKS cluster to ArgoCD

Login to ArgoCD with Cli:

```bash
argocd login localhost:8080 --username admin --insecure
```

```bash
argocd cluster add k8s-user@cluster-soldo.eu-central-1.eksctl.io --name k8s-cluster
```

Then we can list clusters:

```bash
argocd cluster list
```

### Resources

- `TechWorld with Nana` [ArgoCD Setup](https://www.youtube.com/watch?v=MeU5_k9ssrs)
