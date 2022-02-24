# Setup OpenShift Account

# Setup CLI

# Setup OpenShift Cluster

# Create a Cluster Admin user

# Create Registry Service Account

# Update "mongodb-enterprise-kubernetes/automation/opsmanager/values.yaml" with the required configuration parameters

# Helm Install Ops Manager

```
# Login to Openshift
oc login $CLUSTER_URL --username $CLUSTER_ADMIN_USER --password $CLUSTER_ADMIN_PASSWORD

# Helm Install MongoDB Operator and Ops Manager
# Set operator to watch all namespaces
# Deploy Ops Manager to custom namespace
helm install finnova-enterprise-operator . --namespace=finnova-ops-manager --create-namespace --set operator.watchNamespace="*" --atomic

# Get Ops Manager URL
# LoadBalance External Connectivity type used for Ops Manager
oc get services -n finnova-ops-manager


```

# Create MongoDB Cluster using Helm

```
## Access Ops Manager portal
--> Create API Key with Organization Owner role

### Copy Public Key
### Copy Private Key
### Add greenlist entry - IP of Pod where MongoDB Operator is running

# Get Ops Manager URL for use in Mongo cluster creation
oc describe om finnova-ops-manager -n finnova-ops-manager | grep URL | awk '{print $2}'
# Get Ops Manager Project Name
# Get OrgID from Ops Manager Console

# Update automation/mongocluster/values.yaml with the required configuration parameters

# Helm Install MongoDB Cluster
helm install dev-mongo-cluster . --namespace=finnova-dev --create-namespace --atomic

# Port Forward MongoDB Cluster
oc port-forward svc/payment-dev-svc 27017:27017 -n finnova-dev

Access using Compass

```

```
helm install finnova-enterprise-operator . --namespace=mongodb --create-namespace --set operator.watchNamespace="mongodb\,mongodb-dev"

```
