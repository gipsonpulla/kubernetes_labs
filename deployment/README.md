# List All the Namespaces

kubectl get namespace

kubectl get ns

Remember that Kubernetes comes with four default namespaces: default,
kube-node-lease, kube-public, and kube-system. While creating
namespaces, we must avoid being reserved by Kubernetes names, starting
with “kube-”.

# Create a Namespace

kubectl create ns test1

kubectl get ns

kubectl create ns test2

kubectl create ns test3 -o yaml -–dry-run=client > test3-ns.yaml

kubectl apply -f test3-ns.yaml

# Create a pod in ns

kubectl config set-context --current --namespace=test1

kubectl run nginx --image=nginx

kubectl get pods

kubectl config set-context --current --namespace=test2

kubectl get pods

We can see our pod from “namespace 1” is nicely isolated from other
namespaces.


# Create a Pod with Environment Variables as Configuration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dependent-envars-demo
spec:
  containers:
    - name: dependent-envars-demo
      image: nginx
      env:
        - name: SERVICE_PORT
          value: "8000"
```
"A similar would apply to mysql image"

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
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
          image: mysql:5.7 # Use the desired MySQL version
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "myrootpw"
            - name: MYSQL_DATABASE
              value: "mydb"
            - name: MYSQL_USER
              value: "myuser"
            - name: MYSQL_PASSWORD
              value: "mypw"
            - name: MYSQL_HOST
              value: "myhost"
          ports:
            - containerPort: 3306
```

# Create a Multi-container Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
  labels:
    purpose: multi-containers
spec:
  containers:
    - name: web-app
      image: my-web-app:latest
      ports:
        - containerPort: 80
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log

    - name: log-sidecar
      image: my-logging-sidecar:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log

  volumes:
    - name: shared-logs
      emptyDir: {}
```
kubectl apply -f multicontainer.yaml

kubectl get pods

kubectl exec -it multi-container-pod /bin/bash
kubectl exec -it multi-container-pod -c web-app -- /bin/bash
If we don’t know the YAML location and any container name, we can just describe the pod to see all the containers, or we can just run kubectl:
kubectl get pods <POD_NAME_HERE> -o jsonpath='{.spec.containers[*].name}'
kubectl get pods web-app -o jsonpath='{.spec.containers[*].name}'

# Create a Pod with Resource Limits
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  labels:
    purpose: demonstrate-resource-limits
spec:
  containers:
    - name: example-container
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"

    - name: log-aggregator
      image: log-aggregator:v1
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m" 
```

# Apply a JSON Patch Operation

[
  {
    "op": "replace",
    "path": "/spec/replicas",
    "value": 5
  }
]
kubectl patch deployment <deployment-name> --type='json' --patch-file=patch.json

# Create Secrets
