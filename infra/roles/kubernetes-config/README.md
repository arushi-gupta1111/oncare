# Kubernetes Master Node Setup Role
This Ansible role automates the setup of a Kubernetes master node using `kubeadm`, including containerd configuration, kubelet setup, Flannel network installation, and ingress controller deployment.
## Role Tasks Overview
The role performs the following tasks:
1. **Create containerd directory** - Ensures `/etc/containerd` exists with proper permissions.
2. **Generate default containerd configuration** - Creates the default `config.toml` for containerd.
3. **Restart containerd service** - Restarts and enables the `containerd` service.
4. **Enable kubelet service** - Starts and enables the `kubelet` service.
5. **Initialize Kubernetes cluster** - Initializes the Kubernetes cluster using `kubeadm init` with a configurable pod network CIDR.
6. **Configure `.kube` directories** - Sets up `.kube` directories and copies `admin.conf` for both `devops` and `jenkins` users.
7. **Load br_netfilter kernel module** - Ensures `br_netfilter` is loaded for networking.
8. **Ensure kernel modules load at boot** - Adds `br_netfilter` to `/etc/modules-load.d/k8s.conf`.
9. **Set Kubernetes sysctl parameters** - Configures required sysctl settings for networking and packet forwarding.
10. **Apply sysctl parameters** - Runs `sysctl --system` to apply changes.
11. **Install Flannel CNI network plugin** - Deploys Flannel network plugin for pod networking.
12. **Remove all taints from the node** - Removes default `NoSchedule` taints from the master node to allow scheduling pods.
13. **Install ingress-nginx controller** - Installs the `ingress-nginx` controller if not already present.
## Role Variables
- `pod_network_cidr` - The CIDR block for the pod network (required by Flannel). Example:
```yaml
pod_network_cidr: "10.244.0.0/16"
