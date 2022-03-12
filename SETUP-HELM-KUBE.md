# Setup minikube environment

```
➜  minikube minikube start --memory 9963 --cpus 6
😄  minikube v1.24.0 on Darwin 12.1
✨  Automatically selected the docker driver
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=6, Memory=9963MB) ...
🐳  Preparing Kubernetes v1.22.3 on Docker 20.10.8 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass

❗  /Users/rajesh/Learn/openshift/kubectl is version 0.21.0-beta.1, which may have incompatibilites with Kubernetes 1.22.3.
    ▪ Want kubectl v1.22.3? Try 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

```

# Helm Install Ops Manager

```
helm install worldline-enterprise-operator . --namespace=worldline-ops-manager --create-namespace --set operator.watchNamespace="*" --atomic

➜  minikube kubectl get pods -n worldline-ops-manager

```

# Get Ops Manager URL

```
kubectl get om -n worldline-ops-manager
kubectl get pods -n worldline-ops-manager
kubectl get services -n worldline-ops-manager

➜  minikube kubectl port-forward svc/worldline-ops-manager-svc-ext 8080:8080 -n worldline-ops-manager
```

# Create MongoDB Cluster using Helm

## Access Ops Manager portal

--> Create API Key with Organization Owner role

# Click on Setup Kubernetes

# Add greenlist entry - IP of Pod where MongoDB Operator is running

```
kubectl get pods -n worldline-dev -o wide
```

# Copy Public API Key

# Copy User

# Get Ops Manager URL for use in Mongo cluster creation

# Get Ops Manager Project Name

# Get OrgID from Ops Manager Console

# Update automation/mongocluster/values.yaml with the required configuration parameters

# Helm Install MongoDB Cluster

```
helm install worldline-mongodb-dev . --namespace=worldline-dev --create-namespace --atomic

```

# Port Forward MongoDB Cluster

Access using Compass

```
kubectl get mongodb -n worldline-dev
kubectl get pods -n worldline-dev

kubectl port-forward payment-dev-0 27017:27017 -n worldline-dev
kubectl port-forward svc/payment-dev-svc 27017:27017 -n worldline-dev

minikube stop

```
