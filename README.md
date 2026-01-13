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

# 🚀 Lab: Configure Kubernetes Architecture on AWS (Amazon Linux)

## 📌 Lab Objective

In this lab, you will learn how to **set up a basic Kubernetes cluster on AWS EC2** using **Amazon Linux**, consisting of:

* 🧠 **1 Master Node**
* ⚙️ **2 Worker Nodes**

### Components Overview

| Component        | Purpose                     |
| ---------------- | --------------------------- |
| **Master Node**  | Controls the cluster        |
| **Worker Nodes** | Run application pods        |
| **Docker**       | Container runtime           |
| **kubeadm**      | Cluster initialization tool |
| **kubelet**      | Runs on each node           |
| **kubectl**      | Command-line tool           |

---

## ☁️ Step 1: Create EC2 Instances on AWS

### 🔹 Instance Configuration (Same for All 3)

| Setting        | Value                           |
| -------------- | ------------------------------- |
| OS             | Amazon Linux 2                  |
| Instance Type  | `t2.medium` (Recommended)       |
| Storage        | 20 GB                           |
| Security Group | **Allow All Traffic (for lab)** |
| Key Pair       | Your existing key               |

> 💡 **Why `t2.medium`?**
> Kubernetes needs more memory than `t2.micro` to run smoothly.

---

### 🔹 Create These Instances

| Node Type | Instance Name  |
| --------- | -------------- |
| Master    | `k8s-master`   |
| Worker 1  | `k8s-worker-1` |
| Worker 2  | `k8s-worker-2` |

---

## 🔐 Step 2: Connect to EC2 Instances

```bash
ssh -i key.pem ec2-user@<PUBLIC-IP>
```

Run this **on all 3 instances**.

---

## 🐳 Step 3: Install Docker (All Nodes)

### 1️⃣ Update System

```bash
sudo yum update -y
```

### 2️⃣ Install Docker

```bash
sudo yum install docker -y
```

### 3️⃣ Start & Enable Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 4️⃣ Add User to Docker Group

```bash
sudo usermod -aG docker ec2-user
newgrp docker
```

### 5️⃣ Verify Docker

```bash
docker --version
```

✅ Run these steps on **Master & Worker nodes**

---

## ☸️ Step 4: Install Kubernetes Components (All Nodes)

### 1️⃣ Disable Swap (Mandatory for Kubernetes)

```bash
sudo swapoff -a
```

---

### 2️⃣ Configure Kubernetes Repository

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
EOF
```

---

### 3️⃣ Install Kubernetes Packages

```bash
sudo yum install -y kubelet kubeadm kubectl
```

# If Above Not Worked, Try This (Optional)

This guide helps you fix the **Kubernetes installation error** on **Amazon Linux / RHEL-based EC2 instances** when the command below fails:

```bash
sudo yum install -y kubelet kubeadm kubectl
````

### ❌ Common Error Faced

```text
Error: Failed to download metadata for repo 'kubernetes'
Status code: 404
No match for argument: kubelet kubeadm kubectl
```

---

## 📌 Why This Error Happens

* The old Kubernetes yum repository
  `https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/`
  is **deprecated**
* Kubernetes packages are no longer available from that source
* So `yum` cannot find:

  * `kubelet`
  * `kubeadm`
  * `kubectl`

---

## ✅ Solution: Use the New Official Kubernetes Repository

Kubernetes now uses the **pkgs.k8s.io** repository.

Follow the steps **exactly in order**.

---

## 🧹 Step 1: Remove Old Kubernetes Repo (If Exists)

```bash
sudo rm -f /etc/yum.repos.d/kubernetes.repo
```

---

## ➕ Step 2: Add New Kubernetes Repository

```bash
sudo tee /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF
```

✔️ This is the **official and maintained** Kubernetes repository.

---

## 🔄 Step 3: Clean Yum Cache

```bash
sudo yum clean all
sudo yum makecache
```

---

## 📦 Step 4: Install Kubernetes Components

```bash
sudo yum install -y kubelet kubeadm kubectl
```

✔️ This should now install successfully.

---

## ▶️ Step 5: Enable and Start Kubelet

```bash
sudo systemctl enable kubelet
sudo systemctl start kubelet
```

⚠️ Note:
`kubelet` may show as **inactive** until the cluster is initialized — this is normal.

---

## 🔍 Step 6: Verify Installation

```bash
kubeadm version
kubectl version --client
kubelet --version
```

Expected output should show version like:

```text
v1.29.x
```

---

## 🧠 Important Notes

* ❌ Do NOT use `packages.cloud.google.com` anymore
* ✅ Always use `pkgs.k8s.io`
* Works on:

  * Amazon Linux 2
  * RHEL 7 / 8 / 9
  * CentOS

---

## 🚀 Next Step (Master Node Only)

Initialize Kubernetes Master Node:

```bash
sudo kubeadm init
```

Worker nodes will later join using the token generated here.

---


---

### 4️⃣ Start & Enable kubelet

```bash
sudo systemctl start kubelet
sudo systemctl enable kubelet
```

---

### 5️⃣ Verify Installation

