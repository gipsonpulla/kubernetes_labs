# Stateful
Stateful applications require data persistence. A typical example is the
deployment and management of containerized databases, a cornerstone
for any robust cloud-native architecture. Usually, these use cases are
more complex than stateless applications and require the simultaneous
application of multiple YAML files, streamlining the configuration
process in Kubernetes environments. Delving deeper, the chapter explores
the cloud-native Software Development Lifecycle (SDLC), offering
insights into the best practices and methodologies that underpin successful
cloud-native deployments. Complex applications mark an increase in
the realm of polyglot, multi-language cloud application development,
highlighting the versatility and adaptability required in today's diverse
technological landscape.

The following YAML configuration deploys PostgreSQL in Kubernetes. This
configuration includes a deployment and a service. The deployment manages the
PostgreSQL container, while the service exposes PostgreSQL to other parts of the cluster or outside. Remember that we should adjust the values like passwords, storage sizes, and other configurations to suit our specific needs and environment. Also, it's crucial to handle secrets like passwords more securely in a production environment, possibly using Kubernetes secrets or another secure method:

```yaml
apiVersion: v1
kind: Service
metadata:
    name: postgres-service
spec:
    selector:
        app: postgres
    ports:
        - protocol: TCP
          port: 5432
          targetPort: 5432
    type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:latest
          env:
            - name: POSTGRES_DB
              value: mydatabase
            - name: POSTGRES_USER
              value: myuser
            - name: POSTGRES_PASSWORD
              value: mypassword
          ports:
            - containerPort: 5432
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgres-storage
      volumes:
        - name: postgres-storage
          persistentVolumeClaim:
            claimName: postgres-pvc
```

In this solution
• A service named postgres-service is defined to expose PostgreSQL.
• A deployment named postgres-deployment is created for
PostgreSQL.
• Environment variables (POSTGRES_DB, POSTGRES_USER,
POSTGRES_PASSWORD) are set for the initial database setup.
• Port 5432 is exposed as it's the default for PostgreSQL.
• A volume (postgres-storage) is attached for persistent data storage.
We need to create a PersistentVolumeClaim (PVC) named postgrespvc
in our cluster for this to work.