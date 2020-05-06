# Ansible Kubeadm
Ansible playbooks for creating Kubeadm Kubernetes cluster

## Prerequisites
- Ansible

## Creating a cluster
### Update hosts file
Amend host and worker IP addresses as appropriate in `./cluster/hosts`

### Create Ubuntu user
`ansible-playbook -i ./cluster/hosts ./cluster/initial.yaml`

### Install Kubernetes dependencies on hosts
`ansible-playbook -i ./cluster/hosts ./cluster/kube-dependencies.yaml`

### Setup master nodes
`ansible-playbook -i ./cluster/hosts ./cluster/master.yaml`

### Setup worker nodes
`ansible-playbook -i ./cluster/hosts ./cluster/worker.yaml`

### Create OpenVPN secrets (Optional)
If existing OpenVPN certificates are present in `./resouces/openvpn-secrets` then the below will add them to a Kubernetes secret in the cluster.  
`ansible-playbook -i ./cluster/hosts ./cluster/openvpn-secrets.yaml`

### Install cluster services
- NGINX ingress controller
- OpenVPN  

`ansible-playbook -i ./cluster/hosts ./cluster/cluster.yaml`

## Create OpenVPN certificate file
(As Ubuntu user)

`~/openvpn/generate-key KEY_NAME openvpn WORKER_NODE_IP openvpn`

`KEY_NAME` - name of `.ovpn` file to create  
`WORKER_NODE_IP` - IP of worker node which will serve VPN access
