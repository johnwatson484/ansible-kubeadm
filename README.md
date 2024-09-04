# Ansible Kubeadm
Ansible playbooks for creating Kubeadm Kubernetes cluster

## Prerequisites
- Ansible
- at least three Ubuntu instances, one master and two worker nodes
- IPv6 enabled
- all ports open to all other nodes
- port 22 and range 30000:32767 open to public


## Creating a cluster
### Update hosts file
Amend host and worker IP addresses as appropriate in `./cluster/hosts`

### Create Ubuntu user
`ansible-playbook -i ./cluster/hosts ./cluster/initial.yaml`

### Install Kubernetes dependencies on hosts
`ansible-playbook -i ./cluster/hosts ./cluster/kube-dependencies.yaml`

### Open required ports (If not managed firewall)
Update port IP addresses for nodes in `./cluster/ports.yaml`

`ansible-playbook -i ./cluster/hosts ./cluster/ports.yaml`

### Setup master nodes
`ansible-playbook -i ./cluster/hosts ./cluster/master.yaml`

### Setup worker nodes
`ansible-playbook -i ./cluster/hosts ./cluster/worker.yaml`

### Create OpenVPN secrets (Optional)
If existing OpenVPN certificates are present in `./resources/openvpn-secrets` then the below will add them to a Kubernetes secret in the cluster.  

`ansible-playbook -i ./cluster/hosts ./cluster/openvpn-secrets.yaml`  

Note: using secrets will prevent future generation of certificates due to read only filesystem.

### Install cluster services
- NGINX ingress controller
- OpenVPN
- Cert Manager  

`ansible-playbook -i ./cluster/hosts ./cluster/cluster.yaml`

## Create OpenVPN certificate file
(As Ubuntu user)

`~/openvpn/generate-key KEY_NAME openvpn WORKER_NODE_IP openvpn`

`KEY_NAME` - name of `.ovpn` file to create  
`WORKER_NODE_IP` - IP of worker node which will serve VPN access

### Configure default storage (Optional)
If persistent storage is needed then configure default storage to use node filesystem.  

`kubectl create -f ./resources/configuration/storageClass.yaml`  

Replace `worker1` and `worker2` with actual Node name under values.  `kubectl get nodes` will display names.  

`kubectl create -f ./resources/configuration/persistentVolume.yaml`  

Note: If not using VPN files will need to be copied to master node.

### Configure Certificate manager (Optional)
If certificate authority required update email address and provider settings in `./resources/cert-manager/issuer.yaml`.  Example shows LetsEncrypt use.  

`ansible-playbook -i ./cluster/hosts ./cluster/cert-manager-issuer.yaml`

### Configure dashboard access
Get token for dashboard access.  

`kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')`  

Add output token to kubeconfig file as below:  

```
users
  - user:
      token: TOKEN
```

To connect to dashboard, first proxy to local with `kubectl proxy` and navigate to http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

## Setup Infiscal secrets manager (Optional)

Create a machine identity in Infiscal and setup universal auth secret in cluster following the instructions in the [Infiscal documentation](https://infisical.com/docs/integrations/platforms/kubernetes#authentication-universalauth) 
