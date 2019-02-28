# Securing Clusters with Kubernetes Network Policies - Ahmet Balkan, Google
## What are network policies
* control what traffic from/to pods
* Stable since 1.7
### Labels
* Add metadata to pods
* Then can be used as selectors
	* matchLabels: tier:db
	* empty label selector - will select everything
## How to write them
1. which pods to they apply to - label selector
2. which direction 
	* Ingress
	* Egress
3. rules for allowing
	* Ingress - who can connect to pod?
	* Egresss - who can pod connect to?
Rules:
1. Traffic is *allowed* unless there is a newtork policy selecting the pod
2. traffic is *denied* if there are policies selecting the pod, but none of them have any rules allowing it.
Rule 1 + 2 = you can only write rules that *allow* traffic
	* i.e. no deny rules
* ex. Denying all incoming traffic

	spec:
		podSelector: {}
		ingress: {}

3. Traffic is *allowed* if there is at least one policy allowing it
* ex. Backend should talk to DB, but not frontend

	spec:
		podSelector:
			matchLabels:
				app: foo
				tier: db
		ingress:
		- from:
			- podSelector:
				matchLabels:
					app: foo
					tier: backend

* ex. restricting ports

		- from:
			- podSelector:
				matchLabels:
					app: foo
					tier: backend
			ports:
			- port: 3306
				protocol: TCPports:
				- port: 3306
					protocol: TCP


4. Policy rules are additive, they are OR'ed with each other
ex. Using empty selectors

	spec:
		podSelector: {}
		ingress:
		- from:
			-podSelector
				matchLabels:
					role:monitoring
				ports:
				- port: 5000

Gotcha: this will only work if pods are in the same namespace!

5. Network policies are scoped to the namespace they're deployed to
	* You have to deploy policy to every namespace
	* namespaceSelector allows you to match pods from other namespaces
	  * selects Namespaces using labels - limitation, allows all pods from a namespace

* The `ipBlock` selector

		from:
		- ipBlock:
				cidr: 10.0.0.0/8
				- 10.11.12.0/24

* Egress rules
	* almost identical to ingress
	* controls traffic *from* pods
* ex. allowing some egress

		spec:
			podSelector: 
				matchLabels:
					tier: frontend
			policyTypes: 
			- Egress
			egress:
			- to:
				- podSelector:
					matchLabels:
						tier: backend

Note this will also deny DNS, so you need to allow all DNS or add egress rule with the IP address of kube-dns

		spec:
			podSelector: 
				matchLabels:
					tier: frontend
			policyTypes: 
			- Egress
			egress:
			- to:
				- podSelector:
					matchLabels:
						tier: backend
				- ports:
					- port: 53
					  protocol: "UDP"
					- port: 53
					  protocol: "TCP"

## How do they work
* Without a network plugin that enforces policies, network policies are silently ignored
* Install a plugin like Calico, weave, or romana
* GKE uses Calico
* Policies will not kill an existing flow if a new policy is implemented
* Policies add minimal latency
	* Depends on plugin
	* iptables vs overlay
	* implementation/caching differences
	* Can depend on the # of policies
	* sub 1ms. overhead
## Go home and use them
## Best practices
* Best practices
* Use *default-deny-all* rules
	* first block all ingress/egress in a ns
	* then start whitelisting
* understand rule evaluation
	* rules are OR'ed (not AND'ed)
	* be explict about empty vs null fields
* test your policies
	* try what's allowed/blocked
	* test external connectivity
		* ingress: Should I allow traffic from external LB?
		* egress: Should I allow connections to external?
	* test with other ns
		* allow traffic to/from another ns?
	* use `kubectl describe` to verify rule syntax
* github.com/ahmetb/kubernetes-networkpolicy-tutorial

