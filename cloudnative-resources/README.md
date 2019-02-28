# Technology Bookmarks

# Linux
## iptables/netfilter/ipset
* https://goyalankit.com/blog/iptables
* https://www.booleanworld.com/depth-guide-iptables-linux-firewall/amp/
iptables is the command line interface to netfilter (kernel firewall)
structures are:
    * tables - processes packet
        * filter - should the packet be allowed to dest
        * mangle -  alter packet headers
        * nat - address translation
        * raw - stateful firewall - works with packets before kernel starts tracking state

    * chains - attached to tables inspect packets and associate with target
        * PREROUTING - applies when packet first arrives on nic, present in nat, mangle, raw
        * INPUT - applys before given to local proces - mangle, filter
        * OUTPUT - applies after processing - raw, mangle, nat, filter
        * FORWARD - applies to packets routing through - mangle, filter
        * POSTROUTING - applies as packet leaves - nat, mangle
    * targets


* https://www.linuxjournal.com/article/4815

The iptables command line is broken down into as many as six parts. The first part is the iptables command, which will not be discussed further. The second part is the table specification, and the chain name is the third part. The fourth part is the rule specification, which is the part of the command to match against the IP or ICMP header, but also could be a rule number in some incantations. The fifth part is the target, and the sixth part is the target option. The general command line, then, looks like

`iptables [-t table] -ACDI CHAIN rule-specification å -j TARGET [target option]`
Netfilter has three tables you need to concern yourself with: filter, nat and mangle.
Each table has certain chains available to it. User-created chains will belong to one and only one table. You will see that some built-in chains belong to more than one table, but this is only true for built-in chains. You cannot mix chains from other tables within user-created chains.

The filter table is the basic packet-filter table and has the built-in chains INPUT, FORWARD and OUTPUT. Rules added to user-created chains created in the filter table can only contain targets valid in the INPUT, FORWARD or OUTPUT chains. Packets traversing the filter table will pass through one and only one of INPUT, FORWARD or OUTPUT. The INPUT chain will only be traversed if the packet's destination is the local system. The FORWARD chain will only be traversed if the packet is passing through the local system and bound for another system. And the OUTPUT chain is traversed only by packets originating on the local system with an external destination. Only one chain will be traversed by any given packet. 

The nat table performs network address translation. Built-in chains for nat are PREROUTING, POSTROUTING and OUTPUT. Each chain permits a particular target. The PREROUTING accepts the DNAT target, and the remaining chains accept the SNAT target. 

The mangle table is used to change (mangle) information other than the IP address in the header. It can be used to mark the packets, change type of service (TOS) or change time-to-live (ttl) information.

Netfilter has four built-in targets: ACCEPT, DROP, QUEUE and RETURN. All other targets used are based on modules that load as a target. These include REJECT, LOG, MARK, MASQUERADE, MIRROR, REDIRECT and TCPMSS. Terminal targets, such as ACCEPT, DROP, REJECT, MASQUERADE, MIRROR and REDIRECT, terminate a chain. A LOG target does not terminate a chain. LOG also neither ACCEPTS, REJECTS nor DROPS a packet, so the chain continues to be traversed. 

$IPT -t filter -N tcprules
$IPT -t filter -A tcprules -i ppp+ -m state --state ESTABLISHED,RELATED -j ACCEPT
$IPT -t filter -A tcprules -i ! ppp+ -m state --state NEW -j ACCEPT
$IPT -t filter -A tcprules -i ppp+ -m state --state NEW,INVALID -j DROP
$IPT -t nat -A POSTROUTING -o ppp+ -s 192.168.0.0/24 -d 0/0 -j MASQUERADE
$IPT -t filter -A INPUT -j tcprules
$IPT -t filter -A FORWARD -j tcprules
$IPT -t filter -P INPUT DROP
$IPT -t filter -P FORWARD DROP
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t filter -L -n
iptables -t nat -L -n
iptables -t mangle -L -n
iptables -t filter -L -nv

* https://netfilter.org/
* https://www.netfilter.org/documentation/HOWTO/netfilter-hacking-HOWTO-3.html
* https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html


## Tools
* https://github.com/rickettm/SendIP


