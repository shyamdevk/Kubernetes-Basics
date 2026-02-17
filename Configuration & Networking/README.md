# 🚀 Configuration & Networking

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/network.gif)

> This document covers the most important configuration and networking concepts in Kubernetes.
> These topics are very important for DevOps engineers and interviews.

---

# 📌 1️⃣ Kubernetes Services

## 🔎 What is a Service?

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/service.gif)

A **Service** is used to expose Pods so they can communicate with:
- Other Pods
- External users
- Other applications

Pods are temporary and their IP changes.
👉 Service provides a **stable IP and DNS name**.

---

## 🎯 Types of Services

### 1️⃣ ClusterIP (Default)
- Accessible **inside the cluster only**
- Used for internal communication

✅ Example:
Frontend → Backend communication

---

### 2️⃣ NodePort
- Exposes service on a specific port on each node
- Accessible using:  
  `NodeIP:NodePort`

⚠️ Mainly used for testing

---

### 3️⃣ LoadBalancer
- Creates an external load balancer (Cloud environments)
- Used in AWS, Azure, GCP

✅ Used in production for public apps

---

### 4️⃣ Headless Service
- No cluster IP
- Used for Stateful applications
- Direct pod-to-pod communication

---

## 📌 Important Concepts

- **Selector** → Connects Service to Pods
- **Port** → Service port
- **TargetPort** → Container port
- **NodePort** → External port

---

# 📌 2️⃣ Ingress

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/ingress.gif)

## 🔎 What is Ingress?

Ingress is used to manage **external HTTP/HTTPS access** to services.

Instead of exposing multiple NodePorts,
👉 Ingress allows routing using:

- Domain names
- Paths
- TLS (HTTPS)

---

## 🎯 Why Use Ingress?

Without Ingress:
- Each service needs separate LoadBalancer

With Ingress:
- One entry point
- Smart routing
- Cost efficient

---

## 📌 Ingress Controller

Ingress needs a controller to work.

Most common:
- NGINX Ingress Controller

It:
- Reads Ingress rules
- Routes traffic properly

---

## 📌 Types of Routing

### 🔹 Path-based Routing
Example:
- `/api` → Backend service
- `/` → Frontend service

---

### 🔹 Host-based Routing
Example:
- `app.example.com` → App service
- `admin.example.com` → Admin service

---

## 🔐 TLS in Ingress

- Used for HTTPS
- Requires TLS secret
- Secures communication

---

# 📌 3️⃣ ConfigMaps & Secrets

## 🔎 Why Needed?

Applications need:
- Environment variables
- Config files
- API keys
- Passwords

We should NOT hardcode them inside container images.

---

# 📘 ConfigMap

Used for:
- Non-sensitive configuration
- App settings
- Environment variables

Example:
- Database host
- App mode (dev/prod)

---

## 📌 How ConfigMap Can Be Used?

- As Environment Variable
- As File inside container
- As Command-line argument

---

# 🔐 Secret

Used for:
- Sensitive data
- Passwords
- Tokens
- Certificates

⚠️ Stored in base64 format (not encrypted by default)

---

## 📌 Types of Secrets

- Opaque (default)
- TLS Secret
- Docker registry secret

---

# 📌 ConfigMap vs Secret

| Feature        | ConfigMap | Secret |
|---------------|-----------|--------|
| Sensitive Data | ❌ No | ✅ Yes |
| Stored as | Plain text | Base64 |
| Use Case | App configs | Passwords, Tokens |

---

# 🧠 Real DevOps Example

Example Application:

Frontend Pod
Backend Pod
Database Pod

You will use:

- Service → To connect frontend & backend
- ConfigMap → Store app configuration
- Secret → Store DB password
- Ingress → Expose app to internet

---



✔ Expose application using Service  
✔ Route traffic using Ingress  
✔ Store configuration using ConfigMap  
✔ Secure data using Secret  

---

# 🚀 Kubernetes Phase 1 Lab – Configuration & Networking

## 🎯 Objective
Learn and practice:
- ConfigMap
- Secret
- Service (ClusterIP & NodePort)
- Port Forwarding

Environment:
- AWS EC2 (Amazon Linux)
- Minikube Kubernetes Cluster

---

# 🖥️ STEP 1: Launch EC2 Instance

