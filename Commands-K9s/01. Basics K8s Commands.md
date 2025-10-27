# 🚀 **SOP: Basic Kubernetes (`kubectl`) Commands for Beginners**

---

## 🎯 **Objective**

To understand and practice **fundamental `kubectl` commands** for interacting with a Kubernetes cluster — from viewing resources to creating and deleting pods.

---

## ⚙️ **Prerequisites**

✅ Kubernetes cluster (GKE, Minikube, or others)
✅ `kubectl` CLI installed and configured (`kubectl get nodes` should work)
✅ Access credentials set (via `gcloud container clusters get-credentials` or kubeconfig)

---

## 🧩 **1️⃣ Verify Cluster Connection**

| Command                     | Purpose                              | Example Output                          |
| --------------------------- | ------------------------------------ | --------------------------------------- |
| `kubectl version --client`  | Check kubectl client version         | Client Version: v1.31.0                 |
| `kubectl cluster-info`      | Verify cluster API endpoint          | Kubernetes control plane at https://... |
| `kubectl get nodes`         | List nodes in the cluster            | Shows node names and statuses           |
| `kubectl get nodes -o wide` | Detailed node info (IP, OS, version) | Node internal/external IPs visible      |

💡 **Tip:** Seeing `Ready` under STATUS = cluster is healthy.

---

## 🧱 **2️⃣ Explore Available API Resources**

| Command                 | Description                                                                            |
| ----------------------- | -------------------------------------------------------------------------------------- |
| `kubectl api-resources` | Lists all resource types (Pods, Services, Deployments, etc.) supported by your cluster |

💬 Use this to know what Kubernetes objects you can manage.

---

## 🧫 **3️⃣ Create & Inspect a Pod**

| Command                                                          | Description                                              |
| ---------------------------------------------------------------- | -------------------------------------------------------- |
| `kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0` | Creates a new pod named `my-first-pod` with NGINX image  |
| `kubectl get pods` or `kubectl get po`                           | Lists all running pods                                   |
| `kubectl get pods -o wide`                                       | Shows pod details like IP and node name                  |
| `kubectl describe pod my-first-pod`                              | Shows detailed pod info (events, containers, node, etc.) |
| `kubectl delete pod my-first-pod`                                | Deletes the pod                                          |

---

## 🧪 **4️⃣ Dry-Run Mode for Testing**

| Command                                                                                              | Description                                           |
| ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| `kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0 --dry-run=client`                    | Tests creation without actually deploying             |
| `kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0 --dry-run=client -o yaml > pod.yaml` | Generates YAML manifest for editing or applying later |

💡 **Use Case:** Use `--dry-run=client -o yaml` to convert CLI commands into reusable YAML files for GitOps or IaC.

---

## 🧱 **5️⃣ Apply or Manage YAML Files**

| Command                      | Description                          |
| ---------------------------- | ------------------------------------ |
| `kubectl apply -f pod.yaml`  | Creates/updates resources from YAML  |
| `kubectl get pods`           | Verify deployment                    |
| `kubectl delete -f pod.yaml` | Deletes the resource defined in YAML |

💬 YAML > CLI for real-world scenarios (more reproducible, trackable).

---

## 🕵️ **6️⃣ Get Cluster-Wide Information**

| Command                                                    | Description                                                            |
| ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| `kubectl get all`                                          | Lists all running resources (Pods, Services, ReplicaSets, Deployments) |
| `kubectl get namespaces`                                   | Lists all namespaces                                                   |
| `kubectl config get-contexts`                              | Lists all kubeconfig contexts                                          |
| `kubectl config current-context`                           | Shows active cluster context                                           |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | View latest cluster events                                             |

---

## 🧰 **7️⃣ Debug & Troubleshooting Essentials**

| Command                                 | Description                                         |
| --------------------------------------- | --------------------------------------------------- |
| `kubectl logs my-first-pod`             | View container logs                                 |
| `kubectl exec -it my-first-pod -- bash` | SSH into the running pod                            |
| `kubectl describe node <node-name>`     | Check node conditions and resource usage            |
| `kubectl top pod` / `kubectl top node`  | View CPU and memory usage (requires Metrics Server) |

---

## 🧨 **8️⃣ Cleanup**

| Command                           | Description                                |
| --------------------------------- | ------------------------------------------ |
| `kubectl delete pod my-first-pod` | Delete specific pod                        |
| `kubectl delete all --all`        | Delete all resources (⚠️ Use with caution) |
| `kubectl delete namespace <name>` | Delete a namespace and all its objects     |

---

## 🌈 **Quick Summary Table**

| Category     | Core Command                                                                    |
| ------------ | ------------------------------------------------------------------------------- |
| Cluster Info | `kubectl cluster-info`, `kubectl get nodes`                                     |
| Pods         | `kubectl run`, `kubectl get pods`, `kubectl describe pod`, `kubectl delete pod` |
| YAML         | `kubectl apply -f`, `kubectl delete -f`                                         |
| Debug        | `kubectl logs`, `kubectl exec`, `kubectl top`                                   |
| Resources    | `kubectl api-resources`, `kubectl get all`                                      |

---

## 🧠 **Pro Tips**

* Always start with `kubectl get all` when debugging — gives a full cluster snapshot.
* Use namespaces to separate environments (e.g., `dev`, `prod`).
* Combine `-o yaml` and `--dry-run=client` to learn YAML syntax fast.
* Alias shortcut: `alias k=kubectl` → use `k get po`, `k desc po`, etc.

---
