# Lessons learned
Don't use flask for heavy traffic
Test scalability before allowing lots of users
Use a select number of users for large scale demo
Horizontal scaling
Health checks
Load balancing
What tool to use to break the existing demo?  -- ab?  How to make it fault tolerant
https://stackoverflow.com/questions/12591760/flask-broken-pipe-with-requests


# SKO Demo 
* Two k8s clusters, prod in Azure, dev on prem - connected by veos IPsec
* Microservice architecture
* Wireless sensors deployed at remote sites - Mojo wifi connected through vEOS appliance -> Azure vwan
* Can pull up Mojo UI
* Pull up CloudVision
* Using ML in the cloud to generate preditions of what the temperatures will be
* Using Google cloud to provide voice services for Assistant integration
* Dev creates new version of the app, tests on prem
* Where is this running?  Check with container tracer
* Pushes to cloud
* We're adding new ML feature to the app, pushing it to the cloud after developing - Leveraging ML from Google to IoT data stored in Azure
* Azure IoT hub grabbing sensor data
* You guys are the sensors, one per table sign into IoT web app and push data

* Azure RDP - Arista123456!


## TODO

- [x] Bring up 2 node k8s cluster in Azure
- [x] Bring up 2 node k8s cluster in DC
- [x] Connect cEOS in DC to cloudvision
- [x] Connect cEOS in Azure to cloudvision
- [x] Connect veos in Azure to cloudvision
- [x] Connect veos in DC to cloudvision
- [ ] Connect veos in gcp to cloudvision
- [x] Configure container tracer for DC cluster
- [x] Install CV reachable from outside and cluster
- [ ] Connect virtual appliance
    - Need to load KVM on it
- [x] Get BGP between vEOS in VWAN and vEOS in Azure
    - Need to complete above action first
    - Using veos-remote as alternative
- [ ] Connect wifi to virtual appliance
    - Need to get Mojo device from home
- [x] Get BGP between vEOS in DC and vEOS in Azure
- [x] Get BGP between vEOS in Azure and GCP
- [x] deployment for temp + dbint -- pulls sensor data
- [x] service for temp + dbint -- pulls sensor data
- [x] deployment for ml -- provides data from DB and displays
- [x] service for ml -- provides data from DB and displays
- [x] Get mL to pull from database
- [x] calculate linear regression
- [x] Show linear regression line
- [x] Show data points
- [x] Show prediction
- [ ] Show data point labels
    - view-source:https://www.chartjs.org/samples/latest/advanced/data-labelling.html
- [x] REST for prediction
- [x] REST for data
- [x] REST for regression
- [ ] What if container tracer for cloud.. bgp based
- [ ] Create Google action to fetch data from CloudVision
  - Ask what the next temperature will be
  - POST to app to find temperature
  - respond with temp
  - Ask about next hop to IP address
  - POST to app to find next hop
  - respond with from which device
  - say "kube 153"
  - respond with next hop


## Status
* routes being exchanged
* Need to put in static route to override the gated routes back to DC in the cloud

## Caveats
* routing not working correctly between azure nodes - scheduling all nginx on vm2
* can curl from eth2 in vEOSdc to nodeport exposed on vm2 for nginx
* Needed next-hop self for veos-az and ebgp-multihop to estab bgp between dc and az
* Needed to use lo0 since cannot ping to tun5 interface

# Setup info
* CVP: 172.20.7.100
* veos-azure : 13.66.133.245 -- fredlhsu/Arista123456
* veos-DC : 10.90.224.180
* kube-138
* kube-139
* Azure VM1: 13.66.222.192
* Azure VM2: 52.247.212.53 -- MongoDB
* Azure VM4: 52.175.251.120 -- fredlhsu/Arista123456
* Azure LB: 
  * Temp: <http://13.66.230.106> 
  * Graph: http://52.183.1.97/
* GCP: 104.196.240.105
* veos-remote : 10.90.224.181 -- 172.22.132.53 
* CVX: 10.90.224.53

## TerminAttr config

```
daemon TerminAttr
  exec /usr/bin/TerminAttr -ingestgrpcurl=172.20.7.100:9910 -taillogs -ingestauth=key,
  no shutdown
 ```

 ```
 daemon TerminAttr
 exec /usr/bin/TerminAttr -ingestgrpcurl=172.20.7.100:9910 -taillogs -ingestauth=key, -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/
 no shut
 ```


# Demo steps
1. diagrams
2. Have users start creating datapoints
3. Azure VWAN with vEOS
4. cEOS running in Azure
5. cloudvision with routing tables to remote sites
6. Begin creating new application
  * Describe google conatiner registry
  * Show routes to Google