1. Go to AWS Console → EC2 → Launch Instance
2. Choose:
   - AMI: Amazon Linux 2023
   - Instance Type: t2.medium (Minimum recommended)
   - Storage: 20GB
3. Security Group:
   - Allow SSH (22)
   - Allow 30000-32767 (NodePort range)
4. Connect using SSH

```
ssh -i key.pem ec2-user@your-public-ip
```

---

# ⚙️ STEP 2: Install Required Tools

## Update system
```
sudo yum update -y
```

## Install Docker
```
sudo yum install docker -y
sudo systemctl start docker
sudo usermod -aG docker ec2-user
```

Logout and login again.

## Install kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

## Install Minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
```

---

# 🚀 STEP 3: Start Kubernetes Cluster

```
minikube start --driver=docker
```

Check status:
```
kubectl get nodes
```

---

# 📦 STEP 4: Create ConfigMap

Create file:

```
nano configmap.yaml
```

Paste:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: "blue"
  APP_MODE: "production"
```

Apply:
```
kubectl apply -f configmap.yaml
```

Verify:
```
kubectl get configmap
```

---

# 🔐 STEP 5: Create Secret

```
nano secret.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
stringData:
  DB_PASSWORD: "mypassword"
```

Apply:
```
kubectl apply -f secret.yaml
```

Check:
```
kubectl get secret
```

---

# 🚀 STEP 6: Create Deployment

```
nano deployment.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
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
        image: nginx
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: app-config
        - secretRef:
            name: app-secret
```

Apply:
```
kubectl apply -f deployment.yaml
```

Check:
```
kubectl get pods
```

---

# 🌐 STEP 7: Create Service (NodePort)

```
nano service.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

Apply:
```
kubectl apply -f service.yaml
```

Check:
```
kubectl get svc
```

---

# 🌍 STEP 8: Access Application

Get Minikube IP:
```
minikube ip
```

Open in browser:
```
http://<minikube-ip>:30007
```

If not working (EC2 issue), use port-forward:

```
kubectl port-forward service/nginx-service 8080:80
```

Then open:
```
http://<EC2-public-ip>:8080
```

---

# 🧪 BONUS: Test Environment Variables

Exec into pod:

```
kubectl exec -it <pod-name> -- /bin/sh
```

Check:
```
printenv | grep APP
printenv | grep DB
```

You will see values from:
- ConfigMap
- Secret

---

# 📚 What You Learned

✔ How to create ConfigMap  
✔ How to create Secret  
✔ How Deployment uses them  
✔ How Service exposes app  
✔ How Kubernetes networking works  

---

# 🏁 Cleanup

```
kubectl delete all --all
minikube stop
```

---

# 🚀 Kubernetes Advanced Networking Lab
## 🌐 Ingress + Domain Routing (Beginner Friendly)

---

# 🎯 Objective

Learn how to:
- Expose multiple applications using one IP
- Use Ingress Controller
- Route traffic using domain names

---

# 🖥️ STEP 1: Start Minikube

Start cluster:

```
minikube start --driver=docker
```

Enable Ingress addon:

```
minikube addons enable ingress
```

Verify:

```
kubectl get pods -n ingress-nginx
```

You should see nginx controller running.

---

# 📦 STEP 2: Create Two Sample Applications

We will deploy:
- app1
- app2

---

## 🟢 Create App1 Deployment

```
nano app1.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: hashicorp/http-echo
        args:
        - "-text=Hello from App1"
        ports:
        - containerPort: 5678
```

Apply:

```
kubectl apply -f app1.yaml
```

---

## 🟢 Create App1 Service (ClusterIP)

```
nano app1-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app1-service
spec:
  type: ClusterIP
  selector:
    app: app1
  ports:
  - port: 80
    targetPort: 5678
```

Apply:
```
kubectl apply -f app1-service.yaml
```

---

## 🔵 Create App2 Deployment

```
nano app2.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - name: app2
        image: hashicorp/http-echo
        args:
        - "-text=Hello from App2"
        ports:
        - containerPort: 5678
```

Apply:
```
kubectl apply -f app2.yaml
```

---

## 🔵 Create App2 Service

```
nano app2-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  type: ClusterIP
  selector:
    app: app2
  ports:
  - port: 80
    targetPort: 5678
