# Ansible Kubernetes Installation Playbook
This Ansible playbook is designed to automate the installation of Kubernetes on Ubuntu. The playbook includes the following roles and tasks:

## Prerequisites

- A compatible Linux hosts:  2 GB or more of RAM per machine and 2 CPUs or more 
- 3 - Ubuntu 20.04 LTS Serves:  1x Manager (4GB RAM, 2 vCPU)t2.medium type, 2x Workers (1 GB, 1 Core) t2.micro type 
- Full network connectivity between all machines in the cluster 
- Unique hostname for each host. Change hostname of the machines using hostnamectl. For master nodes, run`hostnamectl set-hostname master`. For slaves, run `hostnamectl set-hostname slave-01`  `hostnamectl set-hostname slave-02` 
- Certain ports are open on your machines(https://kubernetes.io/docs/reference/ports-and-protocols/)
  - On Master Node
	```
	6443/tcp for Kubernetes API Server
	2379-2380 for etcd server client API
	6783/tcp,6784/udp for Weavenet CNI
	10248-10260 for Kubelet API, Kube-scheduler, Kube-controller-manager, Read-Only Kubelet API, Kubelet health
	80,8080,443 Generic Ports
	30000-32767 for NodePort Services
	```
  - On Slave Nodes
	```
	6783/tcp,6784/udp for Weavenet CNI
	10248-10260 for Kubelet API etc
	30000-32767 for NodePort Services
	```

## Run on all nodes of the cluster as root user
#### Disable SWAP
You MUST disable swap in order for the kubelet to work properly 
```
swapoff -a
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

```

## Role: kubernetes
### Tasks:
- `install_packages.yml`: Install necessary packages required for Kubernetes.
- `configure_kernel.yml`: Configure the kernel settings for Kubernetes.
- `install_containerd.yml`: Install containerd runtime.
- `install_runc.yml`: Install runc container runtime.
- `install_cni.yml`: Install the CNI plugins.
- `install_crictl.yml`: Install crictl command line tool.
- `install_kubernetes.yml`: Install Kubernetes binaries.
- `initialize_cluster.yml`: Initialize the Kubernetes cluster.
- `install_weave.yml`: Install the Weave network plugin.
### Templates:
- `crictl.yaml.j2`: Template for the crictl configuration file.
### Files:
- `files/`: Directory to include additional files required for Kubernetes.
### Handlers:
- `handlers/main.yml`: Handlers to restart Kubernetes services.
### Vars:
- `vars/main.yml`: Variables used in the playbook.
### Defaults:
- `defaults/main.yml`: Default values for the variables.
### Meta:
- `meta/main.yml`: Meta information about the playbook.
## Usage
To use this playbook, first make sure you have Ansible installed on your local machine. Then, clone this repository to your local machine.

bash
```
git clone https://github.com/username/ansible-kubernetes.git
```
After cloning the repository, navigate to the ansible-kubernetes directory and create an inventory file. This file should contain the IP addresses or hostnames of the servers you want to install Kubernetes on.

Next, update the variables in `vars/main.yml` to match your environment. You can also update the default values in `defaults/main.yml` if needed.

Finally, run the playbook with the following command:

```
ansible-playbook -i inventory.ini roles/kubernetes/tasks/main.yml

```
This will run the entire Kubernetes installation playbook. If you want to run only specific tasks, you can specify the task file name instead of main.yml.

### Credits
This playbook is based on the Kubernetes installation guide provided by the official Kubernetes documentation.