## eBPF/XDP
* https://cilium.readthedocs.io/en/latest/bpf/#bpf-and-xdp-reference-guide
* https://unhandledexpression.com/general/rust/2018/02/02/poc-compiling-to-ebpf-from-rust.html (#Rust)


## Linux Bridge
https://goyalankit.com/blog/linux-bridge

https://wiki.aalto.fi/download/attachments/70789083/linux_bridging_final.pdf




# Virtualization
## Firecracker
* https://firecracker-microvm.github.io/

## GVisor
* https://www.infoq.com/presentations/gvisor-os-go
    What is a container?
    1. Packaging - tarball of tarballs
    2. Abstratcion on top of system abstractions
    Great for portability, etc.. but not for containing since its a shared kernel

    What gvisor does
    1. Let the kernel handle physical hardware (as opposed to VM)
    2. Don't rely on kernel for abstractions and system API



# Kubernetes

## NodePort/LB/Ingress -  How we get to a service
* https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0
ClusterIP is default - for internal service reachability and proxy : kubectl proxy --port=8080
* NodePort
Opens a port on all nodes to reach the service
Recommended to let k8s choose the port from a pool
You can only have once service per port
You can only use ports 30000–32767
If your Node/VM IP address change, you need to deal with that
* LoadBalancer
Default choice in the cloud to expose a service directly
No filtering/routing, so any traffic will work, not just HTTP
Each service exposed will get an IP and LB, which you pay for
* Ingress
Not actually a type of service
Sits in front of multiple services and routes requests

### Ingress
* https://mesosphere.com/blog/ingress-controllers-kubernetes/
* https://kubernetes.github.io/ingress-nginx/user-guide/external-articles/
* https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-ingress-guide-nginx-example.html
* https://github.com/heptio/contour
* https://medium.com/@cashisclay/kubernetes-ingress-82aa960f658e
* https://cloud.google.com/kubernetes-engine/docs/tutorials/http-balancer
Uses nodeport for the service, then deploys load balancers to provide the ingress service



## Projects
### Cloud Services Platform from Google
A basic service in k8s is usually a dynamic set of pods behind a grouping mechanism that implements varous policies and maintains the service IP

### K3S
https://github.com/rancher/k3s

### Kube Ops View
https://github.com/hjacobs/kube-ops-view

### Skipper 
- https://github.com/zalando/skipper 
- An HTTP router and reverse proxy for service composition, including use cases like Kubernetes Ingress

## Tutorials / Walkthroughs
https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/configure-advanced-networking.md


# Istio 
## Crash course
- https://medium.com/namely-labs/a-crash-course-for-running-istio-1c6125930715
Istio takes advantage of the mutating webhook configuration to modify pods as they are created.  The pods are modified to add an init container for setting up iptables rules to send traffic to the envoy proxy, as well as inserting the envoy proxy sidecar pod itself.

k8s uses a service to handle networking and a service manages a type called endpoint.

A service has match labels to choose which pods to use to handle the request, and provides a virtual IP.
Traffic will hit VIP and then be forwarded to the pod.

Istio behaves differently.  Istio configures envoy based on services and endpoints, bypasssing the k8s service.  Instead of proxying a single IP, envoy knows and connects to pod IPs.  Istio maps k8s config to envoy config to make this possible.

Service in k8s = cluster in envoy
Envoy cluster has list of endpoints which are IPs to handle requests
Istio configures protocols for service manifests by reading name field in port entries
**i.e. you need to name your ports with grpc, http, prefixes**

Listeners incorporate k8s endpoints to allow traffic into pods, such as addressvalidator

Istio does not use Ingress, instead usues custom resource called *VirtualService* that allows you to match routes to upstream clusters by attaching them to a gateway.  Similar to Ingress + Ingress Controller

How do you install Istio?
We use Spinnaker for our deployments, but in general the process entails pulling down the latest Helm charts, making our in-house modifications, using helm template -f values.yml and committing those files to Github to compare changes before we apply them via kubectl apply -f -. We take this approach in order to confirm there are no CRD or API changes across versions we may not have accounted for.


# Network automation
## OpenConfig
* https://github.com/openconfig/ygot
* https://github.com/openconfig/reference/tree/master/rpc/gnmi

# Automation
## Spinnaker
## Jenkins


# Programming
## Rust
## Go
## Category Theory
* http://www.blurb.com/b/9008339-category-theory-for-programmers
