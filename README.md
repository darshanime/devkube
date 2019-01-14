## Devkube

Bootstrap a Kubernetes cluster with custom binaries and flags without Kubeadm.

Currently, a lot of Kubernetes bootstrappers are black boxes which don't provide a lot of flexibility on how the cluster is set up. Think about how difficult it is to change the flags on the api-server, controller-manager, kubelet etc in your present installation. How about using a custom scheduler? Changing the certificates?

Devkube makes all of that very simple. Based off of Kelsey's KTHW, Devkube is a series of ansible playbooks that sets up the cluster step by step, without kubeadm. 

There are variables for each control plane and worker node binaries. The systemd service files and other configs are files that can be modified as can each task in the playbook. 

### demo

<a href="https://asciinema.org/a/221324?autoplay=1"><img src="https://asciinema.org/a/221324" width="836"/></a>
