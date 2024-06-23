Configure Pod Scheduling
Example Resource Quota Definitions

core-object-counts.yaml

kind: ResourceQuota
apiVersion: v1
metadata:
  name: core-object-counts
spec:
  hard:
    configmap: "10"
    persistentvolumeclaims: "4"
    replicationcontrollers: "20"
    secrets: "10"
    services: "10"
    services.loadbalancer: "2"

compute-resources-long-running.yaml

kind: ResourceQuota
apiVersion: v1
metadata: 
  name: compute-resources-long-running
spec:
  hard:
    pods: "4"
    limits.cpu: "4"
    limits.memory: "2Gi"
    limits.ephemeral-storage: "4Gi"
  scope:
  - Not terminating

Create a quota
oc creat -f <file> -n <project_name>

Create object count quotas
oc create quota <name> \ --hard=count/<resource>.<group>=<quota>,count/<resource>.<group>=<quota>

View a list of quotas in a project
oc get quota -n <project_name>

Describe the quota of interest
oc describe quota <quota_name> -n <project_name>

Example
vim compute-resources.yaml

oc create -f compute-resources.yaml -n demo
oc get quota -n demo
oc describe quota compute-resources -n demo


#################################################################################################

Scale Applications to meet Increased Demand
Application Scaling

Manual Scaling
oc scale --replicas=<number> dc/<deployment_name>

Scaling example
oc scale --replicas=2 dc/ruby-hello-world

Auto scaling
oc autoscale dc/<deployment_name> -min <number> --max <number> --cpu-percent=<percentage>

Scaling example
oc autoscale dc/ruby-hello-world --min 1 --max 5 --cpu-percent=75

Example

oc project demo
oc get pods
oc get deploymentconfig
oc scale --replicas=3 dc/ruby-hello-world
oc get deploymentconfig
oc scale --replicas=2 dc/ruby-hello-world
oc get deploymentconfig
oc autoscale dc/ruby-hello-world --min 1 --max 4 --cpu-percent=90
oc get hpa
oc edit hpa ruby-hello-world

vim cpu-autoscale.yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: cpu-autoscale
  namespace: demo
spec:
  macReplicas: 4
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps.openshift.io/v1
    kind: DeploymentConfig
    name: ruby-hello-world
  targeCPUUtilizationPercentage: 90
########################################################################################################
Creating Scheduler Policy

Create ConfigMap
oc create configmap -n openshift-config --from-file=policy.cfg <configmap>

Add ConfigMap to the Scheduler Operator CR
oc patch Scheduler cluster \ --type='merge' -p '{"spec":{"policy":{"name":<configmap>}}}'

Verify it is configured
oc logs <scheduler_pod> | grep predicates

Examples

vim policy.cfg
oc create configmap -n openshift-config --from-file=policy.cfg
oc patch Scheduler cluster --type='merge' -p '{"spec":{"policy":{"name":"pod-scheduler-policy"}}}'
oc get pods -n openshift-kube-scheduler
oc logs <name> -n openshift-kube-scheduler | grep predicates
oc get configmap -n openshift-config
oc edit configmap pod-shceduler-policy -n openshift-config


