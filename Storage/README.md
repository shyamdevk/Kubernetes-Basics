# 💾 Kubernetes Storage

![Kubernetes](https://github.com/shyamdevk/Kubernetes-Basics/blob/images/volume.gif)

> This document explains Kubernetes storage concepts in a simple and clear way.
> Storage is one of the most asked topics in DevOps interviews.

---

# 📌 Why Storage is Important?

By default, Pods are **temporary**.

If a Pod is deleted:
- All data inside it is lost ❌

But applications like:
- Databases
- Logging systems
- File uploads

👉 Need permanent storage.

This is where Kubernetes Storage comes in.

---

# 📦 1️⃣ Volumes in Kubernetes

A **Volume** allows containers inside a Pod to store data.

Volumes exist as long as the Pod exists.

---

# 🗂 2️⃣ emptyDir Volume

## 🔎 What is emptyDir?

- Created when Pod starts
- Deleted when Pod is removed

📌 Used for:
- Temporary storage
- Cache
- Sharing files between containers in same Pod

⚠️ Not persistent.

---

# 🖥 3️⃣ hostPath Volume

## 🔎 What is hostPath?

- Mounts a file or directory from the Node
- Data stored on the worker node

📌 Used for:
- Testing
- Single-node clusters

⚠️ Not recommended for production (not portable).

---

# 💽 4️⃣ Persistent Storage (Main Interview Topic)

To store data permanently, Kubernetes uses:

- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)

---

# 📦 5️⃣ PersistentVolume (PV)

## 🔎 What is PV?

A **PersistentVolume** is actual storage in the cluster.

It can be:
- AWS EBS
- Azure Disk
- NFS
- Local storage

📌 Created by:
- Admin
OR
- Automatically (Dynamic Provisioning)

---

# 📝 6️⃣ PersistentVolumeClaim (PVC)

## 🔎 What is PVC?

A **PersistentVolumeClaim** is a request for storage.

Think of it like:
> "I need 5GB storage"

Kubernetes finds a matching PV and connects it.

---

# 🔄 How PV and PVC Work Together

1️⃣ Admin creates PV (or dynamic provisioning creates it)

2️⃣ User creates PVC

3️⃣ Kubernetes binds PVC to matching PV

4️⃣ Pod uses PVC

---

# 📊 Access Modes (Important)

| Access Mode | Meaning |
|-------------|----------|
| ReadWriteOnce (RWO) | One node can read & write |
| ReadOnlyMany (ROX) | Many nodes can read only |
| ReadWriteMany (RWX) | Many nodes can read & write |

Very common interview question.

---

# ⚙️ 7️⃣ StorageClass

## 🔎 What is StorageClass?

StorageClass is used for **dynamic provisioning**.

Instead of manually creating PV:
👉 Kubernetes automatically creates storage when PVC is created.

Example:
- AWS EBS storage
- Azure Disk
- GCP Persistent Disk

---

# 🔁 Static vs Dynamic Provisioning

## Static Provisioning
- Admin manually creates PV
- User creates PVC

## Dynamic Provisioning
- Only PVC is created
- StorageClass automatically creates PV

👉 Dynamic provisioning is used in production.

---

# 🗑 Reclaim Policy (Interview Question)

When PVC is deleted, what happens to PV?

| Policy | Meaning |
|--------|---------|
| Retain | Keep data |
| Delete | Delete storage |
| Recycle | Basic cleanup (deprecated) |

---

# 🧠 Real DevOps Example

Application:
- Frontend
- Backend
- MySQL Database

For MySQL:
- Use PVC
- Use StorageClass
- Data remains safe even if Pod restarts

---

# ⚠️ Important Interview Points

- Pods are temporary
- emptyDir is temporary
- hostPath is node dependent
- PVC requests storage
- PV provides storage
- StorageClass enables dynamic provisioning
- RWO vs RWX difference

---

# 🚀 Kubernetes Storage Lab

