# 03 - Kubernetes Installation

Install Kubernetes core components on all cluster nodes.

This step installs:
- kubeadm
- kubelet
- kubectl

These tools are required to initialize and manage the Kubernetes cluster.

---

# Purpose

The goal of this step is to:

- Install Kubernetes binaries
- Configure Kubernetes repositories
- Prepare nodes for cluster creation
- Enable kubelet service

This setup must be completed on:
- Master Node
- Worker Node 1
- Worker Node 2

---

# Kubernetes Components

## kubeadm
Used to initialize and manage Kubernetes clusters.

## kubelet
Node agent responsible for managing Pods and containers.

## kubectl
Command-line tool used to interact with Kubernetes cluster.

---

# Step 1 — Install Dependencies

Install required packages:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gpg
```

---

# Step 2 — Add Kubernetes Repository Key

Create keyring directory:

```bash
sudo mkdir -p /etc/apt/keyrings
```

Download Kubernetes signing key:

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.35/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

---

# Why This Step Is Required

The repository key verifies:
- package authenticity
- secure package downloads
- trusted Kubernetes packages

---

# Step 3 — Add Kubernetes Repository

Add official Kubernetes repository:

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.35/deb/ /' | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Update package index:

```bash
sudo apt update
```

---

# Step 4 — Install Kubernetes Tools

Install Kubernetes components:

```bash
sudo apt install -y kubelet kubeadm kubectl
```

---

# Step 5 — Hold Package Versions

Prevent automatic upgrades:

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

---

# Why This Step Is Required

Kubernetes version mismatches can cause:
- cluster instability
- node incompatibility
- API communication failures

Version locking improves stability.

---

# Step 6 — Enable kubelet

Enable kubelet service:

```bash
sudo systemctl enable --now kubelet
```

---

# Why kubelet May Show Errors

At this stage kubelet may continuously restart.

This is normal because:
- cluster is not initialized yet
- kubelet has not connected to control plane

---

# Verification

Check versions:

```bash
kubeadm version
kubectl version --client
kubelet --version
```

Check kubelet status:

```bash
systemctl status kubelet
```

---

# Troubleshooting

## Issue: Repository Not Found

Run:

```bash
sudo apt update
```

Verify repository file:

```bash
cat /etc/apt/sources.list.d/kubernetes.list
```

---

# Final Result

After completing this step:

- Kubernetes binaries are installed
- kubelet service is active
- nodes are ready for cluster initialization

---

# Next Step

Proceed to:

```txt
04-master-initialization.md
```