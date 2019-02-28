# Kubernetes Bootcamp

# Security
## Stay updated and use least priv
* GKE will auto upgrade master for you
* GKE can auto update nodes, or can be done manually 
* Should keep nodes upgraded to near latest
* Service account should be least priv.. SA running nodes should only handle those nodes
    * don't use default

## Run container optimized OS - (COS)
* Similar to CoreOS

## Pod Security policy
* use policy that doesn't allow priv pods or priv escalation and forbits running as root or adding the root group

## Network policy
* Shared VPC allows delegation of network resources to project admins, devs don't touch network stuff
* Using private cluster removes internet access both in and out
    * **Is it possible to take this and use veos to secure?**

## Auditing / Monitoring
* Via stackdriver

** Noticed use of namespaces for different apps, should be doing that more **

## RBAC
* In general RBAC is for kubeapi access, not application control
* "Can *subject* *action* *resource*"

## Network Policy
1. Default allow all
2. If there is a policy for a pod(s), then deny all except what is allowed
3. Only allow statements, no deny statements

# Module 2
* Kubernetes is an API that abstracts the underlying environment/infrastructure
* Developer doesn't have to think about where they are running

## Istio
* Separating applications from network functions
* Started as centralized LB edge or middle proxy, for routing decisions and monitoring, but becomes bottleneck so want to distribute
* moved to with client libraries but can get unwieldy as you move to multiple client library versions and multiple languages
    * Move to sidear proxy to be independent
    * All network functions run in client side proxy
    * Service abstracted from proxy so knows nothing about underlying network topology
    * Allows migration of service from one network to another
* Pilot configures data plane/envoy proxies, needs to understand the underlying environment (i.e. k8s api)
* Mixer 
    - telemetry
    ** could we take in mixer metric data into CV?  or graphana? **
    - Policy/quota enforcement
* Citadel
    - auth
    - cert authority/tls/mtls
### Cloud Build vs Spinnaker
* Cloud build is like Jenkins and doesn't understand anything about what's happening, just executes commands
* Spinnaker actually interacts with underlying tools, richer and more advanced

# Q&A
## how to make mult-cluster work
* Different cluster abstraction, hides pod scheduling, list of clusters
* IP based firewall rules coarsely and istio type rules for fine grained access
    * Nothing but these LB ports are allowed in
* Start at a higher level of abstraction and move down -- function -> container/k8s -> VM depending on needs
    * ex. caching doesn't work right with functions, so maybe move to k8s, or custom kernel, need to move down to VM
    * Prototype at an easy place and move down stack as you need more control (or cost)

# Module 3 - monitoring
## Black box vs white box monitoring
* externally visibile behavior as a user would see it
* monitoring based on metrics exposed by the system
    * Highly dependent on infrastructure and components

## Four golden signals 
* Latency
    * LANZ
* Traffic - Where are things going
    * sFlow
* Saturation - often decreased performance before 100%
    * Interface counters
* Errors
    * Interface counters/logs
Istio give 3 of 4 for free

illegal quantity: id={req.body.id}, quantity={req.body.quantity}
