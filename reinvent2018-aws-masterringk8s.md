# Mastering k8s on AWS 
* Yaniv
## Moving to production 
* data plane -- once you scale up scheduling and managing infra
* Control plane - managing k8s control plane can be tricky and complex
    * Multiple masters
    * Multiple etcd
    * one kubedns
    * quorum of 3 for one or 5 to have two node redundancy
    * if more than 50% is down it will be readonly
* 51% of k8s is run on aws today - cncf
## EKS abstracts the control plane by creating HA control plane
* Mutliple masters in multiple AZs
* Cross account design - control plane is in AWS owned EKS vpc, workers are in customer VPC
* EKS owned cross account ENI in customer vpc connected to worker that can be managed by eks

## Netwokring pod to pod
* AWS VPC CNI Plugin
* run as daemonset
* gets list of enis that provide secondary IPs  for pods
* Pod gets scheduled, gets the ip from ipamd
* ipamd to handle ip address requests and maintaints list - configurable
### IPAMd
* Create vpc with primary cidr ie. 10/8, 172.16/12, 192.168/16
* Used in eks for:
    * pods
    * x-accoutn enis for master -> worker communication - exec, logs, proxy, etc.
    * internal k8s services network (10.100/16 or 172.20/16 - chosen based on vpc range, will not be in your vpc range)
* Setup
    * EKS cluster creation -> provide list of subnets (in at least 2 AZs) -> tagging
* custom network configs (new)
    * secondary cidr range on vpc -> non rfc1918 blocks 
    * can be used for pods only
    * aws eks custom network config -> enable -> create ENIConfig CRD -> annotate nodes
### Configurability
* Custom network configs
* SNAT/ External SNAT 
    * by default pod IP will be translated into host primary for outbound
    *  can use external SNAT provider to disable SNAT (i.e. use NAT GW on vpc) so private communication can avoid nat
* Configurable warm pool 
    * Can select the number of ENIs/IPs that can be used by eks
## Networking pod to service
### k8s servicetype; clusterip
* exposes the service on a cluster-internal ip -- not accessible from outside
* only reachable from within the cluster
* access possible via kube-proxy
* useful for debugging services, connecting from laptop, or displaying internal dashboards
traffic -> proxy -> service => pods
## k8s servicetype: nodeport
* exposes the service on each node ips at a static port
* routes to a clusterIP service which is automatically created
* from outside the cluster: <nodeip> : <nodeport>
* 1 service per port
* uses ports 30000 - 32767
## k8s servicetype: loadbalancer
* exposes the service externally using a cloud provider's LB (LB is cloud specific)
* not really used for on prem?
* nodeport and cluster ip services (to which the lab will route) automatically created
* each sevice 
* by default ELB but in k8s 1.9 there is support for NLB
## Service LB: NLB
* NLB supports forwarding the client's IP through to the node
* .spec.externalTrafficPolicy = Local -> client IP passed to pod
* Nodes with no matching pods will be removed by specified NLB's health check
* . spec.healthCheckNodePort
* Use Daemonset or pod anti-affiniity to verfiy even traffic split
## k8s servicetype: externalname
* Maps: service -> cname
* no proxying
* accessing my-service works in the same way as other services
* redirection happens at dns level
## k8s ingress object
* exposes http/https routes to services within the cluster
* many implenetations: alb, nginx, f5, haproxy
* defualt service type: clusterIP
## ALB ingress controller

# Security: Runtime
* Lots of optins for giving pod permission to AWS service
* kube2iam - most used in production
* kiam
* iam4kube
* kube-aws-iam-controller

# Logging: workers
* EFK stack (fluentd data collector providing unified logging layer)
* fluentd daemonset -> cloudwatch using plugin -> cloudwatch logs subscripption to ES -> visualized with kibana 
    * cloudwatch would be optional

# Snapchat perspective

CON 412 Networking on eks
