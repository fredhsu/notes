https://docs.microsoft.com/en-us/azure/aks/ssh

When trying to login to azure k8s node, the nodes may be part of a node resource group, not the group created for aks.  Need to do a show of the aks and then get the node rg name to be able to hit the vm hosting the node.

az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_fredaksgroup_fredAKSCluster_westus

az aks show --resource-group fredaksgroup -n fredAKSCluster -o table
az vm list -g MC_fredaksgroup_fredAKSCluster_westus  -o table #Should show the VM matching node name from kubectl get nodes

az vm user update -g MC_fredaksgroup_fredAKSCluster_westus -n aks-nodepool1-26627485-0 --username fredlhsu --ssh-key-value ~/.ssh/id_rsa.pub # Adding my ssh key to the vm/node

Then need to get private IP of the node
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

Create a jump host container
kubectl run -it --rm aks-ssh --image=debian

install ssh on jump host
apt-get update && apt-get install openssh-client -y

Get pod name
kubectl get pods

Copy ssh key over
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa


Now ssh from jump host 
$ ssh -i id_rsa azureuser@10.240.0.4

sudo journalctl -u kubelet -o cat
