# J&J EBC with Nutanix
28-JUN-2017
Fred Hsu - Arista
Adam Dworsky - Nutanix
Jeff Foraker - JJ
-- Thenu Kittappa - Nutanix
Steve Coleman - Nutanix
Sridhar - Nutanix

Hugh from JJ has worked with Acropolis tracer already
Currently vSphere all over at JJ
Nutanix has lost 2 deals, but working on a 3rd with JJ wanting to have diversity
Need microsegmentation, vxlan, etc.. and reubild their internal automation
PoC with Nutanix ends Aug 31st
Want microsegementation - controlled centrally

Initially looking at remote site - want to virtualize firewall / microsegmentation, currently physical
Branch office would be mfg or lab factility and need to split compontnets
FW close to lab facility, not so much on the wan side
ISA layers
Want to add multi-teanancy and overlapping IP
Firewall not doing VPN
Currently no isolation between VMs on the same L2

VPN and LB are ip based, so need for IP mobility between DCs - VXLAN used for that

Network automation - next phase

Nutanix will be providing microsegmentation and service insertion of firewalls
But without the use of overlay networking

Looking to provide a hybrid/cloud service - Nutanix hosted hybrid cloud
tech preview end of year - for disaster recovery as a service
On premise piece of that will be 2018
Will utilize evpn and vxlan

Q: multi-rack in the DC - currently controllers have to be L2 connected 
A: will be removing that dependency - also recommend vxlan in network which is where Arista automation comes in

Nutanix working on SW based VPN service from acropolis
Nutanix also looking at a LB service based on haproxy? Roadmap but far out

JJ uses F5 currently, but NSX in the branch

Need a solution for nsx edge GW.. possibly MSS as a way to provide N/S firewalling, they need to firewall non-nutanix vm devices
