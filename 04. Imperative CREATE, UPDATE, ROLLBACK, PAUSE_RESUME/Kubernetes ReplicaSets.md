# 🚀 **SOP: Kubernetes ReplicaSets**

---

## 🎯 **Objective**

Learn what a **ReplicaSet** is, how to **create, manage, expose, scale, and delete** it in Kubernetes — ensuring **high availability** and **fault tolerance** for your applications.

---

## 🧠 **Step-01: Introduction to ReplicaSets**

### 📘 What is a ReplicaSet?

A **ReplicaSet** ensures that a **specified number of identical pods** are always running in your cluster.
If a pod crashes or gets deleted, the ReplicaSet **automatically creates a new one** to maintain the desired count.

```plaintext
+-------------+
| ReplicaSet  |
+-------------+
       |
       | ensures N pods
       v
+----------+   +----------+   +----------+
| Pod #1   |   | Pod #2   |   | Pod #3   |
| healthy  |   | healthy  |   | crashed  |
+----------+   +----------+   +----------+
                           |
                           v
                  auto-create new pod ✅
```

---

### 💪 Advantages of Using ReplicaSets

| Feature                    | Description                  | Benefit                       |
| -------------------------- | ---------------------------- | ----------------------------- |
| 🩹 **Self-Healing**        | Recreates pods if one fails  | Ensures uptime                |
| 📈 **Scalability**         | Adjust replicas count easily | Handle variable loads         |
| ♻️ **Declarative Control** | Maintains desired state      | Reduces manual ops            |
| 🧩 **Integration**         | Used by Deployments          | Enables rollouts and rollback |

---

## ⚙️ **Step-02: Create ReplicaSet**

---

### 🧩 Step-02-01: Create ReplicaSet

```bash
# Kubernetes ReplicaSet
kubectl create -f replicaset-demo.yml
```

#### 📄 **replicaset-demo.yml**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-helloworld-rs
  labels:
    app: my-helloworld
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-helloworld
  template:
    metadata:
      labels:
        app: my-helloworld
    spec:
      containers:
      - name: my-helloworld-app
        image: stacksimplify/kube-helloworld:1.0.0
```

🧠 **Explanation:**

* `replicas: 3` → Runs 3 pods.
* `selector.matchLabels` → Matches pods by label.
* `template` → Defines the pod structure.

---

### 🔍 Step-02-02: List ReplicaSets

```bash
# List ReplicaSets
kubectl get replicaset
kubectl get rs
```

📊 **Sample Output**

| NAME             | DESIRED | CURRENT | READY | AGE |
| ---------------- | ------- | ------- | ----- | --- |
| my-helloworld-rs | 3       | 3       | 3     | 1m  |

---

### 🧾 Step-02-03: Describe ReplicaSet

```bash
# Describe ReplicaSet
kubectl describe rs/<replicaset-name>

kubectl describe rs/my-helloworld-rs
# or
kubectl describe rs my-helloworld-rs
```

🧩 **Check:**

* Labels
* Selector
* Events
* Number of pods

---

### 📋 Step-02-04: List of Pods

```bash
# Get list of Pods
kubectl get pods
kubectl describe pod <pod-name>

# Get list of Pods with Pod IP and Node in which it is running
kubectl get pods -o wide
```

📊 **Sample Output**

| NAME                   | READY | STATUS  | AGE | IP       | NODE   |
| ---------------------- | ----- | ------- | --- | -------- | ------ |
| my-helloworld-rs-c8rrj | 1/1   | Running | 2m  | 10.1.0.5 | node-1 |
| my-helloworld-rs-ks8hj | 1/1   | Running | 2m  | 10.1.0.6 | node-2 |
| my-helloworld-rs-wn5kl | 1/1   | Running | 2m  | 10.1.0.7 | node-3 |

---

### 🧬 Step-02-05: Verify the Owner of the Pod

```bash
# List Pod with Output as YAML
kubectl get pods <pod-name> -o yaml
kubectl get pods my-helloworld-rs-c8rrj -o yaml
```

🧩 **Look for:**

```yaml
ownerReferences:
- apiVersion: apps/v1
  kind: ReplicaSet
  name: my-helloworld-rs
```

✅ Confirms which ReplicaSet owns the Pod.

---

## 🌐 **Step-03: Expose ReplicaSet as a Service**

```bash
# Expose ReplicaSet as a Service
kubectl expose rs <ReplicaSet-Name>  --type=LoadBalancer --port=80 --target-port=8080 --name=<Service-Name-To-Be-Created>
kubectl expose rs my-helloworld-rs  --type=LoadBalancer --port=80 --target-port=8080 --name=my-helloworld-rs-service

