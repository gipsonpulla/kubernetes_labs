# kubernetes_labs
Using Persistent Storage
Kubernetes (K8s) is great for managing your containerized applications, allowing them to scale and recover from failures quickly - K8s will just spin up a new container if the previous one encountered an error, allowing you to minimize your application downtime. However, containers are ephemeral by nature, which means that when a container is destroyed, the data it generated while running, will be destroyed too. To overcome this issue in Kubernetes, you can use Persistent Volumes (PV) and Persistent Volume Claims (PVC).

Persistent Volumes allow you to abstract the different types of storage solutions available in your infrastructure. Some of the common PV types include:

CSI (Container Storage Interface): A standard that allows Kubernetes to use different storage systems. With this PV type, you can use storage solutions from the different cloud providers (Amazon Web Services, Microsoft Azure, Google Cloud, etc.).
FC (Fibre Channel): High-speed network technology for connecting storage to servers.
hostPath: Uses a directory on the host node’s filesystem. This is good for single-node testing but it does not work for multi-node clusters.
iSCSI (Internet Small Computer Systems Interface): Network protocol for linking storage devices.
Local: Uses local storage devices directly attached to nodes.
NFS (Network File System): Allows multiple nodes to share the same storage over a network. You, as a Kubernetes administrator, will provision the PV at the cluster level, then you will create a PVC to allow the pods within a specific namespace to use that volume - PVs are cluster-scoped resources whereas PVCs are namespaced resources.

Steps:
kubectl create ns challenge2
kubectl config set-context --current --namespace=challenge2

#apply
kubectl apply -f storage_pv.yaml

