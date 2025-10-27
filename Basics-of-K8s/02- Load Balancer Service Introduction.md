### 🧩 Load Balancer Service Introduction

#### 🚀 What are Services in Kubernetes?

* Services are **abstractions** that expose a set of pods as a **network service**.
* They provide **stable networking** and **load balancing** between pods, which are otherwise ephemeral.
* A service ensures that users don’t need to know pod IPs — they access via the **Service name or IP**.

#### ⚖️ What is a Load Balancer Service?

* A **LoadBalancer Service** exposes an application to the **internet** using an **external load balancer**.
* It works on top of a **cloud provider’s load balancer** (like Google Cloud Load Balancer, Azure LB, AWS ELB).

#### ⚙️ How it Works

1. You create a **Service** of type `LoadBalancer`.
2. Kubernetes requests your **cloud provider** to create a **Load Balancer**.
3. The cloud LB routes incoming traffic to the **NodePorts** of your service.
4. Kubernetes internally forwards traffic from those NodePorts to **matching pods**.

```
[Internet User]
      ↓
[External Load Balancer]
      ↓
[K8s NodePort Service]
      ↓
[Pods Running the App]
```

#### 🌐 Verify Before LB Service Creation (Azure Example)

* ✅ Azure Standard Load Balancer created for AKS Cluster.
* ✅ Frontend IP Configuration exists.
* ✅ Load Balancing Rules configured.
* ✅ Azure Public IP assigned.

---

### 🧩 Step-01: Demo - Expose Pod with a LoadBalancer Service

#### 🛠️ Create a Pod

```bash
kubectl run <desired-pod-name> --image <Container-Image>
kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0
```

#### 🌍 Expose Pod as a Service

```bash
kubectl expose pod <Pod-Name>  --type=LoadBalancer --port=80 --name=<Service-Name>
kubectl expose pod my-first-pod  --type=LoadBalancer --port=80 --name=my-first-service
```

#### 🔎 Get Service Info

```bash
kubectl get service
kubectl get svc
```

📘 **Observation:**

1. Initially `EXTERNAL-IP` will show as **Pending**.
2. After 2–3 minutes, it will get assigned by the cloud provider.

#### 🧾 Sample Output

```
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
my-first-service   LoadBalancer   10.96.0.150    34.93.115.42     80:31234/TCP   3m
```

#### 📋 Describe Service

```bash
kubectl describe service my-first-service
```

🔍 **Output Snippet:**

```
Name:                     my-first-service
Type:                     LoadBalancer
IP:                       10.96.0.150
LoadBalancer Ingress:     34.93.115.42
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
Endpoints:                10.4.2.7:80
```

#### 🌐 Access Application

```bash
http://<External-IP-from-get-service-output>
curl http://<External-IP-from-get-service-output>
```

✅ Verify in Cloud Console:

* Google Load Balancer → Created automatically.
* Backends → Pods (nodes) registered.
* Frontends → Public IP.
* GKE Dashboard → Services and workloads listed.

---

### 🧩 Step-02: Interact with a Pod

#### 🔍 Step-02-01: Verify Pod Logs

```bash
kubectl get po
kubectl logs <pod-name>
kubectl logs my-first-pod
```

💡 **Stream logs in real time:**

```bash
kubectl logs -f my-first-pod
```

🧾 **Sample Output:**

```
10.24.0.1 - - [27/Oct/2025:06:41:15 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.68.0"
```

📘 Reference: [Interacting with Pods (kubectl cheatsheet)](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

#### 🧩 Step-02-02: Connect to a Container in POD

```bash
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it my-first-pod -- /bin/bash
```

🧠 **Example Commands:**

```bash
ls
cd /usr/share/nginx/html
cat index.html
exit
```

🧾 **Sample Output:**

```
root@my-first-pod:/usr/share/nginx/html# ls
50x.html  index.html
root@my-first-pod:/usr/share/nginx/html# cat index.html
<html><h1>Welcome to Kubernetes NGINX!</h1></html>
```

#### ⚙️ Step-02-03: Run Individual Commands

```bash
kubectl exec -it my-first-pod -- env
kubectl exec -it my-first-pod -- ls
kubectl exec -it my-first-pod -- cat /usr/share/nginx/html/index.html
```

---

### 🧩 Step-03: Get YAML Output of Pod & Service

```bash
kubectl get pod my-first-pod -o yaml
kubectl get service my-first-service -o yaml
```

📄 **Sample Snippet:**

```
apiVersion: v1
kind: Service
metadata:
  name: my-first-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 80
  selector:
    run: my-first-pod
```

---

### 🧩 Step-04: Clean-Up

```bash
kubectl get all
kubectl delete svc my-first-service
kubectl delete pod my-first-pod
kubectl get all
```

---

### 🧠 LOGS - More Options

```bash
# Return snapshot logs from pod nginx
kubectl logs nginx

# Return previous terminated container logs
kubectl logs -p -c ruby web-1

# Stream logs live from container
kubectl logs -f -c ruby web-1

# Display last 20 lines
kubectl logs --tail=20 nginx

# Show logs from last 1 hour
kubectl logs --since=1h nginx
```

---

### 🌳 **Concept Tree Summary (Chat Tree)**

```
Kubernetes Services
│
├── ClusterIP (Default)
│   └── Internal-only access within cluster.
│
├── NodePort
│   └── Exposes service on each node's IP:Port.
│
└── LoadBalancer
    ├── Integrates with Cloud LB (GCP, Azure, AWS)
    ├── Provides External IP to access app
    ├── Distributes traffic across pods
    └── Auto-creates forwarding rules and targets
```

---

### 🧩 Real-World Example (GCP)

* **Pod:** `my-first-pod`
* **Service:** `my-first-service`
* **External IP:** `34.93.115.42`
* **Result:** Access your app → `http://34.93.115.42`

🧾 **Output:**

```
<html><h1>Welcome to Kubernetes NGINX!</h1></html>
```

✅ LoadBalancer created in GCP Console → Network Services → Load Balancing.
✅ Backend Service = Node Pool Instances.
✅ Health Checks auto-managed by GKE.
✅ External IP = Public endpoint for your application.
