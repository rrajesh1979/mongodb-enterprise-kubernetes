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
NAME                       TYPE                                  DATA   AGE
builder-dockercfg-vm2gg    kubernetes.io/dockercfg               1      11m
builder-token-bkb4r        kubernetes.io/service-account-token   4      11m
builder-token-k5gj7        kubernetes.io/service-account-token   4      11m
default-dockercfg-7lddt    kubernetes.io/dockercfg               1      11m
default-token-pb742        kubernetes.io/service-account-token   4      11m
default-token-qg95m        kubernetes.io/service-account-token   4      11m
deployer-dockercfg-7gv4r   kubernetes.io/dockercfg               1      11m
deployer-token-gv55l       kubernetes.io/service-account-token   4      11m
deployer-token-wk9kq       kubernetes.io/service-account-token   4      11m
openshift-pull-secret      kubernetes.io/dockerconfigjson        1      13s

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
NAME                       TYPE     DATA   AGE
ops-manager-admin-secret   Opaque   4      4s

oc apply -f ops-manager.yaml
mongodbopsmanager.mongodb.com/ops-manager created

oc get deployments
NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
mongodb-enterprise-operator   1/1     1            1           11m

oc get pods
NAME                                          READY   STATUS     RESTARTS   AGE
mongodb-enterprise-operator-cdb5685ff-kgb54   1/1     Running    0          21m
ops-manager-0                                 1/1     Running    0          7m38s
ops-manager-backup-daemon-0                   0/1     Init:0/1   0          19s
ops-manager-db-0                              3/3     Running    0          73s
ops-manager-db-1                              3/3     Running    0          105s
ops-manager-db-2                              3/3     Running    0          2m18s

# Get Ops Manager URL
oc describe om ops-manager | grep URL | awk '{print $2}'

# Port forwarding to access Ops Manager
oc get services
NAME                            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                          AGE
operator-webhook                ClusterIP   172.30.65.237   <none>        443/TCP                          22m
ops-manager-backup-daemon-svc   ClusterIP   None            <none>        8443/TCP                         88s
ops-manager-db-svc              ClusterIP   None            <none>        27017/TCP                        11m
ops-manager-svc                 ClusterIP   None            <none>        8080/TCP                         8m47s
ops-manager-svc-ext             NodePort    172.30.59.60    <none>        8080:32551/TCP,25999:30993/TCP   8m47s

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
  --from-literal="projectName=$OM_PROJECT" \
  --from-literal="orgId=$OM_ORG_ID"
configmap/ops-manager-configmap created

# Create MongoDB Cluster
oc apply -f replica-set.yaml
mongodb.mongodb.com/demo-mongodb-cluster-1 created

oc get pods
NAME                                          READY   STATUS    RESTARTS   AGE
demo-mongodb-cluster-1-0                      1/1     Running   0          2m3s
demo-mongodb-cluster-1-1                      1/1     Running   0          108s
demo-mongodb-cluster-1-2                      1/1     Running   0          83s
mongodb-enterprise-operator-cdb5685ff-kgb54   1/1     Running   0          45m
ops-manager-0                                 1/1     Running   0          30m
ops-manager-backup-daemon-0                   1/1     Running   0          23m
ops-manager-db-0                              3/3     Running   0          24m
ops-manager-db-1                              3/3     Running   0          25m
ops-manager-db-2                              3/3     Running   0          25m

# Create MongoDB Password
oc apply -f create-password-1.yaml
secret/user-1-password created

# Create MongoDB User
oc apply -f create-user-1.yaml
mongodbuser.mongodb.com/user-1 created

# Verify MongoDB User created
oc get mongodbuser
NAME     PHASE     AGE
user-1   Updated   15s

# Create Config Map for another Cluster
oc create configmap ops-manager-configmap-2 \
  --from-literal="baseUrl=http://ops-manager-svc.mongodb.svc.cluster.local:8080" \
  --from-literal="orgId=620fff3ab5f3c21be2e8dc27"


# Deploy Java / Spring Boot App to access MongoDB
oc run test-mongo-java-connection --image=madhuram2468/test-mongo-java-connection
oc port-forward pod/test-mongo-java-connection 8081:8080

# Access http://localhost:8081
# Host demo-mongodb-cluster-2-1.demo-mongodb-cluster-2-svc.mongodb.svc.cluster.local
# Port 27017
# Username user-1
# Password Passw0rd-1
```
