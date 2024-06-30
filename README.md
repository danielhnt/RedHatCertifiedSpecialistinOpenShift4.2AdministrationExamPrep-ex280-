# RedHatCertifiedSpecialistinOpenShift4.2AdministrationExamPrep-ex280-

Solution
If you don't already have a Red Hat login, then please register.

For your local CodeReady Containers installation, use sudo for any task requiring permission escalation.

Install OpenShift on CodeReady Containers
Open a terminal session on your local machine, and begin by creating an OpenShift_CRC directory:

mkdir OpenShift_CRC
Change into the OpenShift_CRC directory:

cd OpenShift_CRC
In the browser, log in to your Red Hat account.

Navigate to the download page for CodeReady Containers.

Right-click the CodeReady Containers download link for the platform you're using, and select to copy the link address.

Paste it into a text file, as we'll need it shortly.

Copy the pull secret, and paste it into the same text file.

In the terminal, download CodeReady Containers by running wget with the download link you copied:

wget <DOWNLOAD_LINK>
It could up to 10 minutes or so to finish downloading.

When it's done, extract the file (replace <FILE_NAME> with everything after the last forward slash in the download link):

tar xvf <FILE_NAME>
Find your path:

echo $PATH
Copy the file ending in /crc to your local path (everything up to the : in the previous output):

cp <FILE_NAME> /usr/local/bin/
Start CodeReady Containers setup:

crc setup
Increase the allocated memory:

crc config set memory 12288
Start it:

crc start
At the prompt, paste in the pull secret you copied earlier and press Enter. (It may take up to 10 minutes or so to complete.)

Verify the memory was allocated properly:

crc config view
Start setting up the OpenShift client tools:

crc oc-env
Run the command included in the output.

Log in to the cluster using the oc login -u command included in the output when you started up the cluster. It will look like this (your password value will be different):

oc login -u kubeadmin -p DhjTx-8gIJC-2h2tK-eksGY https://api.crc.testing:6443
If you get an error message, it may be because the cluster isn't up yet. Wait a couple minutes, and then run the command again.

Set Up htpasswd as the Identity Provider and Add Users and Permissions
Set up the htpasswd file:

htpasswd -c -B -b users.htpasswd admin doubletap
Verify the file contents:

cat users.htpasswd
Set up the rest of the users and their passwords:

for users in columbus wichita littlerock tallahassee;
do htpasswd -B -b users.htpasswd $users doubletap;
done
Verify the file contents:

cat users.htpasswd
Check if there's an existing HTPasswd Secret file:

oc get secret -n openshift-config
We should see htpass-secret in the list.

Delete the htpass-secret file:

oc delete secret htpass-secret -n openshift-config
Create the secret from the htpasswd file:

oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config
Download the HTPasswd Custom Resource:

wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/htpasswd_cr.yaml
Open the file:

vim htpasswd_cr.yaml
Under identityProviders, replace my_htpasswd_provider with users.htpassword.

Save and exit the file by pressing Escape followed by :wq.

Apply the changes:

oc apply -f htpasswd_cr.yaml
Log in as the admin user:

oc login -u admin
Enter the password doubletap.

Verify you're logged in as admin:

oc whoami
Log in as the columbus user:

oc login -u columbus
Enter the password doubletap.

Verify you're logged in as columbus:

oc whoami
Log in as the wichita user:

oc login -u wichita
Enter the password doubletap.

Verify you're logged in as wichita:

oc whoami
Log in as the littlerock user:

oc login -u littlerock
Enter the password doubletap.

Verify you're logged in as littlerock:

oc whoami
Log in as the tallahassee user:

oc login -u tallahassee
Enter the password doubletap.

Verify you're logged in as tallahassee:

oc whoami
Log in as kubeadmin:

oc login -u kubeadmin
Use the password you used earlier as part of the command to log in as kubeadmin.

Create the ZLand project:

oc new-project zland
Give Columbus admin permissions to the ZLand project:

oc adm policy add-role-to-user admin columbus -n zland
Give Wichita and Little Rock edit permissions to the ZLand project:

oc adm policy add-role-to-user edit wichita -n zland
oc adm policy add-role-to-user edit littlerock -n zland
Give Tallahassee basic user permissions to the ZLand project:

oc adm policy add-role-to-user basic-user tallahassee -n zland
Give the admin user cluster admin permissions:

oc adm policy add-cluster-role-to-user cluster-admin admin
Log in as admin:

oc login -u admin
Remove the kubeadmin user from the cluster:

oc delete secrets kubeadmin -n kube-system
Role-Based Access and Groups
Create a project called twinkies:

oc new-project twinkies
Create a group called yum:

oc adm groups new yum
Add columbus, wichita, and littlerock to the yum group.

oc adm groups add-users yum columbus wichita littlerock
Grant admin access to the yum group for the project twinkies:

