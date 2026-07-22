## 🚀 K3s Installation

Follow the steps below to install and configure K3s on your system.

### 1. Prepare the System

Disable the firewall and adjust SELinux settings:

```bash
systemctl disable ufw
```

### 2. Install K3s

Run the official installation script:

```bash
curl -sfL https://get.k3s.io | sh -
```

### 3. Configure kubectl Access

Set up your kubeconfig file:

```bash
mkdir -p $HOME/.kube
cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
chmod 600 $HOME/.kube/config
```

### 3. Verify installation

Check that your node is up and running:

```bash
root@chris-NUC:~# kubectl get nodes
NAME        STATUS   ROLES           AGE   VERSION
chris-nuc   Ready    control-plane   32h   v1.36.2+k3s1
root@chris-NUC:~#
```