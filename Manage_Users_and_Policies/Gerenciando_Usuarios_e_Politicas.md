#Configure the HTPassword Identity Provider for Authentication

Create or update htpasswd file
htpasswd -c -B -b </path_to/htpasswd_file> <user_name> <password>

Append to or update credentials
htpasswd -b </path_to/htpasswd_file> <username> <password>

Create the HTPasswd secret
oc create secret generic htpass-secret --from-file=htpasswd=</path_to/htpasswd_file> -n openshift-config

Adding th HTPasswdIdentity Provider Resource (CR)
Ex:
"apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
- name: my_htpasswd_provider
  mappingMethod: claim
  type: HTPasswd
  htpasswd:
    fileData:
      name: htpass-secret"

Apply the defined CR
oc apply -f <path_to/CR>

Test by logging in as a user in the file
oc login -u <user_name>

Confirm the user is loggged in successfully
oc whoami

Create a secret from file
oc create secret generic htpass-secret --from-file=users.htpasswd -n openshift-config 

sudo vim htpasswd.yaml
Ex:
"apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
- name: users.htpasswd
  mappingMethod: claim
  type: HTPasswd
  htpasswd:
    fileData:
      name: htpass-secret"

oc apply -f htpasswd.yaml

=============================================================================================
#Create and delete users
Adding a User to HTPasswd secretname
oc get secret -n openshift-config

Extract the secret to a HTPasswd file
oc extract secret/HTPASSWD_SECRET --to - -n oemshift-config > htpasswd

Add user(s) to the HTPasswd file
htpasswd -B -b htpasswd <user_name> <password>

Replace secret with new secret
oc create secret generic HTPASSWD_SECRET --from-file=htpasswd --dry-run -o yaml openshift-config | 
oc replace -f -

Check identity
oc get identity

Check access
oc login -u <user_name>
oc get users
==============================================================================================
#Deleting Users from HTPasswd Identity Provider
Confirm HTPasswd secret name
oc get secret -n openshift-config

Extract the secret to a HTPasswd file
oc extract secret/HTPASSWD_SECRET --to - -n openshift-config > htpasswd

Edit HTPasswd file
vim htppasswd

Replace secret with new secret
oc create secret generic HTPASSWD_SECRET --from-file=htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -
oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -


Remove identity
oc get identity
oc delete identity/HTPASSWD_PROVIDER:<username>

Remove user
oc get users
oc delete user/<username>

Adding users passwords
for users in wichita madison tallahassee; do htpasswd -B -b users.htpasswd $users password1;

Verify users
cat users.htpasswd

Extract password
oc extract secret/htpass-secret --to - -n openshift-config > users.htpasswd 
=================================================================================================
#Modify users password in the HTPasswd Identity Provider

Confirm htppasswd secret name
oc get secret -n openshift-config

Extract the secret to a HTPasswd file
oc extract secret/HTPASSWD_SECRET --to - -n openshift-config > htpasswd

Modify the users password in the htpasswd file
htpasswd -B -b htpasswd <username> <new_password>

Replace secret with new secret
oc create secret generic HTPASSWD_SECRET --from-file=htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -

Check access
oc login -u <username>

==================================================================================================
#Modify Users and Groups Permissions
Default Cluster Roles

admin
Has rights to view any resourcein the project and modify any resource in the project except for quota.

basic-user
Can get basic information about projects and users

cluster-admin
Super-user that can perform any action in any project. When bound to a specific project, that user has full control of quota and every action on every resource
in project

cluster-status
Can get basic cluster status information

edit
Can modify most objects in a project but does not have the power to view or modify roles or bindings

self-provisioner
Can create their own projects

view
Cant make any modifications, but can see most objects in a project cant view or modify roles or bindings

#Adding Roles

Add a role to a user
oc adm policy add-role-to-user <role> <user> -n <project>

Add role to a group
oc adm policy add-role-to-group <role> <group> -n <project>

Verify by viewing the local rolebinding
oc describe rolebinding.rbac -n <project>

Creating a cluster admin
oc adm policy add-cluster-role-to-user cluster-admin <user>

Remove kubeadmin user
oc delete secrets kubeadmin -n kube-system

=========================================================================================================
Create and Manage group

Create a new group
oc adm groups new <group>

Create a new group with one user
oc adm groups new <group> <user>

Add users to an existing group
oc adm groups add-users <group> <user1> <user2> <user3>

Remove users from an existing group
oc adm groups remove-users <group> <user1> <user2>

Default virtual Groups

system:authenticated
system:authenticated.oauth
system:unauthenticated

========================================================================================================