sudo kubeadm init --pod-network-cidr="10.100.0.0/16" --ignore-preflight-errors=SystemVerification --apiserver-cert-extra-sans=127.0.0.1


  mkdir -p $HOME/.kube
yes | sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

docker pull docker.elastic.co/elasticsearch/elasticsearch-oss:6.4.1
docker pull docker.elastic.co/kibana/kibana-oss:6.4.1
docker pull quay.io/tigera/calicoctl:v2.2.1
docker pull quay.io/tigera/calicoq:v2.2.1
docker pull quay.io/tigera/cnx-apiserver:v2.2.1
docker pull quay.io/tigera/cnx-manager:v2.2.1
docker pull quay.io/tigera/cnx-manager-proxy:v2.2.1
docker pull quay.io/tigera/cnx-node:v2.2.2
docker pull quay.io/tigera/cnx-queryserver:v2.2.1
docker pull quay.io/tigera/es-proxy:v2.2.1
docker pull quay.io/tigera/fluentd:v2.2.1
docker pull quay.io/tigera/kube-controllers:v2.2.1
docker pull quay.io/tigera/typha:v2.2.1
docker pull quay.io/coreos/configmap-reload:v0.0.1
docker pull quay.io/coreos/prometheus-config-reloader:v0.0.3
docker pull quay.io/coreos/prometheus-operator:v0.18.1
docker pull quay.io/prometheus/alertmanager:v0.14.0
docker pull quay.io/prometheus/prometheus:v2.2.1
docker pull giantswarm/tiny-tools:0.3.0
docker pull upmcenterprises/elasticsearch-operator:0.2.0

kubectl create namespace calico-monitoring

SECRET=$(cat config.json | tr -d '\n\r\t ' | base64 -w 0)

cat > cnx-pull-secret.yml <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: cnx-pull-secret
  namespace: kube-system
data:
  .dockerconfigjson: ${SECRET}
type: kubernetes.io/dockerconfigjson
---
apiVersion: v1
kind: Secret
metadata:
  name: cnx-pull-secret
  namespace: calico-monitoring
data:
  .dockerconfigjson: ${SECRET}
type: kubernetes.io/dockerconfigjson
EOF

kubectl create -f cnx-pull-secret.yml

kubectl apply -f \
https://docs.tigera.io/v2.2/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml

curl \
https://docs.tigera.io/v2.2/getting-started/kubernetes/installation/hosted/kubernetes-datastore/policy-only/1.7/calico.yaml \
-O
# May need to edit calico.yaml to change subnet
kubectl apply -f calico.yaml

DATASTORE_TYPE=kubernetes KUBECONFIG=~/.kube/config calicoctl apply -f - < arista-license.yaml

curl --compressed -o cnx.yaml \
https://docs.tigera.io/v2.2/getting-started/kubernetes/installation/hosted/cnx/1.7/cnx-kdd.yaml

sudo kubectl create secret generic cnx-manager-tls \
--from-file=cert=/etc/kubernetes/pki/apiserver.crt \
--from-file=key=/etc/kubernetes/pki/apiserver.key -n kube-system

kubectl apply -f \
https://docs.tigera.io/v2.2/getting-started/kubernetes/installation/hosted/cnx/1.7/cnx-policy.yaml

kubectl create clusterrolebinding network-admin \
  --clusterrole=network-admin \
  --user=jane

cat > basic_auth.csv <<EOF
welc0me,jane,1
EOF
sudo mv basic_auth.csv /etc/kubernetes/pki/basic_auth.csv
sudo chown root /etc/kubernetes/pki/basic_auth.csv
sudo chmod 600 /etc/kubernetes/pki/basic_auth.csv
cat > sedcmd.txt <<EOF
/- kube-apiserver/a\    - --basic-auth-file=/etc/kubernetes/pki/basic_auth.csv
EOF
sudo sed -i -f sedcmd.txt /etc/kubernetes/manifests/kube-apiserver.yaml
sudo systemctl restart kubelet


curl --compressed -O \
https://docs.tigera.io/v2.2/getting-started/kubernetes/installation/hosted/cnx/1.7/operator.yaml

kubectl apply -f operator.yaml
