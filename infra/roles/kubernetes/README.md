# Kubernetes and Helm Installation Role
This Ansible role automates the installation of Kubernetes tools (`kubeadm`, `kubelet`, `kubectl`) and Helm on Debian/Ubuntu-based systems, including adding the Kubernetes APT repository and holding package versions.

## Role Tasks Overview
The role performs the following tasks:

1. **Create Kubernetes keyring directory** - Ensures `/etc/apt/keyrings` exists with proper permissions.
2. **Download Kubernetes GPG key** - Downloads the official GPG key for the Kubernetes repository.
3. **Add Kubernetes APT repository** - Adds the stable Kubernetes APT repository using the downloaded key.
4. **Install Kubernetes tools** - Installs `kubeadm`, `kubelet`, and `kubectl`.
5. **Hold Kubernetes packages** - Prevents automatic updates for installed Kubernetes packages.
6. **Install Helm** - Installs Helm using the official Helm installation script.

## Requirements
- Ansible 2.9 or later
- Debian/Ubuntu-based system
- `curl` installed on the target machine

## Usage
Example playbook using this role:

```yaml
- hosts: k8s-nodes
  become: yes
  roles:
    - kubernetes-helm-install
