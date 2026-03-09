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
A similar would apply to mysql image
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
