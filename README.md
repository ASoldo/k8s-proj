# k8s-proj Deployment Guide

This guide provides instructions on how to deploy the Kaufland Game application on Kubernetes using Minikube. The application is containerized and available on Docker Hub as `spearx/kaufland-game:main`.

## Prerequisites

- **Minikube**: Ensure Minikube is installed on your system. Minikube allows you to run Kubernetes locally.
- **kubectl**: The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters.
- **k9s**: An optional terminal-based UI to interact with your Kubernetes clusters, providing a more intuitive way to monitor and manage your deployments.
- **lazydocker**: A simple terminal UI for both docker and docker-compose, aimed at making managing containers easier.

## Getting Started

### Step 1: Start Minikube

First, start your Minikube cluster:

```bash
minikube start
```

This command starts a local Kubernetes cluster. Minikube will be the environment where your application is deployed.

### Step 2: Deploy the Application

Deploy the kaufland-game application using the provided YAML file:

```bash
kubectl apply -f kaufland-game.yaml
```

This command instructs Kubernetes to create the deployment and service as defined in kaufland-game.yaml. The deployment ensures your application is running, and the service exposes it on a specific port.

### Step 3: Access the Application

To access the Kaufland Game application, obtain the URL provided by Minikube:

```bash
minikube service kaufland-service --url
```

This command returns a URL that you can use in your web browser to access the application. The service is exposed on node port 30100, as specified in the YAML file.

### Step 4: Inspect the Application

**Using k9s**
For a detailed inspection and management of your application, `k9s` is a powerful tool. It provides an interactive terminal UI to interact with your Kubernetes cluster, allowing you to:

Monitor live logs from your containers.
SSH into containers for direct command execution.
View and manage Kubernetes resources (deployments, pods, services, etc.).
To use `k9s`, simply run:

```bash
k9s
```

Navigate through the UI using your keyboard to explore the different views and manage your Kubernetes resources.

**Using lazydocker**
`lazydocker` is a simple terminal UI for managing your docker and docker-compose environments. It's especially useful for inspecting the Minikube Docker environment, where you can:

Quickly view logs from your containers.
Manage container lifecycle (start, stop, restart).
Explore container and volume details.
To start `lazydocker`, run:

```bash
lazydocker
```

This command will launch the UI, where you can navigate using your keyboard to inspect and manage your Docker containers.

### Understanding the YAML Configuration

The `kaufland-game.yaml` file contains two main parts:

Deployment: Defines the desired state of your application. It specifies the Docker image to use (spearx/kaufland-game:main) and the port the application listens on (3000).

Service: Exposes the application to the network. In this case, a NodePort service is used to make the application accessible on a specific port of the Minikube node.

### Cleaning Up

To remove the deployed resources from your cluster, run:

```bash
kubectl delete -f kaufland-game.yaml
```

And to stop Minikube:

```bash
minikube stop
```
