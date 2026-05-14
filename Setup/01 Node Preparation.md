

# 01 - Node Preparation

Prepare Ubuntu nodes for Kubernetes installation and cluster communication.

This step configures the Linux operating system to meet Kubernetes requirements before installing kubeadm, kubelet, and container runtime components.

---

# Purpose

The goal of this step is to:

- Prepare Linux nodes for Kubernetes
- Configure networking prerequisites
- Enable kernel-level container networking
- Disable unsupported system settings
- Ensure communication between cluster nodes

This setup must be completed on:
- Master Node
- Worker Node 1
- Worker Node 2

---

# Cluster Nodes

| Hostname   | IP Address      | Role           |
|------------|------------------|----------------|
| k8s-master | 192.168.40.131   | Control Plane  |
| node1u     | 192.168.40.132   | Worker Node    |
| node2u     | 192.168.40.133   | Worker Node    |

---

# Step 1 — Set Hostnames

Each node requires a unique hostname for cluster identification.

## Master Node

```bash
sudo hostnamectl set-hostname k8s-master
```

## Worker Node 1

```bash
sudo hostnamectl set-hostname node1u
```

## Worker Node 2

```bash
sudo hostnamectl set-hostname node2u
```

---

# Why This Step Is Required

Kubernetes identifies cluster nodes using hostnames.

Without unique hostnames:
- node communication becomes inconsistent
- cluster registration issues may occur
- troubleshooting becomes difficult

---

# Step 2 — Configure /etc/hosts

Add all cluster nodes to `/etc/hosts` on every machine.

Edit:

```bash
sudo nano /etc/hosts
```

Add:

```txt
192.168.40.131 k8s-master
192.168.40.132 node1u
192.168.40.133 node2u
```

---

# Why This Step Is Required

This enables hostname-based communication between Kubernetes nodes.

Benefits:
- reliable internal communication
- simplified cluster management
- easier troubleshooting

---

# Step 3 — Disable Swap

Kubernetes requires swap to be disabled.

Disable swap temporarily:

```bash
sudo swapoff -a
```

Disable swap permanently:

```bash
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

Verify:

```bash
free -h
```

---

# Why This Step Is Required

Kubernetes scheduler and kubelet rely on accurate memory management.

Swap can cause:
- unstable pod scheduling
- memory allocation issues
- kubelet startup failures

---

# Step 4 — Load Required Kernel Modules

Load Kubernetes networking modules.

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

Make modules persistent:

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

Verify:

```bash
lsmod | grep br_netfilter
lsmod | grep overlay
```

---

# Why This Step Is Required

These modules enable:
- container filesystem layering
- Kubernetes bridge networking
- iptables processing for containers

Without them:
- pod networking may fail
- cluster communication breaks

---

# Step 5 — Configure Sysctl Networking

Create Kubernetes sysctl configuration:

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

Apply settings:

```bash
sudo sysctl --system
```

Verify:

```bash
sysctl net.ipv4.ip_forward
```

---

# Why This Step Is Required

These settings allow:
- packet forwarding
- pod-to-pod communication
- Kubernetes service networking

Without IP forwarding:
- pods cannot communicate across nodes

---

# Step 6 — Update System Packages

Update Ubuntu packages:

```bash
sudo apt update && sudo apt upgrade -y
```

---

# Why This Step Is Required

Updated packages help:
- reduce compatibility issues
- improve system stability
- ensure latest security patches

---

# Verification

Run these commands on all nodes.

## Verify Hostname

```bash
hostname
```

## Verify Swap Disabled

```bash
free -h
```

## Verify Kernel Modules

```bash
lsmod | grep overlay
lsmod | grep br_netfilter
```

## Verify IP Forwarding

```bash
sysctl net.ipv4.ip_forward
```

Expected output:

```txt
net.ipv4.ip_forward = 1
```

---

# Final Result

After completing node preparation:

- Linux nodes become Kubernetes-ready
- Networking prerequisites are enabled
- System configuration supports container workloads
- Nodes are ready for container runtime installation

---

# Next Step

Proceed to:

```txt
02-container-runtime-containerd.md
```