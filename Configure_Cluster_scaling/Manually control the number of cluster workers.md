Machineset Scaling

View machinesets
oc get machinesets -n openshift-machine-api

Manualy scale machineset option 1
oc scale --replicas=2 machineset <machineset> -n openshifit-machine-api

Manually scale machineset option 2
oc edit machineset <machineset> -n openshift-machine-api

Example
oc get machinesets -n openshift-machine-api
oc scale --replicas=2 machineset <name> -n openshift-machine-api
oc edit machineset <name> -n openshift-machine-api
oc get machinesets -n openshift-machine-api
oc get nodes

#######################################################################################
Automatically Scale the Number of Clusters Workers

Machine Autoscaler
apiVersion: "autoscaling.openshift.io/v1beta1"
kind: "MachineAutoscaler"
metadata:
  name: "worker-us-east-1a"
  namespace: "openshift-machine-api"
spec:
  minReplicas: 1
  maxReplicas: 4
  scaleTargetRef:
    apiVersion: machine.openshift.io/v1beta1
    kind: MachineSet
    name: worker-us-east-1a

Create both autoscalers
oc create -f <filename>.yaml

Example
oc get machinesets -n openshift-machine-api

vim MachineAutoscaler.yaml

apiVersion: "autoscaling.openshift.io/v1beta1"
kind: "MachineAutoscaler"
metadata: 
  name: "worker-autoscaler"
  namespace: "openshift-machine-api"
spec:
  minReplicas: 1
  maxReplicas: 4
  scaleTargetRef:
    apiVersion: machine.openshift.io/v1beta1
    kind: MachineSet
    name: <name>

oc create -f MachineAutoscaler.yaml
oc get machineautoscalers -n openshift-machine-api
oc edit machineautoscaler worker-autoscaler -n openshift-machine-api
os describe machineautoscaler worker-autoscaler -n openshift-machine-api