7. Deploy to dev cluster
8. Containertracer
9. CloudVision routes to dev cluster
9. Deploy to prod cluster
10. Bring up graph on webpage
11. Wrapup (Google Assistant for bonus)

# Script
Ok lets start with the big picture.  I have this application where I'm pulling in data from IoT temperature sensors out at remote sites, feeding that data over Azure Virtual WAN back to the Azure IoT Hub service.  From there I'm running a kubernetes cluster that takes in that data.  For the demo, I'm going to host a web service on the kubernetes cluster that is going to take in the temperature data and feed it back to IoT database.  I'd like to have you guys pretend to be the sensors.  So if you could go to this URL on your phone and enter in a temperature, that is going to simulate our remote sensor devices feeding data back to Azure. This data is fed back into Azure's IoT hub service for data collection.  These remote sites are connected using Arista vEOS, and are connected to the Azure Virtual WAN service.  Let me show you what that looks like. *browse to azure vwan portal*

Here we have the Azure virtual wan dashboard, and you can see I have a map of the different locations that are connected.  One on the west coast and one on the east.  These connected sites are using vEOS as their VPN gateway.  From here, we get connected to the rest of my azure Virtual network.  Let's go back to the diagram. *slide*

So you can see I have my production kubernetes cluster running in Azure, and we're using cEOS as our kubernetes network control plane.  In fact, when you all went to that website, it was running on this cluster.  Let me login to that cluster and show you what's running. *ssh to azure, kubectl get pods* Here are the kubernetes pods on the cluster, and you see we have my temperature sensor simulator and cEOS to provide our routing stack. **optional** With cEOS here I can run CLI commands to show you its providing our network stack.  *kubectl exec -it FastCli -p 15 -c "show ip route"*

 I have another cluster here in my datacenter for development use.  Also using cEOS for the control plane, and vEOS connecting back to Azure. Let's go into cloudvision and take a look.  You'll see we're getting streaming telemetry from all those cEOS and vEOS nodes, as well as my switches.  This provides way better visiblity than open source tools, and less lock in than proprietary full stacks.  I can see my leaf switch, and all the various virtual and containerized EOS endpoints.

 
Now let's say just like any other good app in Silicon Valley, we need to add some Machine Learning.  I'm not going to use a very deep algorithm, its a simple prediction based on linear regression, but you get the idea.  So we're going to create a new application and test it out on my dev cluster.  I'll build a new docker image for the app, notice I'm pushing these up to google container registry so I'm leveraging my connection over to GCP, and then use kubernetes to deploy it to my dev cluster.  *make* *kubectl deploy* Let's just wait a few seconds here for the containers to start up.  Now after creating the new deployment here on the dev cluster, I can use our new container tracer with support for kubernetes to verify where these containers live in the network.  *show containertracer* and if we pull up the IPs for those containers *kubectl get po -o wide* you'll see they're on the 10.110 subnet, so I can go into cloudvision and see that subnet on my switch, my vEOS in Azure, and Kubernetes clusters in Azure since we're running cEOS on those kubernetes servers and advertising the route.
 
Everything looks good so far, so let's push this up to the cloud.  I'm going to use those new containers stored in Google container registry, but run the service in Azure.  So if we go over here to the google dashboard you see we have our new application containers ready, then I come over to my vms and see I have vEOS that is connecting me over to Azure.  Just to make sure you know I'm really running the connection here I can ssh into this virtual router and see the ip sec connection is up and I'm getting my routes.  Over here in cloudvision I can also see the routes from GCP, 10.138.0.0 in this case, showing up in all my routers.  Ok first I'm going to try and browse to the ML tool before I deploy anything, and you'll see we get nothing.  So I deploy my new kubernetes service in Azure, wait a few seconds for the containers to start, and now when I hit my website, fingers crossed, you'll see our pretty graph.  If you guys enter in some more data the graph will keep updating with the temperatures we're sensing, and the line shows the linear prediciton for future temperature.

 So that's the demo, and you can see how we've used the EOS throughout multiple places in the cloud to bring a customer application to production.  Thanks!

 
# Speech to text
CloudVision
**What feature of cloudvision?**
Devices
**For which device are you interested?**
- Fred demo site 1
- kube 138
- azure vm 1
- remote site 1
**What information would you like?**
IPv4 Routing tables
**What route would you like to find?**
10.110.0.0
**There is a route and the next hop is via ...**
Goodbye
**Thanks and remember friends don't let friends buy Cisco**



