#Allow a Pod to Communicate with the API Server 

Service Accounts provide an identity for processes running in pods,
allowing them to authenticate with the Kubernetes API and access resources
based on the permissions granted to that service account.

By default, every namespace in Kubernetes has a Service Account named
default. If you don't specify a service account when creating a pod,
Kubernetes automatically assigns this default service account to the pod.
This Service Account does not have any permissions assigned to it by
default (except for basic API discovery permissions if RBAC is enabled) -
it will be able to authenticate and access the Kubernetes API but it will
not be able to perform any operations.

When the Pod gets created, the service account token is typically mounted
at /var/run/secrets/kubernetes.io/serviceaccount/token within each
container of the Pod. This token file allows the application running inside
the pod to authenticate with the Kubernetes API server. Along with the
token, other related files, such as the CA certificate and namespace, are
also mounted in the same directory, providing necessary information for
secure communication with the API. For increased security, if your Pod does
not need to interact with the kube-apiserver, you can set the
automountServiceAccountToken field to false in the pod specification to
avoid the token getting automatically mounted.

Imagine now that you have an online learning platform, where users are able
to spin up lab environments to practice their knowledge. Each of the lab
environments will be a Pod created on demand via your main application,
which also runs in Kubernetes. For your application to provision the new
Pod when a user requests a lab environment, it will need to authenticate
against the kube-apiserver and send the request to create new Pods. Using
the default Service Account will not work because it does not have any
permissions by default.


You will now create a new service account, provision to it the required
permissions via a Role and its respective RoleBinding and finally you will
create a Pod and ensure it can connect and create other Pods in the cluster by
interacting with the Kubernetes API Server.

1. First, create the namespace for this challenge using the kubectl create
   namespace challenge3 command and then switch to it: kubectl config
set-context --current --namespace=challenge3

2. Create a service account: kubectl create serviceaccount pod-access-sa  Then
   run the kubectl get sa and ensure the Service Account pod-access-sa has been
created.

3. You will now create the Role to be used by the service account. Check the
   provided definition: cat ~/challenges/03/role.yaml It defines a Role resource
named pod-writer with permissions to create pods.

4. Create the role: kubectl apply -f ~/challenges/03/role.yaml. Then run the
   kubectl get role command to ensure the Role has been created.

5. In the previous challenge you created the RoleBinding using kubectl commands.
   In this challenge you will create it via a definition file. Check the
provided definition by running the cat ~/challenges/03/rolebinding.yaml command.

It defines a RoleBinding resource named pod-writer-binding, where the subject is
the pod-access-sa service account in the challenge3 namespace.

6. Create the Role Binding: kubectl apply -f ~/challenges/03/rolebinding.yaml.
   Then run the kubectl get rolebinding to ensure the resource has been created
as expected:

7. Now you will create the pod that will use the new service account. Check the
   provided definition: cat ~/challenges/03/pod.yaml It defines a Pod resource
named api-access-pod which runs the busybox:1.37 image and uses the
pod-access-sa service account.

8. Run the kubectl apply -f ~/challenges/03/pod.yaml command to create the pod.

9. Now connect to the new pod and send a request to the Kubernetes API Server to
   create a pod:

Connect to the pod: kubectl exec -it api-access-pod -- sh

Send the POST request:

wget --no-check-certificate --header="Content-Type: application/json"
--header="Authorization: Bearer $(cat
/var/run/secrets/kubernetes.io/serviceaccount/token)"
--post-data='{"apiVersion": "v1", "kind": "Pod", "metadata": {"name":
"test-pod"}, "spec": {"containers": [{"name": "nginx", "image":
"nginx:1.27.2-alpine-slim"}]}}'
https://kubernetes.default.svc/api/v1/namespaces/challenge3/pods -O- Remember,
you can copy commands, and paste them into the Terminal by using
Control+Shift+V.

Note: The POST Request includes: - The contents of the
/var/run/secrets/kubernetes.io/serviceaccount/token file in the Authorization
header to authenticate against the kube-apiserver. - The json with the Pod
definition. The pod is named test-pod and uses the nginx:1.27.2-alpine-slim
image.

10. Check the pod test-pod has been successfully created: kubectl get pods




