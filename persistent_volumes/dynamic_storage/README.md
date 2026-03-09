Dynamically Provisioning Storage
Storage Classes abstract the underlying storage details and enable dynamic provisioning. Instead of manually creating and binding PVs, you can request storage through PVCs, and Kubernetes will handle the rest. They are ideal for production environments, where automation and flexibility are crucial. Storage Classes can cater to different types of storage, performance, and availability requirements.

In this challenge you will create two Storage Classes (one for hostPath, another one for NFS) and use them to automatically provision the storage for your applications. You will start with the hostPath app.

kubectl create ns challenge3
kubectl config set-context --current --namespace=challenge3
Your minikube Kubernetes cluster has the storage-provisioner add-on enabled, which means that it comes with a Storage Class for hostPath volume out of the box. Run: kubectl get storageclass csi-hostpath-sc to get its details.

pslearner@ip-172-31-24-7:~$ kubectl get storageclass csi-hostpath-sc
NAME              PROVISIONER           RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
csi-hostpath-sc   hostpath.csi.k8s.io   Delete          Immediate           false                  83m

Reclaim Policy: Set to Delete. It determines what happens to the PV when the PVC is deleted. If set to Delete, the storage resource is released back to the system. If set to Retain, the storage resource is not deleted and can be reused.

Volume Binding Mode: Set to Immediate. It indicates that the volume binding and dynamic provisioning occur immediately after the PVC is created. You can also set this to Wait, which ensures the specific requirements like zone or resource constraint of the PV being bound. If meeting specific requirements is crucial for the application, you should set this value to Wait. If speed is more important, set it to Immediate.


pslearner@ip-172-31-24-7:~$ kubectl get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS      REASON   AGE
persistentvolume/my-pv                                      10Gi       RWO            Retain           Bound    challenge2/my-pvc         manual                     48m
persistentvolume/pvc-c6290a50-08c9-4560-aef5-be98e2990b77   1Gi        RWO            Delete           Bound    challenge3/hostpath-pvc   csi-hostpath-sc            43s

NAME                                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
persistentvolumeclaim/hostpath-pvc   Bound    pvc-c6290a50-08c9-4560-aef5-be98e2990b77   1Gi        RWO            csi-hostpath-sc   45s
ps