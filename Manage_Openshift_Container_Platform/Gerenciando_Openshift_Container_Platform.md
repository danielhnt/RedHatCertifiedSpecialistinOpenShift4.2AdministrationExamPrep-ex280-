#Understand and Use the command line and web console

Login to the cluster
oc login

Creating a project
oc new-project my-project

View status of current project
oc status

Create a new app
oc new-app https://github.com/sclorg/cakephp-ex

View pods
oc get pods -o wide
oc get pods -A

View pod logs
oc logs cakephp-ex-l-deploy

List supported APIs
oc api-resources
==============================================================================================
#Create and Delete Project

Create a project
oc new-project <project_name> --description="<description>" --display-name="<display_name>"
oc new-project demotmp --description=<"blablabla"> --displayname="<blabla>"

View and change projects
oc get projects
oc project <project_name>

Delete a Project
oc delete project <project_name> 
oc delete project demotpm

View Projects
oc projects

Change current project
oc project <project_name>

Create new app
oc new-app <inform path project>
oc new-app https://github.com/openshift/ruby-hello-world.git 

View pods
oc get pods

View all projects in namespace
oc get all
====================================================================================================
#Import, Export, and Configure Kubernetes Resources

Export resources to a yamlfile
oc get -o yaml <resource> > <resources>.yaml
oc get -o yaml pods > resources/pod.yaml

Export resources to a json.file
oc get -o json <resource> > <resource>.json
oc get

Create a resource from a yaml file
oc create -f <resource>.yaml

Create a resource from a json file
oc create -f <resource>.json

Replace a resource by filename or stdin
oc replace
oc replace -f resources/pod.yaml

Extract secrets or config maps to disk
oc extract
oc extract secret/builder-token-46nhz --to=resources/

Apply a config to a resource by filename or stdin
oc apply

Copy files between file system and a pod
oc rsync

Verify secrets
oc get secrets

=====================================================================================================
#Examine Resources and Cluster Status
Web Console Dashboard

Details Stautus include ok, warning, in progress e unknown
cluster ID
Provider
Version

Cluster inventory
Number of nodes
Number of podes
Persistent storage volume claims

Cluster Capacity
CPU time
Memory allocation
Storage consumed
Network resources consumed

Cluster Utilization: Capacity of various resources over a specified period of time
Events: Messages related to recent activity in the cluster
Top Consumers: Show how cluster resources are consumed

oc adm top node
oc adm to node <node_name>

=====================================================================================================
#Viewlogs

Print the logs for a resource
for more info, use oc logs --help

Stream the logs of de most recent build config
oc logs -f bc/<buildconfig_name>

Stream the logs of the latest deployment
oc logs -f dc/<deploymentconfig_name>

Stream logs of the first deployment
oc logs --version=1 dc/<deploymentconfig_name>

Stream logs of pods
oc logs -f pod/<pod_name> --tail=5

Return a container snapshot from pod backend
oc logs backend -c <container_name>

A stream of container logs from pod backend
oc logs -f pod/backend -c <container_name>

========================================================================================================
#Monitor Cluster Evens and Alerts

View events in a project
oc get events -n <project_name> 
































