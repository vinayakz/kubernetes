## Understand Kubernetes

Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF). 

### How Kubernetes Works 

Kubernetes helps manage applications that are packaged into containers (e.g., using Docker) and deployed across a cluster of machines. Here’s how it works: 

- Cluster: A group of machines (nodes) where Kubernetes manages the deployment of applications. 
- Node: A single machine in a Kubernetes cluster that runs applications. 
    - Master Node (Control Plane): Manages the cluster, schedules workloads, and maintains the desired state. 
    - Worker Nodes: Run containerized applications. 
- Pods: The smallest deployable u nit in Kubernetes, which encapsulates one or more containers. 
- Deployments: Define how applications should be deployed and updated. 
- Services: Provide networking and load balancing to allow different parts of an application to communicate. 
- ConfigMaps & Secrets: Store configuration and sensitive information separately from application code.


Groups of node server or node runs inside docker containers

### Understanding Kubernetes Nodes and Docker Containers 
Kubernetes runs containerized applications inside nodes, and each node can run multiple containers. Here's how it works: 

1. Groups of Nodes (Kubernetes Cluster) 
    - A Kubernetes cluster is a group of nodes (servers) that run containerized applications. 
    - Nodes are categorized into: 
        - Master Node (Control Plane) – Manages the cluster. 
        - Worker Nodes – Run applications inside Pods.
2. Nodes and Docker Containers 
    - Each worker node runs Docker (or another container runtime like containerd or CRI-O). 
    - Inside a node, Kubernetes does NOT directly run containers—instead, it runs Pods. 
    - A Pod is the smallest unit in Kubernetes and contains one or more containers. 
    - Kubernetes manages these Pods, not individual containers. 
3. Does a Node Run Inside a Docker Container? 
    - No, a Node is a physical or virtual machine, not a container. 
    - However, the containers inside Pods run on the Node using Docker (or another container runtime). 


IMG link


## Worker Node and Deployment 
img link 

This architecture diagram represents a Kubernetes cluster with multiple namespaces running on a worker node. Below is a breakdown of its key components:

1. Worker Node
    - The entire structure runs on a single worker node.
    - It contains two separate namespaces to isolate workloads.
2. Namespaces
    Namespaces in Kubernetes help isolate resources within a cluster. In this diagram:
    - Namespace: Python runs Python-based applications.
    - Namespace: Java runs Java-based applications.

Each namespace has its own deployments, services, and pods.

3. Deployments and Pods
    - Deployment 1 (Python Namespace)
        - Manages Pod 1 and Pod 2.
        - Both pods run a containerized application (represented by Docker icons).
        - The application listens on port 8000.
    - Deployment 2 (Java Namespace)
        - Manages Pod 3 and Pod 4.
        - Both pods run Java-based containerized applications.
        - These are also isolated from the Python namespace.

4. Kubernetes Service (NodePort)
    - A Service exposes the application inside the cluster.
    - In the Python namespace, a NodePort service is used to expose the application.
        - Internal Port: 8000 (used by pods).
        - External Port: 30000 (accessible outside the cluster).
    - This allows users to access the Python application externally via NODE_IP:30000.

5. Isolation Between Namespaces
    - The Python and Java namespaces are isolated from each other.
    - Traffic from one namespace cannot interfere with the other.
    - This is useful for running different applications or teams within a shared Kubernetes cluster.

## Manifest files 
Understanding Kubernetes Concepts: Namespace, Pod, Deployment, and Service

### Pod.yml 
A Pod is the smallest deployable unit in Kubernetes. It runs one or more containers.

```bash
    apiversion: v1 
    kind: Pod 
    metadata: 
        name : nginx-pod 
    spec: 
        containers: 
            - name: nginx 
            image: nginx:latest 
            ports: 
            - containerPort: 80
```
Apply it:

```bash
kubectl apply -f pod.yml
kubectl get pods
```

### namespace.yml 
A Namespace is a logical isolation within a Kubernetes cluster. It allows you to create multiple environments (e.g., dev, staging, prod) within the same cluster.

```bash
apiverison: v1 
kind: Namespace 
metadata: 
  name: nginx 
  labels: nginx 
```
Apply it:

```bash
kubectl apply -f namespace.yaml
```

### deployment.yaml
A Deployment manages a set of replica Pods and ensures they are always running. It helps with scaling and rolling updates.

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
  labels:
    app: nginx
spec:  
  replicas: 3
  selector:
    matchLabels:  
      app: nginx
  template:
    metadata:
      labels:  
        app: nginx
    spec:  
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```
Apply it:

```bash
kubectl apply -f deployment.yaml
```

### service.yaml
A Service exposes a group of Pods to the network (internally or externally). It enables load balancing and stable access to applications.

```bash
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: my-namespace
  labels:  
    app: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80   # Exposed Service port
      targetPort: 80  # Container Port
  type: LoadBalancer  # Can be ClusterIP, NodePort, or LoadBalancer

```

## Why Not Run Pods Directly? 
If you create a Pod manually, it will run successfully, but if the Pod crashes or the Node fails, Kubernetes will NOT automatically restart it. 

### Deployment vs. Pod ("Process-Oriented Delivery")
