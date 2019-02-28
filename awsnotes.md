# 29-NOV-2017 
## Networking state of the union 
### Core architecture
- Strong isolation between AZs
- Blast radius reduction
- Staged deployments
    - Staggered changes/deployments
    - Even a config change is done slowly and piece at a time
- Active Montoring
    - Network traffic is constantly monitored for any isolation violations
- SLA 99.99% of availability in EC2
### Instance network performance
- C1 = 1Gbps
- CC1 = 10 Gbps
- C3 = Enhanced networking, 20x pps, < 100us latency
- C4 = EBS optimized
- C5 = ENA, 25 Gbps, < 50us latency, offload cards -- Just launched
- 25 Gbps within placement group
- 25 Gbps within region
- 25 Gbps to S3
- 5 Gbps for other
### Hyperplane NFV
- Underpins NAT GW, EFS, and NLB
- Runs on EC2 instances
- NET405 

# 30-NOV-2017
## Container Networking on ECS
### ECS Contructs
- Register Task definition
- Create Cluster - infra isolation boundary - register EC2 instances
- Run Task - instantation of Task def. 
- Create service - managed task, define desired state, # copies, AZs, etc..

### Container networkign modes in ECS
- Host mode
- Bridge mode
- Task Networking (awsvpc) mode (new)
- None

### Bridge Mode
- Containers share NIC as instance
- Each container gets private IP and uses Docker bridge
- Mutliple tasks use same ENI
- Dyanamic port mapping mode to handle multiple apps listening on same port
    - This has challenges: performance (extra hop through docker0), lack of fine grained access control, no routable IP for containers (no network level isolati)on)

### Task Networking Mode
- ECS Agent is open soure agent running on all hosts
- This is CNI compliant
- Allocates ENI interface to each task
- Only mode that works with Fargate
- Config
    - AWS creates ENI automatically
    - ENI gets private IP
    - Sec Grp applied to ENI
- ENI attachment
    - Pre-ENI attachment, the primary ENI (eth0) is in the default ns
    - New ENI (eth1) is in the default ns
    - ECS agent invokes CNI plugins to move 
- No overlay or proxy
- Useful for servcies attached to ALB/NLB
- Could run into ENI limits based on EC2 Instnace Type

### Fox preso
- Linkerd doesn't know how to find closest container
- Does some of this using latency routing
- Tries to pick one using low latency - how does it measure?
- But doesn't try to do so based on the container running on the same host

