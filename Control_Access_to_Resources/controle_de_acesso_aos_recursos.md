
#Define Role-Based Access Controls
Create a local role
oc create role <name> --verb=<verb> --resource=<resource> -n <project>

Example
oc create role podview --verb=get --resource=pod -n blue

Create a cluster role
oc create clusterrole <name> --verb=<verb> --resource=<resource>

Example
oc create clusterrole podviewonly --verb=get --resource=pod

oc adm policy add-role-to-user podview wichita --role-namespace=demo3 -n demo3

oc create clusterrole podviewonly --verb=get --resource=pod

oc create user tallahassee

oc adm policy add-cluster-role-to-user podviewonly tallahassee

oc describe rolebinding.rbac -n demo3

os describle clusterolebindings.rbac podviewonly 

#####################################################################################
#Apply permissions to users

Local role binding commands

Indicates which users can perform an action on a resource
oc adm policy who-can <verb> <resource>

Binds a specified role to specified users in the current project
oc adm policy add-role-to-user <role> <user>

Removes a given role from specified users in the current project
oc adm policy remove-role-from-user <role> <user>

Removes specified users and all their roles in the current project
oc adm policy remove-user <user>

Binds a given role to specified groups in the current project
oc adm policy add-role-to-group <role> <group>

Removes a given role from specified groups in the current project
oc adm policy remove-role-from-group <role> <group>

Removes specified groups and all their roles in the current project
oc adm policy remove-group <group>

#Cluster Role Binding Commands

Binds a given role to specified users for all projects in the cluster
oc adm policy add-cluster-role-to-user <role> <user>

Removes a given role from specified users for all projects in the cluster
oc adm policy remove-cluster-role-from-user <role> <user>

Binds a given role to specified groups for all projects in the cluster
oc adm policy remove-cluster-role-from-group <role> <group>

Example
oc project demo

oc adm policy who-can get pod

oc adm policy who-can create pod

#############################################################################################
#Create and apply secrets to manage sensitive information
Secrets 
Types of secrets

kubernetes.io/service-account-token
kubernetes.io/basic-auth
kubernetes.io/ssh-auth
kubernetes.io/tls
Opaque

Create a secret object on the master

vim secret.yaml

Example secret yaml file
apiVersion:v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: dawdasewe
  password: wqawedwda

Create a secret object
oc create -f secret.yaml

Example yaml of a pod populating environment variables
apiVersion: v1
kind: Pod
metadata:
  name: secret-example-pod
spec:
  containers:
    - name: secret-test-container
    image: busybox
    command: [ "/bin/sh", "-c","export" ]
    env:
       - name: TEST_SECRET_USERNAME_ENV_VAR
         valueFrom:
           secretKeyRef:
             name: mysecret
             key: username
  restartPolicy: Never

Example yaml of build config populating environment variables
apiVersion: v1
kind: BuildConfig
metadata:
  name: secret-example-bc
spec:
  strategy:
    sourceStrategy:
      env:
      - name: TEST_SECRET_USERNAME_ENV_VAR
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: username

Example

echo -n 'columbus' | base64
echo -n 'password2' | base64

vim secret.yaml 

apiVersion: v1
kind: Secret
metadata:
  name: test-secret
  namespace: demo3
type: Opaque
data:
  username: Y29sdW1idXM=
  password: cGFzc3dvcmQy

oc create -f secret.yaml

oc get secrets -n demo3
########################################################################################
#Create Service Accounts and Apply Permissions Using Security Context Constrains

View service accounts
oc get sa

Create a service account
oc create sa <service_account_name>

View details about the service account
oc describe sa/<service_account_name>

Grant a role to the service account for current project
oc adm policy add-role-to-user <role> system:serviceaccount:<project>:<service_account_name>

Example SCC YAML file
kind: SecurityContextConstraints
apiVersion: v1
metadata:
  name: acc-admin
allowPrivilegedContainer: true
runAsUser:
  type: RunAsAny
seLinuxContext:
  type: RunAsAny
fsGroup:
  type: RunAsAny
users:
- <service_account_name>

Create a security context constraint
oc create -f scc_admin.yaml

oc describe scc scc-admin

##############################################################################################



