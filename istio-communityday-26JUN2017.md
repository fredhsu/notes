# Intro to Istio
26-JUN-2017
Sven 

Problem statement
IT's shift to a modern distributed architecture has left enterprises unable to monitor, manage, or secure their services in a consistent way.
Moving to MS - how to do you manage it all in a consistent way, monitoring, etc..
Istio - an open project ot connect, manage, monitor and secure microsvcs

## what is it (service mesh)
* complete framework for connecting..
* network of services not bytes - service a -> b not IP addr to ip addr
* secure and monitor traffic for msvcs *and* legacy
* Built from community at the beginning: Google, IBM, Lyft, and others
* Multi-env. multi-platform but k8s first

## What is a service mesh
* A network for services, not bytes
* Visibilty
* resilency & efficiency
* traffic control
    * route 10% of traffic to a new version
    * internal users get canary version
    * policy routing lang
* security
* policy enforcement

## Weaving hte mesh
* sidecar and ingress proxies put in front of every service
* All traffic comes through the proxy for policy enforcement

## Putting it all together
* Sidecars talk to *Mixer* to check on policy and report on results
* Pilot configures proxies
* Istio-Auth - TLS certs to Envoy

## Roadmap
* Expanding to Raw VMs, CF, mesos
* hybrid
* core - stability, performance, robustness
* API machinery - mixer api, config api, galley
* Integrations - Monitoring, logging, tracing, ID
* Security - service ID, inter cluster, inter-environ, VMs/pods
* API mgmt - key validation, api gateway use cases

# Istio demo and overview
26-JUN-2017
Kesley HIghtower
* Most people just want a PaaS
* But it needs to be built internally
* i.e. PaaS in a box
* Ingress uses nginx/haproxy but those don't have great APIs
* Instrumentation and visibilty are hard / ugly to implement

* Istio doesn't try to redo things, but integrates so you don't have to
* Its like a big Envoy config builder - must learn Envoy

* Manage traffic flows between svcs
* Aggregating telemetry data
* Enforcing access policies

## Istio components
* Control plane (pilot, mixer, auth)
* Adapators for backend infra
* Envoy side car

```
kubectl get notes
kubectl get pods
kubectl get pods -n kube-system (namespace for system)
istioctl kube-inject -f kubernetes/deployments/frontend-v1.yaml

kubectl apply -f <(istioctl kube-inject-f kubernetes/deployments/frontend-v1.yaml)
kubectl apply -f <(istioctl kube-inject-f kubernetes/deployments/foo.yaml)
kubectl apply -f <(istioctl kube-inject-f kubernetes/deployments/bar.yaml)
kubectl get pods
istioctl get route-rules
istioctl delete -f istio/bar.yaml
istioctl create -f istio/bar.yaml

```
### Gotchas
* Deal with headers
    * Need to capture headers and propopgate down to request for tracing to work (zipkin)
    * Work needs to be done to pass headers down 
* make sure healthchecks are ok
    * split out grpc, health, http ports 
* kube frontend won't work, need to move to ingress(envoy)
    * result of proxy on worker node
### Service Graph
* great API to visualize how stack is deployed!
* API for this as well /graph


## Codewalk
Extensibility across all points
### Each box is a repo on github
github.com/istio
istio.io
Start in API
github.com/istio/api
* Api envoy - mixer - servie def
* schemas - protobufs
* guides on web on how to write yaml from protobuf

### Mixer
* where to integrate and extend
* single point of policy enforcement and telemetry
* aspect - describes what you can do
    * when request goes through envoy, envoy asks for permission
    * when request is done, it gives attributes back to mixer
* pick interface you want to extend in golang
* clients written in c++


### Pilot
* How to configure the envoys
* Make envoy a transparent proxy
* Uses iptables rules for now
* When using istio, nothing should change
* Features of envoy get configured / exposed
* Environmental adaptation - info from k8s feed in here

## Galley
* Will hold system state and APIs to set and get config

### Auth
* Implementation of CA, runs independent of the rest

### Envoy
* Filters

### Other
* Init container does iptables management

# Calico for Istio
Saurabh Mohan
saurabh@tigera.io
* k8s has a network policy component that would be lower level than istio
* L3 policy enforcement across hybrid environment (VMs, BM, k8s, etc..)
* Define policy in Istio -> k8s -> enforced in Calico

