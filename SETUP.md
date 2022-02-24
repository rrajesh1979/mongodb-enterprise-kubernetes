# Setup OpenShift Account

# Setup CLI

# Setup OpenShift Cluster

# Create a Cluster Admin user

# Create Registry Service Account

# Install Ops Manager & MongoDB Cluster

```
# Login to Openshift
oc login $CLUSTER_URL --username $CLUSTER_ADMIN_USER --password $CLUSTER_ADMIN_PASSWORD

# Create namespace for MongoDB
oc create namespace mongodb
# Set context
oc config set-context $(oc config current-context) --namespace=mongodb

# Download Service Registry Account secret yaml / json
# Create file openshift-pull-secret.yaml with the ImagePullSecret

oc apply -f openshift-pull-secret.yaml

# Validate secret was created
oc get secrets

# Create MongoDB Kubernetes Operator / CRDS
oc apply -f crds.yaml
customresourcedefinition.apiextensions.k8s.io/mongodb.mongodb.com created
customresourcedefinition.apiextensions.k8s.io/mongodbmulti.mongodb.com created
customresourcedefinition.apiextensions.k8s.io/mongodbusers.mongodb.com created
customresourcedefinition.apiextensions.k8s.io/opsmanagers.mongodb.com created
clusterrole.rbac.authorization.k8s.io/mongodb-enterprise-operator-mongodb-webhook created

oc apply -f mongodb-enterprise-openshift.yaml
serviceaccount/mongodb-enterprise-operator created
clusterrolebinding.rbac.authorization.k8s.io/mongodb-enterprise-operator-mongodb-webhook-binding created
role.rbac.authorization.k8s.io/mongodb-enterprise-operator created
rolebinding.rbac.authorization.k8s.io/mongodb-enterprise-operator created
serviceaccount/mongodb-enterprise-appdb created
serviceaccount/mongodb-enterprise-database-pods created
serviceaccount/mongodb-enterprise-ops-manager created
role.rbac.authorization.k8s.io/mongodb-enterprise-appdb created
rolebinding.rbac.authorization.k8s.io/mongodb-enterprise-appdb created
deployment.apps/mongodb-enterprise-operator created

# Create cluster admin user to access Ops Manager
oc apply -f ops-manager-admin-secret.yaml
secret/my-ops-manager-admin-secret created

oc get secret ops-manager-admin-secret

oc apply -f ops-manager.yaml
mongodbopsmanager.mongodb.com/ops-manager created

oc get deployments

oc get pods

# Get Ops Manager URL
oc describe om ops-manager | grep URL | awk '{print $2}'

# Port forwarding to access Ops Manager
oc get services

oc port-forward svc/ops-manager-svc-ext 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080
Handling connection for 8080

## Access http://localhost:8080
--> Goto Access Manager
--> Create API Key with Organization Owner role

### Copy Public Key
### Copy Private Key
### Add greenlist entry - OpenShift Cluster to connect to Ops Manager
### Add restricted list of IPs in production

### Continue Step 6 from documentation
oc -n mongodb \
  create secret generic ops-manager-admin-key \
  --from-literal="publicKey=$OM_PUBLICKEY" \
  --from-literal="privateKey=$OM_PRIVATEKEY"
secret/ops-manager-admin-key created

# Get Ops Manager URL
# Get Ops Manager Project Name
# Get OrgID from Ops Manager Console

oc create configmap ops-manager-configmap \
  --from-literal="baseUrl=http://ops-manager-svc.mongodb.svc.cluster.local:8080" \
  --from-literal="orgId=$OM_ORG_ID"
configmap/ops-manager-configmap created

##   --from-literal="projectName=$OM_PROJECT" \

# Create MongoDB Cluster
oc apply -f replica-set.yaml
mongodb.mongodb.com/demo-mongodb-cluster-1 created

oc get pods

# Create MongoDB Password
oc apply -f create-password-1.yaml
secret/user-1-password created

# Create MongoDB User
oc apply -f create-user-1.yaml
mongodbuser.mongodb.com/user-1 created

# Verify MongoDB User created
oc get mongodbuser

# Create Config Map for another Cluster
oc create configmap ops-manager-configmap-2 \
  --from-literal="baseUrl=$BASE_URL" \
  --from-literal="orgId=$OM_ORG_ID"


# Deploy Java / Spring Boot App to access MongoDB
oc run test-mongo-java-connection --image=madhuram2468/test-mongo-java-connection
oc port-forward pod/test-mongo-java-connection 8081:8080

# Access http://localhost:8081
# Host <<>>
# Port 27017
# Username <<>>
# Password <<>>
```

```
helm install finnova-enterprise-operator . --namespace=mongodb --create-namespace --set operator.watchNamespace="mongodb\,mongodb-dev"

```
