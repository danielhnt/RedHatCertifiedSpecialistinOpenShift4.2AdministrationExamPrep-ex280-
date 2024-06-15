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

Export resources to a json.file
oc get -o json <resource> > <resource>.json

Create a resource from a yaml file
oc create -f <resource>.yaml

Create a resource from a json file
oc create -f <resource>.json

Replace a resource by filename or stdin
oc replace

Extract secrets or config maps to disk
oc extract

Apply a config to a resource by filename or stdin
oc apply

Copy files between file system and a pod
oc rsync

































