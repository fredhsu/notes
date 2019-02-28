# The mechanics of deploying Envoy at Lyft - Matt Klein
* Every service has a sidecar proxy
* Each service only knows about its local proxy, doesn't need to know about underlying network
* Also allows any language
* Envoy is a self-contained proxy
	* L3/L4 filter
	* Http L7 filter can operate on messages
	* Http/2 first
	* Service discovery / active/passive health checking
	* Advanced LB
	* *Best in class observability* - stats/logging/tracing
	* Edge proxy
* Lyft runs envoy on every node to give stats/tracing
* MongoDB, Spanner, etc.. for DB
## Started with Edge proxy
* Web apps need edge reverse proxy
* Existing cloud offerints not so good (ALB/Google)
	* not much visiblity
* Easy to show value quickly with stats, enhanced LB, and routing and proto
## Next: TCP proxy and Mongo
* Mongo behaves badly, so envoy allows rate limiting and provides more info
## Next: Service Sidecar
* Start using Envoy for ingress buffering, circuit breaking, and observability
* Still using internal ELBs for service to service traffic
* Tons of value without direct mesh connect
* AWS TCP ELB -> Edge Envoy -> AWS Internal ELB -> Mesh Envoy -> Service
## Next: Service discovery and real mesh
* ELBs are not great, use direct connect 
* Need service discvoery
* No ZK, etcd, Consul.  Eventually consistent. Simple build system with simple API -- most companies use teams to just keep ZK alive, but service discovery can be eventually consistent.  Do you really need fully consistent?
	* 200 lines of python and cron job
	* Runs on boot, writes to DynamoDB, and a sweeper that deletes a service that doesn't check in
	* Caching in service and envoy, allows things to keep running even if things die
## Envoy thin clients
* To get full data you need to have clients that provide data
* Envoy client does this in Python and Go
## Next: Envoy everywhere
* Run everywhere for full value
* Slog to convert everything

## Move from static configs to templates
* Use Jinja templates to define
* Evolve to manifests that genreate the templates
* Eventually want to get to minimal config on server, config comes from service manage.. see Istio Pilot
