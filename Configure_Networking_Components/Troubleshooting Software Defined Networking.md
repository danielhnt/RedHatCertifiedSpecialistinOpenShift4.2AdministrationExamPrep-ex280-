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

Debugging network issues

Check the DNS Operator
oc get -n openshift-dns-operator deployment/dns-operator
oc get clusteroperator/dns

DNS Operator Status
oc describle clusteroperator/dns

DNS Operator logs
oc logs -n openshift-dns-operator deployment/dns-operator

Debugging Routes
oc get endpoints -n <project>

oc get pods -n <project> --template='{{range.items}}HostIP:{{status.hostIP}} PodIP: {{.status.podIP}}{{"\n"}}{{end}}'

oc get route -n <project>

Debugging Services
oc get services -n <project_name>
oc get endpoints -n <project_name>

Example

oc get -n openshift-network-operator deployment/network-operator
oc get clusteroperator/network
oc describe network.config/cluster
oc logs -n openshift-network-operator deployment/network-operator --tail 10
oc get clusteroperator/dns

oc describe clusteroperator/dns
oc get endpoints -n demo3
oc get pods -n demo3 -o wide
oc get route -n demo3

###################################################################################################################
Create and Edit external routes
External routes (Unsecured)

oc expose service <service_name>

Create a route with custom label and route name
oc expose service <service_name> -l name=<label> --name<route_name>

Create a route with a specified port and protocol
oc expose service <service_name> --port=<port> --protocol="<protocol>"

Create a route with a specified path
oc expose service <service_name> --path=<path>

Edit routes

Add a timeout to the route
oc annotate route <route>  --overwrite haproxy.router.openshift.io/timeout=<timeout><time_unit>

Add a desired cookie name
oc annotate route <route> router.openshift.io/<cookie_name>="-cookie_annotation"

Restrict access by whitelisting an IP
oc annotate route <route> haproxy.router.openshift.io/ip_whitelist="<ip1 ip2 ip3>"

Enable rate limiting
oc annotate route <route> haproxy.router.openshift.io/rate-limit-connections=true

Example
oc project demo
oc get services
oc expose service ruby-hello-world -l name=helloworld

oc annotate route helloworld --overwrite haproxy.router.openshift.io/timeout=5s

oc annotate route helloworld router.openshift.io/hellhoworld="-helloworld_annotation"
oc annotate route helloworld haproxy.router.openshift.io/rate-limit-connections=true
oc describe route helloworld

#####################################################################################################
Control cluster network ingress control

Ingress Resource
vim example-ingress.yaml

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: example
  namespace: <project>
spec:
  rules:
    - host: example
      http:
      paths:
        - path: /testpath
          backend:
            serviceName: test
            servicePort: 80

Apply Ingress Controller file

oc apply -f router-internal.yaml

Example

oc get services -n demo3
oc expose service django-psql-example -n demo3
oc get routes -n demo3

vim demo3-ingress.yaml
apiVersion: networking.k8s.io/v1beta1
kind Ingress
metadata:
  name: demo3-ingress
  namespace: demo3
spec:
  rules:
  - host: django-psql-example-demo3.apps.ocplab.openshift.labdemodomain.com
    http:
      paths:
      - path: /demo
        backend:
          serviceName: postgresql
          servicePort: 5432
oc apply -f demo3-ingress.yaml
oc get ingress -n demo3
oc get routes -n demo3
oc describe ingress/demo3-ingress -n demo3

###########################################################################################
Create a Self Signed Certificate

OpenSSL
OpenSSL is a full-featured toolkit for TLS and SSL. It is also a general-purpose cryptografy library. It can be used to create self-signed certificates to be used when creating secured routes. Here is an axample of how it can be used.

Create Sel-signed certificate
openssl req -x509 -newkey rsa:4096 -nodes -keyout cert.key -out cert.crt

Remove a passphrase from a key file
openssl rsa -in <password_protected_key_file>.key -out <key_file>.key

##############################################################################################
Secure Routes using TLS Certificate

Secure Routes
oc create route edge --service=frontend --cert=tls.crt --key=tls.key --hostname=www.example.com

Examining route
apiVersion: v1
kind: Route
metadata:
  name: frontend
spec:
  host: www.example.com
  to:
    kind: Service
    name: frontend
  tls:
    termination: edge
    key: |-
      -----BEGIN PRIVATE KEY-----
      [...]
      -----END PRIVATE KEY-----
    certificate: |-
      -----BEGIN CERTIFICATE-----
      [...]
      -----END CERTIFICATE-----

Example
oc project demo
oc delete route helloworld
oc get services
oc create route edge helloworld --service=ruby-hello=world --cert=helloworld.crt \ > --key=helloworld.key --hostname=helloworld-demo.apps.ocplab.openshift.labdemodomain.com
oc get routes
oc describe route helloworld
oc edit route helloworld


