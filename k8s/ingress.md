https://medium.com/@cashisclay/kubernetes-ingress-82aa960f658e
* Ingress gives you a way to route requests to services based on athe host or path, making a single entrypoint into the cluster instead of a loadbalancer per service (costly!)
## Ingress resources
Two main pieces - ingress resource, ingress controller
### Ingress resource
* Define how requests are routed to backing services

    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
        name: my-ingress
    spec:
        rules:
        - host: www.mysite.com
        http:
            paths:
            - backend:
            serviceName: website
            servicePort: 80
            - host: forums.mysite.com
        http:
        paths:
            - path:
            backend:
            serviceName: forums
            servicePort: 80



An ingress resource on its own doesn't do anything, it needs something to listen to the k8s api for new ingress resources and handles requests that match
### Ingress controller
* Any system capable of reverse proxying
    * reverse proxy will take a request and feed it to different endpoints (as opposed to a proxy that changes the source of the request)
* Following example of ingress controller using loadbalancer service, could use nodeport on a bare metal deployment:

    kind: Service
    apiVersion: v1
    metadata:
      name: ingress-nginx
      spec:
type: LoadBalancer
selector:
app: ingress-nginx
ports:
- name: http
port: 80
targetPort: http
- name: https
port: 443
targetPort: https
---
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
name: ingress-nginx
spec:
replicas: 1
template:
metadata:
labels:
app: ingress-nginx
spec:
terminationGracePeriodSeconds: 60
containers:
- image: gcr.io/google_containers/nginx-ingress-controller:0.8.3
name: ingress-nginx
imagePullPolicy: Always
ports:
- name: http
containerPort: 80
protocol: TCP
- name: https
containerPort: 443
protocol: TCP
livenessProbe:
httpGet:
path: /healthz
port: 10254
scheme: HTTP
initialDelaySeconds: 30
timeoutSeconds: 5
env:
- name: POD_NAME
valueFrom:
fieldRef:
fieldPath: metadata.name
- name: POD_NAMESPACE
valueFrom:
fieldRef:
fieldPath: metadata.namespace
args:
- /nginx-ingress-controller
- --default-backend-service=$(POD_NAMESPACE)/nginx-default-backend

* Nginx will monitor ingress resources for routes to serve.  `--default-backend-service` handles 404 response for requests that the controller can't handle

    kind: Service
    apiVersion: v1
    metadata:
      name: nginx-default-backend
      spec:
        ports:
- port: 80
targetPort: http
selector:
app: nginx-default-backend
---
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
name: nginx-default-backend
spec:
replicas: 1
template:
metadata:
labels:
app: nginx-default-backend
spec:
terminationGracePeriodSeconds: 60
containers:
- name: default-http-backend
image: gcr.io/google_containers/defaultbackend:1.0
livenessProbe:
httpGet:
path: /healthz
port: 8080
scheme: HTTP
initialDelaySeconds: 30
timeoutSeconds: 5
resources:
limits:
cpu: 10m
memory: 20Mi
requests:
cpu: 10m
memory: 20Mi
ports:
- name: http
containerPort: 8080
protocol: TCP


Deploy services to be served by ingress:

    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: demo-ingress
      spec:
rules:
- host: mysite.com
http:
paths:
- backend:
serviceName: nginx
servicePort: 80
---
apiVersion: v1
kind: Service
metadata:
name: nginx
spec:
ports:
- port: 80
targetPort: 80
selector:
app: nginx
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
name: nginx
spec:
replicas: 1
template:
metadata:
labels:
app: nginx
spec:
containers:
- name: echoserver
image: nginx
ports:
- containerPort: 80


Get entrypoint to test:

    kubectl get service ingress-nginx -o wide

or

    kubectl describe service ingress-nginx

then curl against either LB DNS or nodeport IP:port

## Labbing based on gke ingress deployment
Even simpler ingress example from Google GKE Example:

Using iptables -t nat -L ` you can see after the nodeport service is created that NAT table entries get created.  Also looking at /var/log/kube-proxy.log will show that kubeproxy is picking up the messages to create the service and nodeport rules.  Gives go lines to go look at.. namely proxier.  Then GCP will create a load balancer to front end the ingress rule.  Different from loadbalancer in that one LB can be used to route to multiple endpoints, vs. a LB per endpoint.

