# cadvisor
* runs as part of kublet
* gathers stats about the system
* presents them on port 4194
* the default kubeadm configuration disables the cAdvisor port (with the --cadvisor-port=0 flag).
* Actually CIS requires cadvisor-port=0, so I recommend everyone to use the /metrics/cadvisor path. I'll leave this open as we need to fix it for kube-prometheus.

# metrics
Can query host at port http://kube153:10255/spec/
/metrics/
/metrics/cadvisor