```

Apply:
```
kubectl apply -f app2-service.yaml
```

---

# 🌐 STEP 3: Create Ingress Resource

```
nano ingress.yaml
```

Paste:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: app1.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
  - host: app2.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
```

Apply:

```
kubectl apply -f ingress.yaml
```

---

# 🌍 STEP 4: Configure /etc/hosts (Important)

Get Minikube IP:

```
minikube ip
```

Example:
```
192.168.49.2
```

Edit hosts file:

```
sudo nano /etc/hosts
```

Add:

```
192.168.49.2 app1.local
192.168.49.2 app2.local
```

---

# 🌐 STEP 5: Test in Browser

Open:

```
http://app1.local
```

You should see:
Hello from App1

Open:

```
http://app2.local
```

You should see:
Hello from App2

---

# 🧠 What You Learned

✔ What is Ingress  
✔ Why ClusterIP is used internally  
✔ How one IP handles multiple apps  
✔ Domain-based routing  
✔ NGINX Ingress Controller  

---

# 🏁 Cleanup

```
kubectl delete -f .
minikube stop
```

---

# 💡 Real-World Understanding

Without Ingress:
- Need multiple LoadBalancers (cost 💰)

With Ingress:
- Single entry point
- Domain-based routing
- Production ready setup

---

# 🎯 Interview Tip

If interviewer asks:

"What is difference between NodePort and Ingress?"

Answer:

NodePort:
- Exposes one service per port

Ingress:
- Smart router
- One entry point
- Domain-based routing
- Production recommended

# 🔐 Kubernetes HTTPS Lab
## Ingress + TLS (Secure Application)

---

# 🎯 Objective

Enable HTTPS for your Kubernetes app using:
- Self-signed certificate
- TLS secret
- Ingress configuration

---

# 🖥️ STEP 1: Start Minikube

```
minikube start --driver=docker
minikube addons enable ingress
```

Verify:

```
kubectl get pods -n ingress-nginx
```

---

# 📦 STEP 2: Deploy Sample App

Create deployment:

```
nano secure-app.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      containers:
      - name: secure-app
        image: nginx
        ports:
        - containerPort: 80
```

Apply:

```
kubectl apply -f secure-app.yaml
```

---

Create service:

```
nano secure-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: secure-service
spec:
  type: ClusterIP
  selector:
    app: secure-app
  ports:
  - port: 80
    targetPort: 80
```

Apply:
```
kubectl apply -f secure-service.yaml
```

---

# 🔐 STEP 3: Generate TLS Certificate

Run:

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout tls.key -out tls.crt \
-subj "/CN=secure.local/O=secure.local"
```

This creates:
- tls.crt
- tls.key

---

# 🔒 STEP 4: Create TLS Secret

```
kubectl create secret tls secure-tls \
--cert=tls.crt \
--key=tls.key
```

Verify:

```
kubectl get secret
```

---

# 🌐 STEP 5: Create HTTPS Ingress

```
nano secure-ingress.yaml
```

Paste:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - secure.local
    secretName: secure-tls
  rules:
  - host: secure.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: secure-service
            port:
              number: 80
```

Apply:

```
kubectl apply -f secure-ingress.yaml
```

---

# 🌍 STEP 6: Update /etc/hosts

Get Minikube IP:

```
minikube ip
```

Edit hosts:

```
sudo nano /etc/hosts
```

Add:

```
<minikube-ip> secure.local
```

---

# 🌐 STEP 7: Test HTTPS

Open in browser:

```
https://secure.local
```

You may see:
⚠️ "Not Secure"

This is normal because:
We used self-signed certificate.

Click "Advanced" → Proceed.

---

# 🧠 What You Learned

✔ What is TLS  
✔ How HTTPS works in Kubernetes  
✔ TLS secret creation  
✔ Ingress HTTPS configuration  
✔ Real production-level networking  

---

# 🏁 Cleanup

```
kubectl delete -f .
minikube stop
```

---

# 🎯 Real-World Understanding

In Production:
- Instead of self-signed cert
- We use:
  - Let's Encrypt
  - AWS ACM
  - cert-manager

Companies ALWAYS use HTTPS.

---

# 💼 Interview Question

Q: How do you enable HTTPS in Kubernetes?

Answer:

- Generate certificate
- Create TLS secret
- Reference secret in Ingress TLS block
