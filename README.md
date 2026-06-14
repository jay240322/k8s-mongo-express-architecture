# Multi-Tier Kubernetes Database Deployment (MongoDB & Mongo Express)

A secure, scalable, and self-healing multi-tier database infrastructure built inside a local Kubernetes cluster using **Minikube** and **Docker**. 

This project demonstrates core DevOps principles: Infrastructure as Code (IaC), tight network isolation, and abstract service routing to handle high-availability applications.

---

## 🏗️ Cluster Architecture & Request Lifecycle

When a user interacts with the administration panel, the network request travels through the following secure layers:



1. **Client Browser (`http://127.0.0.1:8081`)** ➔ The user accesses the dashboard interface.
2. **External Service (`mongo-express-service`)** ➔ A `LoadBalancer` service acts as the cluster's front gate, receiving public traffic and bridging it inside the container network.
3. **Mongo Express Pod** ➔ The Node.js web application processes the UI actions.
4. **Internal Service (`mongo-service`)** ➔ A secure `ClusterIP` service acting as an internal switchboard. The frontend pods communicate strictly with this abstraction layer rather than tracking volatile Pod IPs.
5. **MongoDB Pod** ➔ The database backend, completely isolated from the public internet for maximum security, reading/writing persistent data.

---

## 🛠️ Tech Stack & Tools
* **Orchestration:** Kubernetes (K8s), Minikube
* **Container Driver:** Docker on Windows (WSL2 backend)
* **Database:** MongoDB
* **Database GUI:** Mongo Express (Node.js)

---

## 🚀 How to Deploy Locally

### 1. Prerequisites
Ensure you have Minikube and `kubectl` installed on your local machine.

### 2. Start the Cluster
```bash
minikube start
```

### 3. Apply Secrets & Cluster Configurations
Create your local credentials file from the template and apply the manifest definitions:
# Note: Ensure you configure your base64 secrets locally!
kubectl apply -f secret.yaml
kubectl apply -f mongo-config.yaml
kubectl apply -f mongo.yaml
kubectl apply -f mongo-express.yaml

### 4. Bypassing Windows/Docker Networking Quirks
Because the Docker driver isolates the container network on Windows, standard NodePorts/LoadBalancers won't expose automatically to your local browser. Use explicit Kubernetes port-forwarding to map the pod directly:
# 1. Get your active Mongo Express pod name
kubectl get pods

# 2. Forward local port 8081 to the container
kubectl port-forward pod/<YOUR-MONGO-EXPRESS-POD-NAME> 8081:8081

Now, safely open your browser and navigate to:``` http://127.0.0.1:8081```