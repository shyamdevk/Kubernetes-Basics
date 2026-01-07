# ☸️ Kubernetes

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/kub.gif)

## What is Kubernetes?
**Kubernetes (K8s)** is an **open-source container orchestration platform** used to **deploy, manage, scale, and monitor containerized applications**.

👉 In simple words:  
**Kubernetes automatically manages Docker containers for you.**

---

## Why do we need Kubernetes?
Managing containers manually becomes difficult when applications grow.

Kubernetes helps by:
- Restarting failed containers automatically
- Scaling applications up or down
- Load balancing traffic
- Managing containers across multiple machines

---

## Kubernetes vs Docker (Simple)
- **Docker** → Builds and runs containers
- **Kubernetes** → Manages and orchestrates containers

👉 Docker creates containers  
👉 Kubernetes manages many containers together

---

## Kubernetes Architecture (High Level)

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/kub2.gif)

Kubernetes has **two main components**:

### 1️⃣ Control Plane (Master Node)
This is the **brain** of the cluster.

Main components:
- **API Server** – Entry point for kubectl and users
- **Scheduler** – Decides where pods should run
- **Controller Manager** – Keeps desired state
- **etcd** – Stores cluster configuration and data

---

### 2️⃣ Worker Nodes
These are the machines where applications actually run.

Each worker node contains:
- **Kubelet** – Communicates with control plane
- **Container Runtime** – Docker / containerd
- **Kube-proxy** – Handles networking

---

## Core Kubernetes Concepts

### 🔹 Pod
- Smallest unit in Kubernetes
- Contains one or more containers
- Shares network and storage

👉 Pod = wrapper around containers

---

### 🔹 Node
- A physical or virtual machine
- Runs pods

---

### 🔹 Cluster
- A group of nodes managed by Kubernetes

---

### 🔹 Deployment
- Manages pods automatically
- Handles:
  - Scaling
  - Updates
  - Self-healing

👉 Best practice: create **Deployments**, not Pods directly

---

### 🔹 Service
- Exposes applications to users
- Provides stable IP and DNS

Service types:
- **ClusterIP** – Internal access
- **NodePort** – Access via node IP
- **LoadBalancer** – Cloud load balancer

---

### 🔹 Namespace
- Logical separation inside a cluster
- Examples:
  - default
  - kube-system
  - dev
  - prod

---

## Kubernetes Workflow (Simple)
1. Write a **YAML file**
2. Run `kubectl apply -f file.yaml`
3. API Server receives request
4. Scheduler assigns Pod to a Node
5. Kubelet runs the container
6. App becomes accessible using a Service

---

## Advantages of Kubernetes
- Auto scaling
- Self-healing
- Load balancing
- High availability
- Cloud & on-prem support

---

## Where Kubernetes is Used?
- Cloud platforms (EKS, AKS, GKE)
- DevOps & CI/CD pipelines
- Microservices architecture
- Production environments

---