```bash
kubeadm version
kubectl version --client
```

✅ Run on **all 3 nodes**

---

## 🧠 Step 5: Initialize Kubernetes Master Node

⚠️ **Run ONLY on Master Node**

```bash
sudo kubeadm init
```

📌 After success, you’ll see a **kubeadm join command** — **save it**.

---

### Configure kubectl Access

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Verify Master Node

```bash
kubectl get nodes
```

---


## 👑 STEP 6: INITIALIZE CONTROL PLANE (MASTER ONLY)

Run **only on the master node**:

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

📌 **Important**

* Save the `kubeadm join` command shown at the end
* Calico requires `192.168.0.0/16`

---

## 🔑 STEP 6: CONFIGURE kubectl (MASTER)

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Verify:

```bash
kubectl get nodes
```

---

## 🌐 STEP 7: INSTALL CALICO (MASTER ONLY)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

Wait 1–2 minutes, then check:

```bash
kubectl get pods -n kube-system
```

You should see `calico-node` in **Running** state.

---

## 🔗 STEP 8: JOIN WORKER NODES

On **each worker node**, run the join command copied from the master:

```bash
sudo kubeadm join <MASTER-PRIVATE-IP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

✅ Successful join message:

```
This node has joined the cluster
```

---

## ✅ STEP 9: VERIFY CLUSTER (MASTER)

```bash
kubectl get nodes
```

Expected output:

```
control-plane   Ready
worker-1        Ready
worker-2        Ready
```

---

## 🧪 STEP 10: TEST THE CLUSTER (OPTIONAL)

Deploy a test application:

```bash
kubectl create deployment nginx --image=nginx
kubectl get pods -o wide
```

Pods should run on worker nodes.

---

## ❗ COMMON ISSUES & FIXES

### Worker not joining

* Ensure **same Security Group**
* Test connectivity:

```bash
curl -k https://MASTER-IP:6443
```

Expected output: `Unauthorized`

### Node shows NotReady

* Wait 1–2 minutes for Calico
* Check:

```bash
kubectl get pods -n kube-system
```
# 🧪 Lab: Install and Run Minikube on Ubuntu (EC2)

## 🎯 Lab Objective
This lab helps beginners understand how to:
- Launch an Ubuntu EC2 instance
- Update the system
- Install Docker
- Install kubectl
- Install and start Minikube
- Fix common Minikube & kubectl errors
- Verify a working Kubernetes cluster

---

## 🛠 Prerequisites
- AWS Account
- Ubuntu Server 22.04 (EC2)
- Internet access
- Basic Linux commands

---

## 🧱 Step 1: Launch Ubuntu EC2 Instance

1. Go to AWS Console → EC2 → Launch Instance
2. Select **Ubuntu Server 22.04 LTS**
3. Instance type: `t2.medium` (recommended)
4. Storage: Minimum 20 GB
5. Security Group:
   - Allow **SSH (22)** from your IP
6. Launch and download `.pem` key

### Connect to EC2
```bash
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
````

---

## 🔄 Step 2: Update Ubuntu System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🐳 Step 3: Install Docker (Required for Minikube)

### Install Docker

```bash
sudo apt install docker.io -y
```

### Start and Enable Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### Add User to Docker Group

```bash
sudo usermod -aG docker ubuntu
```

⚠️ Logout and login again:

```bash
exit
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
```

### Verify Docker

```bash
docker ps
```

---

## ☸️ Step 4: Install kubectl (Kubernetes CLI)

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```

```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

### Verify kubectl

```bash
kubectl version --client
```

---

## 🚀 Step 5: Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Verify Minikube

```bash
minikube version
```

---

## ▶️ Step 6: Start Minikube Cluster (IMPORTANT)

⚠️ **kubectl will NOT work until Minikube is started**

Start Minikube using Docker driver:

```bash
minikube start --driver=docker
```

⏳ First startup may take 3–5 minutes.

---

## ✅ Step 7: Verify Minikube Status

```bash
minikube status
```

Expected Output:

```
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
```

---

## ✅ Step 8: Verify Kubernetes Cluster

```bash
kubectl get nodes
```

Expected Output:

```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   1m    v1.xx.x
```

🎉 Kubernetes cluster is now running successfully!

---

## 🧪 Step 9 (Optional): Test with Sample Deployment

```bash
kubectl create deployment nginx --image=nginx
kubectl get pods
```

---

## 🛑 Common Error & Fix (IMPORTANT FOR BEGINNERS)

### ❌ Error:

```text
The connection to the server localhost:8080 was refused
```

### ✅ Cause:

* Minikube cluster is **not started**
* kubectl has no Kubernetes API server to connect to

### ✅ Fix:

```bash
minikube start --driver=docker
```

Then retry:

```bash
kubectl get nodes
```

---

## 🧹 Stop or Delete Minikube

### Stop Cluster

```bash
minikube stop
```

### Delete Cluster

```bash
minikube delete
```

---

## 📌 Key Learning Points

* Minikube **creates and runs** Kubernetes cluster
* kubectl **only works after** cluster is running
* Always start Minikube before using kubectl
* Docker is required for Minikube on EC2

---
