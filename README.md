# Installation Guide: MongoDB Community Operator on Kubernetes using kubectl

## Introduction
This installation guide will walk you through the step-by-step process of installing the MongoDB Community Operator on Kubernetes using kubectl. By following these instructions, you will have MongoDB up and running on your Kubernetes system.

## Prerequisites
Before proceeding with the installation, ensure that you have the following prerequisites:
- Kubernetes cluster properly set up and accessible via kubectl.
- Terminal or command line interface with kubectl installed.
- Basic knowledge of Kubernetes concepts.

## Step 1: Create Namespace
1. Open your terminal or command line interface.
2. Run the following command to create a namespace for MongoDB:
```bash
kubectl create namespace mongodb
```

## Step 2: Clone MongoDB Kubernetes Operator Repository
1. Open your terminal or command line interface.
2. Run the following commands to clone the MongoDB Kubernetes Operator repository and navigate to its directory:
```bash
git clone https://github.com/mongodb/mongodb-kubernetes-operator.git
cd mongodb-kubernetes-operator/
```

## Step 3: Configure Cluster Role Binding
1. Open the `cluster_role_binding.yaml` file located in the `deploy/clusterwide` directory using a text editor.
2. Replace the `<namespace>` placeholder with "mongodb".
3. Save the file.

## Step 4: Apply Cluster-wide Configuration
1. Run the following command to apply the cluster-wide configuration for the MongoDB Operator:
```bash
kubectl apply -f deploy/clusterwide -n mongodb
```

## Step 5: Apply RBAC Configuration
1. Run the following command to apply the RBAC (Role-Based Access Control) configuration for the MongoDB Operator:
```bash
kubectl apply -k config/rbac --namespace mongodb
```

## Step 6: Apply Custom Resource Definitions (CRDs)
1. Run the following command to apply the custom resource definitions for the MongoDB Operator:
```bash
kubectl apply -f config/crd/bases/mongodbcommunity.mongodb.com_mongodbcommunity.yaml -n mongodb
```

## Step 7: Verify Role and Service Account
1. Run the following commands to verify the role and service account for the MongoDB Operator:
```bash
kubectl get role mongodb-kubernetes-operator --namespace mongodb
kubectl get rolebinding mongodb-kubernetes-operator -n mongodb
kubectl get serviceaccount mongodb-kubernetes-operator -n mongodb
```

## Step 8: Deploy MongoDB Operator Manager
1. Run the following command to deploy the MongoDB Operator manager:
```bash
kubectl create -f config/manager/manager.yaml --namespace mongodb
```

## Step 9: Check Pods
1. Run the following command to check the status of the MongoDB Operator pods:
```bash
kubectl get pods --namespace mongodb
```

## Step 10: Customize MongoDB Configuration (Optional)
1. If you want to customize the MongoDB configuration, open the `mongodbcommunity_cr.yaml` file located in the `config/samples` directory.
2. Replace the `<your-password-here>` placeholder with your desired password.
3. Save the file.

## Step 11: Create Persistent Volumes (PVs)
1. Create two directories for persistent volumes (PVs):
```bash
mkdir /mnt/data3
mkdir /mnt/data4
```
2. Open the `pv-logs.yaml` file located in the `pv` directory using a text editor.
3. Replace the placeholders with appropriate values for storage capacity, path, and node name.
4. Save the file and repeat the process for `pv-data.yaml`.
5. Apply the PVs by running the following commands:
```bash
kubectl apply -f pv/pv-logs.yaml
kubectl apply -f pv/pv-data.yaml
```

## Step 12: Deploy MongoDB Community CR
1. If you haven't customized the MongoDB configuration, run the following command to directly apply the MongoDB Community CR:
```bash
kubectl apply -f config/samples/mongodb.com_v1_mongodbcommunity_cr.yaml --namespace mongodb
```

## Step 13: Check MongoDB Community Status
1. To check the status of the MongoDB Community deployment, use the following command:
```bash
kubectl get mongodbcommunity --namespace mongodb
```
2. For more details, run the following command:
```bash
kubectl describe mongodbcommunity --namespace mongodb
```

## Step 14: Bind Port for External Access (Optional)
1. If you want to access MongoDB from outside your Kubernetes cluster, you can bind a port using the following command:
```bash
kubectl port-forward svc/[service-name] -n [namespace] [external-port]:[internal-port] --address='0.0.0.0'
```

## Conclusion
Congratulations! You have successfully installed the MongoDB Community Operator on your Kubernetes system. Now you can leverage the power of MongoDB for your applications. If you found this installation guide helpful, please refer to our channel for more informative content.


Thank you for following this installation guide! If you have any questions or need further assistance, please leave a comment below. Stay tuned for more exciting tutorials, and happy coding!
