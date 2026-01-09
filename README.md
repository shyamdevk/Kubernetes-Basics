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

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/archi.gif)

Kubernetes has **two main components**:

1. **Control Plane (Master Node)** – makes decisions
2. **Worker Nodes** – run applications

---

## 1️⃣ Control Plane (Master Node)

The **Control Plane** is the **brain of the Kubernetes cluster**.
It does **NOT run your application containers**.
Instead, it **decides what should happen and when**.

### 🔹 API Server

* Acts as the **front door** of Kubernetes
* All commands go through it (`kubectl`, UI, CI/CD)
* Validates and processes requests

📌 Example:
When you run:

```bash
kubectl apply -f deployment.yaml
```

➡ API Server receives and understands this request.

---

### 🔹 Scheduler

* Decides **which worker node** should run a pod
* Checks:

  * CPU availability
  * Memory availability
  * Node health

📌 Example:
If Node-1 is busy and Node-2 is free,
➡ Scheduler places the pod on **Node-2**.

---

### 🔹 Controller Manager

* Ensures **desired state = actual state**
* Watches the cluster continuously
* Handles:

  * Pod failures
  * Node failures
  * Replica count

📌 Example:
If you want **3 pods** but **1 crashes**,
➡ Controller Manager creates a **new pod automatically**.

---

### 🔹 etcd

* A **key-value database**
* Stores:

  * Cluster configuration
  * Pod states
  * Secrets & metadata

📌 Important:

* If `etcd` is lost → **cluster state is lost**
* That’s why backups are critical

---

## 2️⃣ Worker Nodes

**Worker Nodes** are where your **applications actually run**.

Each worker node contains the following components:

---

### 🔹 Kubelet
* Act as Agent 
* Agent running on every worker node
* Talks to the **Control Plane**
* Makes sure containers are running as expected

📌 Example:
If a pod crashes, kubelet reports it to the control plane.

---

### 🔹 Container Runtime/Engine

* Works with Kubelet
* Pulling Images
* Runs & Stops the containers and Exposes the Containers
* Examples:

  * Docker
  * containerd
  * CRI-O

📌 Simple meaning:
This is the engine that **actually starts containers**.

---

### 🔹 Kube-proxy

* Handles **networking**
* Assigns IP to Each Ports Dynamically
* Runs on Each Node and this make sure that each Pod will get it's Own Unique IP Address
* Enables communication between:

  * Pods
  * Services
  * External traffic

📌 Example:
When traffic comes to a Service,
➡ kube-proxy forwards it to the correct pod.

---

## Core Kubernetes Concepts

---

## 🔹 Pod

* **Smallest deployable unit in kubernetes**
* Wraps **one or more containers** like a Group
* Control unit is Pod not Containers
* Cannot Start a Container Without a Pod
* Containers inside a pod:

  * Share IP address
  * Share storage
  * Run together

📌 Example:

```text
Pod
 ├── Container 1 (App)
 └── Container 2 (Logger)
```

👉 **Pod = wrapper around containers**

---

## 🔹 Node

* A **physical or virtual machine**
* Runs pods
* Can be:

  * Worker node
  * Control plane node

📌 Example:

* AWS EC2
* Local VM
* Bare metal server

---

## 🔹 Cluster

* A **group of nodes**
* Managed by Kubernetes
* Contains:

  * 1 Control Plane
  * Multiple Worker Nodes

📌 Example:

```text
Cluster
 ├── Master Node
 ├── Worker Node 1
 └── Worker Node 2
```

---

## 🔹 Deployment

A **Deployment** manages pods automatically.

### What Deployment does:

* Creates pods
* Maintains required number of pods
* Replaces failed pods
* Performs rolling updates

📌 Example:

```yaml
replicas: 3
```

➡ Kubernetes ensures **3 pods are always running**

---

### 🔁 Self-Healing

* Pod crashes → new pod created
* Node fails → pods moved to other nodes

---

### 📈 Scaling

* Increase or decrease pods easily

```bash
kubectl scale deployment app --replicas=5
```

---

### 🔄 Rolling Updates

* Update app version **without downtime**
* Old pods replaced gradually with new ones

---

## 🧠 Simple Real-Life Analogy

| Kubernetes    | Real Life Example |
| ------------- | ----------------- |
| Cluster       | Company           |
| Control Plane | Manager           |
| Worker Node   | Employees         |
| Pod           | Task              |
| Deployment    | Work plan         |

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

# Kubernetes Workflow

## Workflow Steps

1. **Write a YAML file**  
   - Defines the application (image, pods, ports)

2. **Run `kubectl apply -f file.yaml`**  
   - Sends the YAML configuration to Kubernetes

3. **API Server receives request**  
   - Validates and stores the configuration

4. **Scheduler assigns Pod to a Node**  
   - Chooses the best node to run the Pod

5. **Kubelet runs the container**  
   - Starts the container on the selected node

6. **Service exposes the app**  
   - Makes the application accessible

## Flow Summary

YAML → kubectl → API Server → Scheduler → Kubelet → Service → App

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