# List Services
kubectl get service
kubectl get svc
```

📊 **Sample Output**

| NAME                     | TYPE         | CLUSTER-IP | EXTERNAL-IP  | PORT(S)      | AGE |
| ------------------------ | ------------ | ---------- | ------------ | ------------ | --- |
| my-helloworld-rs-service | LoadBalancer | 10.8.0.32  | 34.122.10.45 | 80:30755/TCP | 1m  |

---

```bash
# Access the Application using External or Public IP
http://<External-IP-from-get-service-output>/hello
curl http://<External-IP-from-get-service-output>/hello
```

💡 **Observation**

1. Each time you access the app, traffic goes to a different pod.
2. This confirms load balancing across multiple replicas.

---

## 🔁 **Step-04: Test ReplicaSet Reliability or High Availability**

```bash
# To get Pod Name
kubectl get pods

# Delete the Pod
kubectl delete pod <Pod-Name>

# Verify the new pod got created automatically
kubectl get pods   # Verify Age and name of new pod
```

🧩 **Concept**
If one pod dies, ReplicaSet automatically replaces it — ensuring continuous service.

---

## 📈 **Step-05: Test ReplicaSet Scalability**

```yaml
# Before change
spec:
  replicas: 3

# After change
spec:
  replicas: 6
```

```bash
# Apply latest changes to ReplicaSet
kubectl replace -f replicaset-demo.yml

# Verify if new pods got created
kubectl get pods -o wide
```

💡 **Alternate Command**

```bash
# Scale ReplicaSet manually
kubectl scale rs my-helloworld-rs --replicas=6
```

📊 **Sample Output**

| NAME             | DESIRED | CURRENT | READY |
| ---------------- | ------- | ------- | ----- |
| my-helloworld-rs | 6       | 6       | 6     |

---

## 🧹 **Step-06: Delete ReplicaSet & Service**

### Step-06-01: Delete ReplicaSet

```bash
# Delete ReplicaSet
kubectl delete rs <ReplicaSet-Name>

# Sample Commands
kubectl delete rs/my-helloworld-rs
# or
kubectl delete rs my-helloworld-rs

# Verify if ReplicaSet got deleted
kubectl get rs
```

---

### Step-06-02: Delete Service created for ReplicaSet

```bash
# Delete Service
kubectl delete svc <service-name>

# Sample Commands
kubectl delete svc my-helloworld-rs-service
# or
kubectl delete svc/my-helloworld-rs-service

# Verify if Service got deleted
kubectl get svc
```

---

## 🧩 **Additional Notes**

### 🔄 ReplicaSet vs ReplicationController

| Feature        | ReplicaSet                     | ReplicationController |
| -------------- | ------------------------------ | --------------------- |
| API Version    | apps/v1                        | v1                    |
| Label Matching | MatchLabels + MatchExpressions | Only MatchLabels      |
| Used By        | Deployments                    | Deprecated            |
| Flexibility    | High                           | Low                   |
| Recommended    | ✅ Yes                          | ❌ No                  |

---

### 🧰 Common Troubleshooting Commands

```bash
# List all resources
kubectl get all

# View pod logs
kubectl logs <pod-name>

# Check recent cluster events
kubectl get events --sort-by=.metadata.creationTimestamp

# Monitor rollout status
kubectl rollout status rs my-helloworld-rs
```

---

### 🧠 Pro Tip:

In real-world production environments, you rarely create ReplicaSets manually.
Instead, you create a **Deployment**, which **automatically manages ReplicaSets** and handles rollouts and rollbacks.

```plaintext
Deployment → ReplicaSet → Pods
```

---

## ✅ **Summary**

| Concept      | Key Takeaway                         |
| ------------ | ------------------------------------ |
| ReplicaSet   | Maintains desired pod count          |
| Self-Healing | Recreates deleted pods               |
| Scalability  | Adjust replicas easily               |
| Exposure     | LoadBalancer enables external access |
| Lifecycle    | Create → Test → Scale → Delete       |

---

## 🧭 **Flow Summary: ReplicaSet Lifecycle**

```plaintext
┌────────────────┐
│ Create YAML     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ ReplicaSet Created │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Pods Spawned    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Expose as SVC   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Scale or Heal   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Delete Cleanly  │
└────────────────┘
```

---