oc adm policy add-role-to-group admin yum -n twinkies
Create a custom gettwinkies role:

oc create role gettwinkies --verb=get --resource=pod -n twinkies
Assign the gettwinkies role to tallahassee, allowing the user to get pod information from the twinkies project:

oc adm policy add-role-to-user gettwinkies tallahassee -n twinkies
Verify it worked:

oc describe rolebinding.rbac -n twinkies
Quotas and Resource Limits
Download the quota and resource limit templates:

wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/quota.yaml
wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/resource_limits.yaml
Open the quota.yaml file:

vim quota.yaml
Delete the cpu line.

Set the following values:

pods: "3"
memory: "2Gi"
replicationcontrollers: "2"
services: "8"
Save and exit the file by pressing Escape followed by :wq.

Create quota and apply it to the ZLand project:

oc create -f quota.yaml -n zland
Verify it was created:

oc get quota -n zland
Get more information:

oc describe quota core-object-counts -n zland
Open resource_limits.yaml:

vim resource_limits.yaml
Set the following values:

pods: "4"
requests.cpu: "1"
requests.memory: "1Gi"
requests.ephemeral-storage: "2Gi"
limits.cpu: "4"
limits.memory: "4Gi"
limits.ephemeral-storage: "8Gi"
Save and exit the file by pressing Escape followed by :wq.

Create resource limits and apply it to the twinkies project:

oc create -f resource_limits.yaml -n twinkies
Verify they were created:

oc get quota -n twinkies
Get more information:

oc describe quota compute-resources -n twinkies
Application Creation and Management
Create new projects:

oc new-project test-app5
oc new-project test-app4
oc new-project test-app3
oc new-project test-app2
oc new-project test-app1
Create an application named cake in project test-app1:

oc new-app https://github.com/sclorg/cakephp-ex --name=cake
Make sure the application is up:

oc get pods
If you don't see a status of Completed yet, wait a minute or so and then run the command again. It may take five minutes or so to complete.

Make sure it is accessible to the outside world:

oc expose service cake
Verify the route is now accessible:

oc get routes
Change to test-app2:

oc project test-app2
Create an application named cake2:

oc new-app https://github.com/sclorg/cakephp-ex --name=cake2
Make sure the application is up:

oc get pods
If you don't see a status of Completed yet, wait a minute or so and then run the command again. It may take five minutes or so to complete.

Create the twinkiesforall route for it:

oc expose service cake2 --name=twinkiesforall
Verify the route was created:

oc get routes
Change to test-app3:

oc project test-app3
Create an application named cake3:

oc new-app https://github.com/sclorg/cakephp-ex --name=cake3
Make sure the application is up:

oc get pods
If you don't see a status of Completed yet, wait a minute or so and then run the command again. It may take five minutes or so to complete.

Get the self-signed cert for creating a secure route:

wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/test-app3.crt
wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/test-app3.key
Create the secure route:

oc create route edge --service=cake3 --cert=test-app3.crt --key=test-app3.key --hostname=mytwinkie-test-app3.apps-crc.testing
Verify the route was created:

oc get routes
Change to test-app4:

oc project test-app4
Create an application named cake4:

oc new-app https://github.com/sclorg/cakephp-ex --name=cake4
Make sure the application is up:

oc get pods
If you don't see a status of Completed yet, wait a minute or so and then run the command again. It may take five minutes or so to complete.

Download the secret.yaml file:

wget https://raw.githubusercontent.com/linuxacademy/content-openshift-2020/master/secret.yaml
Open secret.yaml:

vim secret.yaml
In the metadata section:

name: dont-tell
namespace: test-app4
In the data section, enter a username and password of your choice (it can just be random characters).

In the stringData section:

hostname: cake4-test-app1.apps-crc.testing
Save and exit the file by pressing Escape followed by :wq.

Create the secret:

oc create -f secret.yaml
Verify it was created:

oc get secrets
Change to test-app5:

oc project test-app5
Create an application named cake5:

oc new-app https://github.com/sclorg/cakephp-ex --name=cake5
Make sure the application is up:

oc get pods
If you don't see a status of Completed yet, wait a minute or so and then run the command again. It may take five minutes or so to complete.

Create a service account called madison:

oc create sa madison
Create an application that can be edited by the madison service account in the test-app5 project:

oc adm policy add-role-to-user edit system:serviceaccount:test-app5:madison
Verify the service account exists:

oc get sa
Set an autoscaler for min of 1 pod and a max of 3 pods based of 75% CPU utilization for the application in the test-app5 project:

oc autoscale deployment.app/cake5 --min 1 --max 3 --cpu-percent=75
Verify the autoscaler was created:

oc get hpa
Change to test-app2:

oc project test-app2
Manually scale the application in the test-app2 project to 2 pods:

oc scale --replicas=2 deployment.app/cake2
Verify it scaled:

oc get pods
Conclusion
Congratulations on successfully completing this hands-on lab!