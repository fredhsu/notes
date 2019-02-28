docker create --name=ceoslab2 --privileged -e ETBA=1 -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
ad1b872c80cbcba3ddf3487025a0bb50c77c8494df9f84b1803cb7ce3c8c4384
fredlhsu@Docker-150:~/test$ docker cp startup-config /mnt/flash/startup-config
must specify at least one container source
fredlhsu@Docker-150:~/test$ docker cp startup-config ceoslab1:/mnt/flash/startup-config
fredlhsu@Docker-150:~/test$ docker start ceoslab1

docker cp startup-config ceoslab1:/mnt/flash/startup-config

fredlhsu@Docker-150:~/test$ docker create --name=ceoslab2 --privileged -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e ETBA=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
WARNING: IPv4 forwarding is disabled. Networking will not work.
5cd61effff1a873f4d7e2afb69cdf5f8106f6b18016b4800eb2a57573f03d369
fredlhsu@Docker-150:~/test$ docker cp startup-config ceoslab2:/mnt/flash/startup-config
fredlhsu@Docker-150:~/test$ docker start ceoslab2


docker create --name=leaf1 --privileged -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e ETBA=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
docker cp leaf1-config leaf1:/mnt/flash/startup-config


docker create --name=leaf2 --privileged -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e ETBA=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
docker cp leaf2-config leaf2:/mnt/flash/startup-config

docker create --name=spine1 --privileged -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e ETBA=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
docker cp spine1-config spine1:/mnt/flash/startup-config

docker create --name=spine2 --privileged -e CEOS=1 -e container=docker -e EOS_PLATFORM=Docker -e SKIP_ZEROTOUCH_BARRIER_IN_SYSDBINIT=1 -e ETBA=1 -e INTFTYPE=eth -it ceosimage:latest /sbin/init
docker cp spine2-config spine2:/mnt/flash/startup-config

docker network create L1S1
docker network create L1S2
docker network create L2S1
docker network create L2S2
docker network create E1L1
docker network create E2L2

docker network connect L1S1 leaf1
docker network connect L1S2 leaf1
docker network connect L2S1 leaf2
docker network connect L2S2 leaf2