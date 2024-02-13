# k8s-proj Deployment Guide

This guide provides instructions on how to deploy the Kaufland Game application on Kubernetes using Minikube. The application is containerized and available on Docker Hub as `spearx/kaufland-game:main`.

## Prerequisites

- **Minikube**: Ensure Minikube is installed on your system. Minikube allows you to run Kubernetes locally.
- **kubectl**: The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters.

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
