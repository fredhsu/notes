# A day in the life of a cloud network engineer at Netflix (NET303)
* Donavan Fritz : dfritz@netflix.com
* Joel Kodama : jkodama@netflix.com
## Intro
* 150,000+ EC2 instances
* 75+ accounts
* 4 AWS regions
* Globally unique IP space is good
	* 100.64.0.0/10
* IP mgmt is hard
	* Using tools to monitor subnet utilization of IP addresses
	* VPC resize helps
* They monitor the vpc configuration periodically and does diffs. 
	* AWS doesn't always notify on change events
	* i.e. route propgation changes on vgw, they want to see that new routes showed up
## DNS
## Network insight
* Can someone resolve connectivity issue from one microservice to another?
* Does anyone know if there are any network events in us-east-1? some hosts have been partitioned
* Network errors on startup
* VPC is a black box - how do we gather data
* VPC flow logs
	* Really good, but meaningless data
	* IPs <-> IPs, doesn't mean anything since IPs change
	* records are stateless
	* 10,000,000+ flow logs per second
* Network segmentation
	* Not there in the cloud since VPCs have large flat IP ranges
	* Also there are resources that the company has no control over
* What app has these IPs?
	* What app has these IPs, at a specific time? (since ips change over time)
	* What app has these IPs, at a specific time, in a routing domain? (overlapping IPs)
	* f(domain, ip, time) = app
		* Netflix Sonar
		* Extract : Cloudwatch + netflix events
		* Extract Polling : AWS APIs/Logs, Netflix APIs/Logs, DNS crawling
		* Transform : Event processing
		* Transform polling : Polling
		* Load : IP Change events stream - what happened to an IP address over time
			* If an ip address gets assigned, mapped to EC2, attached to a container on an ENI
		* Load Polling : feeds into ip change events
* Flow logs are stateless
	* Run EC2s with SSM that tells what TCP or UDP something is listening on
* Flow logs are fragmented
	* Known deficiency
* Scale of flow logs
	* Netflix Dredge
		* Takes IP Change events (sonar) + VPC Flow logs (via kinesis) = stream join => Netflix data pipline (druid)
* Stream joins
	* Routing Domain = account + ENI
	* IPv4 address
	* Timestamp
	* f(domain, ip, time) = app
	* Then lookup which side was listening at the time
	* Transform all this into an enriched vpc flow record in JSON format to feed into data pipeline
* Sonar integration into slack that will pull an IP address and give data back




