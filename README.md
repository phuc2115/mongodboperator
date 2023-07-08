# Installation Guide: MongoDB Community Operator on Kubernetes using kubectl

## Introduction
This installation guide will walk you through the step-by-step process of installing the MongoDB Community Operator on Kubernetes using kubectl. By following these instructions, you will have MongoDB up and running on your Kubernetes system.

## Prerequisites
Before proceeding with the installation, ensure that you have the following prerequisites:
- Kubernetes cluster properly set up and accessible via kubectl.
- Terminal or command line interface with kubectl installed.
- Basic knowledge of Kubernetes concepts.

## Step 1: Create Namespace
```
kubectl create namespace mongodb
```

## Step 2: Clone MongoDB Kubernetes Operator Repository
```
git clone https://github.com/mongodb/mongodb-kubernetes-operator.git
cd mongodb-kubernetes-operator/
```

## Step 3: Configure Cluster Role Binding
- Open the `deploy/clusterwide/cluster_role_binding.yaml` file using a text editor.
- Replace the `<namespace>` placeholder with "mongodb".
- Save the file.

## Step 4: Apply Cluster-wide Configuration
```
kubectl apply -f deploy/clusterwide -n mongodb
```

## Step 5: Apply RBAC Configuration
```
kubectl apply -k config/rbac --namespace mongodb
```

## Step 6: Apply Custom Resource Definitions (CRDs)
```
kubectl apply -f config/crd/bases/mongodbcommunity.mongodb.com_mongodbcommunity.yaml -n mongodb
```

## Step 7: Verify Role and Service Account
```
kubectl get role mongodb-kubernetes-operator --namespace mongodb
kubectl get rolebinding mongodb-kubernetes-operator -n mongodb
kubectl get serviceaccount mongodb-kubernetes-operator -n mongodb
```
## Step 8: Deploy MongoDB Operator Manager
```
kubectl create -f config/manager/manager.yaml --namespace mongodb
```

## Step 9: Check Pods
```
kubectl get pods --namespace mongodb
```

## Step 10: Customize MongoDB Configuration (Optional)
- If you want to customize the MongoDB configuration, open the `config/samples/mongodb.com_v1_mongodbcommunity_cr.yaml` file using a text editor.
- Replace the `<your-password-here>` placeholder with your desired password.
- Save the file.

Certainly! Here's the revised Step 11 with the content of the PV YAML files included:

## Step 11: Create Persistent Volumes (PVs)
1. Create two directories for persistent volumes (PVs):
   ```
   mkdir /mnt/pv-mongodb-data
   mkdir /mnt/ pv-mongodb-logs
   ```
2. Open the `pv/pv-logs.yaml` file using a text editor and replace the placeholders with appropriate values for storage capacity, path, and node name. Save the file.
   - Example content of `pv/pv-logs.yaml`:
     ```yaml
     # pv/pv-logs.yaml

     apiVersion: v1
     kind: PersistentVolume
     metadata:
       name: pv-logs-mongodb-0
       labels:
         app: example-mongodb-svc
     spec:
       capacity:
         storage: 10Gi # Please adjust to match your requirements
       volumeMode: Filesystem
       accessModes:
         - ReadWriteOnce # Ensure this matches the requirement of your application
       persistentVolumeReclaimPolicy: Retain
       storageClassName: "" # No StorageClass specified
       local:
         path: /mnt/pv-mongodb-logs # Replace with the path where the logs should be stored
       nodeAffinity:
         required:
           nodeSelectorTerms:
             - matchExpressions:
                 - key: kubernetes.io/hostname
                   operator: In
                   values:
                     - k8s-node01 # Replace with the name of your Kubernetes node
     ```

3. Apply the PV for logs by running the following command:
   ```
   kubectl apply -f pv/pv-logs.yaml
   ```

4. Open the `pv/pv-data.yaml` file using a text editor and replace the placeholders with appropriate values for storage capacity, path, and node name. Save the file.
   - Example content of `pv/pv-data.yaml`:
     ```yaml
     # pv/pv-data.yaml

     apiVersion: v1
     kind: PersistentVolume
     metadata:
       name: pv-example-mongodb-0
       labels:
         app: example-mongodb-svc
     spec:
       capacity:
         storage: 20Gi # update this according to your needs
       volumeMode: Filesystem
       accessModes:
         - ReadWriteOnce # most common mode, but check your application requirement
       persistentVolumeReclaimPolicy: Retain
       storageClassName: "" # no storage class
       local:
         path: /mnt/pv-mongodb-data  # update this path according to your needs
       nodeAffinity:
         required:
           nodeSelectorTerms:
             - matchExpressions:
                 - key: kubernetes.io/hostname
                   operator: In
                   values:
                     - k8s-node01 # replace this with your actual node name
     ```

5. Apply the PV for data by running the following command:
   ```
   kubectl apply -f pv/pv-data.yaml
   ```
## Step 12: Deploy MongoDB Community CR
- If you haven't customized the MongoDB configuration, run the following command to directly apply the MongoDB Community CR:
   ```
   kubectl apply -f config/samples/mongodb.com_v1_mongodbcommunity_cr.yaml --namespace mongodb
   ```

## Step 13: Check MongoDB Community Status
- To check the status of the MongoDB Community deployment, use the following command:
   ```
   kubectl get mongodbcommunity --namespace mongodb
   ```
- For more details, run the following command:
   ```
   kubectl describe mongodbcommunity --namespace mongodb
   ```

## Step 14: Bind Port for External Access (Optional)
- If you want to access MongoDB from outside your Kubernetes cluster, you can bind a port using the following command:
   ```
   kubectl port-forward services/example-mongodb-svc -n mongodb 8000:27017 --address='0.0.0.0'
   ```

## Conclusion
Congratulations! You have successfully installed the MongoDB Community Operator on your Kubernetes system. Now you can leverage the power of MongoDB for your applications. If you found this installation guide helpful, please refer to our channel for more informative content.

Thank you for following this installation guide! If you have any questions or need further assistance, please leave a comment below. Stay tuned for more exciting tutorials, and happy coding!
