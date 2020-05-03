# Ansible Kubeadm
Ansible playbooks for creating Kubeadm Kubernetes cluster

## Prerequisites
- Ansible

## Creating a cluster
### Update hosts file
Amend host and worker IP addresses as appropriate in `./cluster/hosts`

### Create Ubuntu user
`ansible-playbook -i hosts ./cluster/initial.yaml`

### Install Kubernetes dependencies on hosts
`ansible-playbook -i hosts ./cluster/kube-dependencies.yaml`

### Setup master nodes
`ansible-playbook -i hosts ./cluster/master.yaml`

### Setup worker nodes
`ansible-playbook -i hosts ./cluster/worker.yaml`
