# Build a Kubernetes cluster using k3s via Ansible

Author: <https://github.com/itwars>

## K3s Ansible Playbook

Build a Kubernetes cluster using Ansible with k3s. The goal is easily install a Kubernetes cluster on machines running:

- [X] Debian
- [X] Ubuntu
- [X] CentOS

on processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## System requirements

Deployment environment must have Ansible 2.4.0+
Master and nodes must have passwordless SSH access

## Usage

First create a new directory based on the `sample` directory within the `inventory` directory:

```bash
cp -R inventory/sample inventory/my-cluster
```

Second, edit `inventory/my-cluster/hosts.ini` to match the system information gathered above. For example:

```bash
[master]
192.16.35.12

[node]
192.16.35.[10:11]

[k3s_cluster:children]
master
node
```

If needed, you can also edit `inventory/my-cluster/group_vars/all.yml` to match your environment.

Start provisioning of the cluster using the following command:

```bash
ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
```

## Kubeconfig

To get access to your **Kubernetes** cluster just

```bash
scp debian@master_ip:~/.kube/config ~/.kube/config
```
## Vagrant

With 
```bash
vagrant up
```
virtual machines are created. In the moment only master1 is in the cluster. The other masters are ignored. 
```bash
vagrant@master1:~$ sudo kubectl get nodes
NAME      STATUS   ROLES                  AGE   VERSION
node1     Ready    <none>                 60s   v1.22.3+k3s1
master1   Ready    control-plane,master   98s   v1.22.3+k3s1
node3     Ready    <none>                 64s   v1.22.3+k3s1
node2     Ready    <none>                 62s   v1.22.3+k3s1
```
The number of master and nodes can be changed in `Vagrantfile`.