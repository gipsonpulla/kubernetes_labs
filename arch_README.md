# I will run a kubectl cmd? what happens in k8s after that ?

* When you run a **`kubectl` command**, a series of steps happen inside the **Kubernetes architecture** before the action actually occurs.
  Let’s walk through it in a **simple step-by-step flow**.

---

## 1️⃣ You run a `kubectl` command

Example:

```bash
kubectl get pods
```

or

```bash
kubectl apply -f deployment.yaml
```

`kubectl` is just a **client tool**. It does not manage Kubernetes itself — it **talks to the Kubernetes API server**.

---

## 2️⃣ `kubectl` reads your kubeconfig

`kubectl` checks the **kubeconfig file** (usually in `~/.kube/config`).

This file tells:

* Which **cluster** to connect to
* Which **API server endpoint**
* Which **authentication credentials** to use
* Which **namespace**

Example entry:

```yaml
clusters:
- cluster:
    server: https://123.45.67.89:6443
```

---

## 3️⃣ Request goes to the Kubernetes API Server

`kubectl` sends an **HTTP/REST request** to the **API Server**.

Example:

| Command            | API Request                    |
| ------------------ | ------------------------------ |
| `kubectl get pods` | GET /api/v1/pods               |
| `kubectl apply -f` | POST /apis/apps/v1/deployments |

The **API Server is the front door of Kubernetes**.

Everything goes through it.

---

## 4️⃣ Authentication & Authorization happen

The API Server checks:

### Authentication

Who are you?

Examples:

* Certificate
* Token
* OIDC
* Service account

### Authorization

Are you allowed to do this?

Handled by:

* **RBAC**
* ABAC
* Node authorization

Example check:

```
Can user Gipson create pods in namespace dev?
```

---

## 5️⃣ Admission Controllers run

Before saving the request, **admission controllers** inspect it.

Examples:

* **MutatingAdmissionWebhook** → modifies objects
* **ValidatingAdmissionWebhook** → validates requests
* **NamespaceLifecycle**
* **LimitRanger**
* **ResourceQuota**

Example:
If a pod has no limits, `LimitRanger` may add defaults.

---

## 6️⃣ Object stored in etcd

If everything is valid:

The API server **stores the object in `etcd`**.

`etcd` = **Kubernetes database**

Example:

```
Deployment saved in etcd
```

But nothing is running yet.

Kubernetes still needs controllers to act.

---

## 7️⃣ Controllers detect the change

Controllers constantly **watch the API server**.

Example controllers:

* Deployment Controller
* ReplicaSet Controller
* Job Controller
* Node Controller

Example:

If you created:

```
Deployment with 3 replicas
```

Deployment controller notices:

```
Desired: 3 pods
Current: 0 pods
```

So it creates a **ReplicaSet**.

---

## 8️⃣ Scheduler assigns nodes

New pods are **unscheduled** initially.

Scheduler looks for:

* Available CPU / memory
* Node selectors
* Taints / tolerations
* Affinity rules

Then decides:

```
Pod → Node-1
```

---

## 9️⃣ Kubelet runs the pod

The **kubelet** on the chosen node sees the new pod assignment.

It:

1. Pulls the container image
2. Talks to container runtime (containerd / CRI-O)
3. Starts the container

---

## 🔟 Networking and Services

Other components now configure networking:

* **kube-proxy** sets iptables rules
* **CNI plugin** configures pod networking

Now the **pod becomes reachable**.

---

# 📊 Full Flow (Important for Interviews)

```
kubectl command
      │
      ▼
kubeconfig authentication
      │
      ▼
Kubernetes API Server
      │
      ├─ Authentication
      ├─ Authorization (RBAC)
      ├─ Admission Controllers
      │
      ▼
Object stored in etcd
      │
      ▼
Controllers watch API
      │
      ▼
Scheduler selects node
      │
      ▼
Kubelet creates containers
      │
      ▼
Networking configured
      │
      ▼
Pod Running
```

---

💡 **One important concept**

`kubectl` only **declares the desired state**.

Example:

```
I want 3 pods
```

Controllers ensure:

```
Actual state = Desired state
```

This is called **Kubernetes reconciliation loop**.

---

✅ Since you're learning **Kubernetes deeply (services, selectors, port-forward, etc.)**, the next **very important concept** to understand is:

**What happens internally when you run**

```
kubectl apply -f deployment.yaml
```

This involves **3-way merge, declarative state, and reconciliation**, which is a **very common interview question**.

If you want, I can explain that next.