Environment:
- AWS EC2 (Amazon Linux)
- Minikube

---

# 🎯 What You Will Learn

1️⃣ emptyDir (Temporary storage)  
2️⃣ hostPath (Node-level storage)  
3️⃣ Persistent Volume (PV)  
4️⃣ Persistent Volume Claim (PVC)  

---

# 🖥️ STEP 1: Start Cluster

```
minikube start --driver=docker
```

Verify:

```
kubectl get nodes
```

---

# ===============================
# 🟢 LAB 1: emptyDir (Temporary Storage)
# ===============================

## 📌 What is emptyDir?

- Created when Pod starts
- Deleted when Pod is deleted
- Used for temporary data

---

## STEP 1: Create Pod with emptyDir

```
nano emptydir.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: temp-storage
  volumes:
  - name: temp-storage
    emptyDir: {}
```

Apply:

```
kubectl apply -f emptydir.yaml
```

---

## STEP 2: Write Data Inside Pod

```
kubectl exec -it emptydir-pod -- /bin/sh
```

Inside container:

```
echo "Hello Kubernetes" > /data/test.txt
cat /data/test.txt
```

Exit.

---

## STEP 3: Delete Pod

```
kubectl delete pod emptydir-pod
```

Recreate:

```
kubectl apply -f emptydir.yaml
```

Check file again:

```
kubectl exec -it emptydir-pod -- ls /data
```

⚠️ File is gone.

✅ Because emptyDir is temporary.

---

# ===============================
# 🔵 LAB 2: hostPath (Node Storage)
# ===============================

## 📌 What is hostPath?

- Stores data on Node machine
- Data remains even if Pod deleted
- Not recommended for production (only testing)

---

## STEP 1: Create Pod with hostPath

```
nano hostpath.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: node-storage
  volumes:
  - name: node-storage
    hostPath:
      path: /mnt/data
      type: DirectoryOrCreate
```

Apply:

```
kubectl apply -f hostpath.yaml
```

---

## STEP 2: Write Data

```
kubectl exec -it hostpath-pod -- /bin/sh
```

Inside:

```
echo "Stored on Node" > /data/nodefile.txt
exit
```

---

## STEP 3: Delete Pod

```
kubectl delete pod hostpath-pod
```

Recreate:

```
kubectl apply -f hostpath.yaml
```

Check file:

```
kubectl exec -it hostpath-pod -- cat /data/nodefile.txt
```

✅ File still exists.

Because data stored on Node.

---

# ===============================
# 🔴 LAB 3: Persistent Volume (Production Way)
# ===============================

## 📌 Why PV & PVC?

In real world:
- Storage must survive Pod deletion
- Storage managed separately
- Cloud storage like EBS used

---

# STEP 1: Create Persistent Volume

```
nano pv.yaml
```

Paste:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/pv-data
```

Apply:

```
kubectl apply -f pv.yaml
```

Check:

```
kubectl get pv
```

---

# STEP 2: Create Persistent Volume Claim

```
nano pvc.yaml
```

Paste:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Apply:

```
kubectl apply -f pvc.yaml
```

Check:

```
kubectl get pvc
```

Status should be:
Bound ✅

---

# STEP 3: Use PVC in Pod

```
nano pvc-pod.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: persistent-storage
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

Apply:

```
kubectl apply -f pvc-pod.yaml
```

---

# STEP 4: Test Persistence

```
kubectl exec -it pvc-pod -- /bin/sh
```

Inside:

```
echo "Persistent Data" > /data/persistent.txt
exit
```

Delete Pod:

```
kubectl delete pod pvc-pod
```

Recreate:

```
kubectl apply -f pvc-pod.yaml
```

Check:

```
kubectl exec -it pvc-pod -- cat /data/persistent.txt
```

✅ Data still exists.

---

# 🧠 What You Learned

