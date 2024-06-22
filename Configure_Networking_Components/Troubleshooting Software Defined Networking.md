#Configure Networking Components
Troubleshoot Software Defined Networking

Cluster Network Operator
View CNO deployment status
oc get -n openshift-network-operator deployment/network-operator

View the state of the CNO
oc get clusteroperator/network

View CNO logs
oc logs --namespace=openshift-network-operator deployment/network-operator

View the CNO configuration
oc describe network.config/cluster