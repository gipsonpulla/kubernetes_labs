# Replicaset
* A ReplicaSet's purpose is to maintain a stable set of replica Pods running at
  any given time. Usually, you define a Deployment and let that Deployment
  manage ReplicaSets automatically.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: us-docker.pkg.dev/google-samples/containers/gke/gb-frontend:v5
```

kubectl apply -f https://kubernetes.io/examples/controllers/frontend.yaml

kubectl get rs

kubectl describe rs/frontend

kubectl get pods


