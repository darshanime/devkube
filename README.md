<p align="center"><img src="./logo.jpg"></p>
<p align="center"><b>Bootstrap a Kubernetes cluster with custom binaries. No Kubeadm!</b></p>
 
## Devkube

Currently, a lot of Kubernetes bootstrappers are black boxes which don't provide a lot of flexibility on how the cluster is set up. Devkube changes that. Based off of Kelsey's [KTHW](https://github.com/kelseyhightower/kubernetes-the-hard-way/), it wires together a cluster with custom binaries, complete with Flannel and CoreDNS.


[![devkube-demo](https://asciinema.org/a/221344.svg)](https://asciinema.org/a/221344?autoplay=1&speed=2)


## Features

- **Use custom binaries for each component (`kube-scheduler`, `kube-api-server`, `kubelet` etc).** 
  - The `variables.yml` file has links for the components.
  - This is useful if you are developing a new Kubernetes feature and wish to test the change out on a real cluster
- **Change the flags on any component**
  - Update the config file for the component and run the relevant playbook to deploy the changes
- **Add new nodes**
  - Create the VM, run the relevant playbooks and the node should automatically join the cluster
- **Use cluster for testing manifests etc**
  - Since the cluster can be created in a few minutes on bare VMs, devkube can be used to quickly get clusters up and down for testing purposes
  
## Getting started

- Provision the VMs on your favorite cloud provider
  - The VMs should be running Ubuntu 18.04 and be able to communicate with each other via private IPs (on all ports)
  - The master node should have the `6443` port open for the `api-server`.
  
- Download the required dependencies on your machine
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) for running the playbooks
  - [cfssl](https://github.com/cloudflare/cfssl) for PKI
  
- Edit the `hosts.ini` file
  - Enter the public ip, the ssh-able username and the private ip of the server
  - Make sure the server is ssh-able with the command `ssh <user>@<public-ip>` and the user has root access
  - The first server (`k8s-node-1`) will become the master and all the other nodes will join in as workers
  
- Run the `play.yml` playbook
  - `ansible-playbook play.yml -i hosts.ini`
  

## How does it work

Some of the main files are:

  - `variables.yml`
    - This file has the locations for the binaries of the various components, and a download flag for each
  - `root_certs.yml`
    - This sets up the root CA and generates the public and signing key for it
  - `certs.yml`
    - This sets up all the certificates for the master and worker node components and transfers them to the servers
  - `control_plane.yml`
    - This downloads the control plane binaries and bootstraps the control plane
  - `workers.yml`
    - This downloads the worker node binaries and bootstraps the worker nodes.
    - It also downloads installs [flannel](https://github.com/coreos/flannel) as the CNI network plugin and adds [CoreDNS](https://coredns.io/) for DNS.
    
    
## How do I...

- Add a new node to the existing cluster
  - Run `certs.yml` to generate the certs for the new node and transfer them to the server
  - Run `workers.yml` to install the binaries and get the node up. 
  
- Change the flag on the `api-server`
  - edit the `kube-apiserver.service.j2` file and run the `control_plane.yml` playbook.
  
- Change the kubelet binary
  - update the `variables.yml` file with the new binary endpoint
  - run `workers.yml` playbook
  
- Avoid downloading worker node binaries
  - this might be required if for example you change a flag on kubelet and don't need a fresh download of binaries
  - run `ansible-playbook workers.yml -i hosts.ini --skip-tags "downloads"`
  

## Contributing

Please file an issue if you face any problems. Better still, help fix it and make a PR!