| Storage Type | Data Saved After Pod Delete? | Production Use? |
|-------------|------------------------------|----------------|
| emptyDir | ❌ No | Temporary only |
| hostPath | ✅ Yes (Node only) | Testing only |
| PV + PVC | ✅ Yes | ✅ Production |

---

# 🎯 Interview Tip

Q: Difference between PV and PVC?

PV:
- Actual storage resource

PVC:
- Request for storage by Pod

Pod does NOT directly use PV.
It uses PVC.

---

# 🏁 Cleanup

```
kubectl delete pod pvc-pod
kubectl delete pvc my-pvc
kubectl delete pv my-pv
kubectl delete pod hostpath-pod
kubectl delete pod emptydir-pod
minikube stop
```

# 🚀 Lab : Dynamic Provisioning (StorageClass)

## 📌 What is Dynamic Provisioning?

Before:

* Admin manually creates PV.

Now:

* Kubernetes automatically creates storage when PVC is created.

This is done using:
👉 StorageClass

---

# 📘 `README.md`

````markdown
# 🚀 Kubernetes Dynamic Storage Lab
Environment:
- AWS EC2 (Amazon Linux)
- Minikube

---

# STEP 1: Start Minikube

```
minikube start --driver=docker
```

Check default storageclass:

```
kubectl get storageclass
```

You should see:
standard (default)

---

# STEP 2: Create PVC (No PV needed!)

```
nano dynamic-pvc.yaml
```

Paste:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply:

```
kubectl apply -f dynamic-pvc.yaml
```

Check:

```
kubectl get pvc
kubectl get pv
```

🎉 Kubernetes automatically created PV!

That is dynamic provisioning.

---

# STEP 3: Use PVC in Pod

```
nano dynamic-pod.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dynamic-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: dynamic-pvc
```

Apply:

```
kubectl apply -f dynamic-pod.yaml
```

Test persistence same as previous lab.

---

````

---

# 🚀 Lab 2: Real AWS EBS Volume with Kubernetes

Now we move to REAL AWS production scenario.

⚠️ This requires:

* EKS cluster
* Or Kubernetes running in AWS with IAM permissions

If using EKS:
You must install AWS EBS CSI Driver.

---

## 📌 Install EBS CSI Driver (EKS)

```
kubectl apply -k \
github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable
```

Check:

```
kubectl get pods -n kube-system
```

---

## 📌 Create StorageClass for EBS

```
nano ebs-sc.yaml
```

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```

Apply:

```
kubectl apply -f ebs-sc.yaml
```

---

## 📌 Create PVC using EBS

```
nano ebs-pvc.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-sc
  resources:
    requests:
      storage: 2Gi
```

Apply:

```
kubectl apply -f ebs-pvc.yaml
```

Check AWS Console → EC2 → Volumes

🎉 You will see new EBS volume created automatically.

That is real production dynamic provisioning.

---

# 🚀 Lab 3: StatefulSet with Storage

## 📌 Why StatefulSet?

Used for:

* Databases
* Applications needing stable storage
* Each Pod gets its own volume

Example: MySQL

---

```
nano mysql-statefulset.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 2
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

Apply:

```
kubectl apply -f mysql-statefulset.yaml
```

Check:

```
kubectl get pods
kubectl get pvc
```

You will see:
mysql-0 → its own PVC
mysql-1 → its own PVC

Each Pod gets separate storage.

That is production-level architecture.

---

# 🧠 What You Learned

| Concept      | Usage                     |
| ------------ | ------------------------- |
| StorageClass | Automatic PV creation     |
| EBS CSI      | AWS integration           |
| StatefulSet  | Databases & stateful apps |

---

# 🎯 Interview Questions

Q: What is Dynamic Provisioning?
→ Automatic PV creation using StorageClass.

Q: Difference between Deployment & StatefulSet?
→ Deployment: Stateless
→ StatefulSet: Stateful + stable storage

Q: How does Kubernetes create EBS automatically?
→ Using CSI Driver.

---

l me your next move 😎
