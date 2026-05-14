# 04 - Master Node Initialization

Initialize the Kubernetes control plane using kubeadm.

This step creates the Kubernetes cluster and configures the master node.

---

# Purpose

The goal of this step is to:

- Create Kubernetes control plane
- Start API server
- Configure cluster networking
- Enable kubectl management access

This step is performed ONLY on:
- Master Node

---

# Kubernetes Control Plane

The control plane manages:
- cluster state
- scheduling
- networking
- node coordination

Main components:
- kube-apiserver
- etcd
- scheduler
- controller-manager

---

# Step 1 — Initialize Cluster

Run:

```bash
sudo kubeadm init \
--apiserver-advertise-address=192.168.40.131 \
--pod-network-cidr=10.244.0.0/16
```

---

# Why This Step Is Required

kubeadm init:
- creates control plane
- generates certificates
- configures kubelet
- starts Kubernetes components

---

# Step 2 — Configure kubectl Access

Create kube directory:

```bash
mkdir -p $HOME/.kube
```

Copy admin configuration:

```bash
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

Set ownership:

```bash
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

# Why This Step Is Required

kubectl requires authentication configuration to communicate with the API server.

Without this:
- kubectl commands will fail

---

# Step 3 — Verify Cluster

Check nodes:

```bash
kubectl get nodes
```

Initially node may show:

```txt
NotReady
```

This is expected before installing network plugin.

---

# Step 4 — Install Calico Network Plugin

Apply Calico manifest:

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

---

# Why This Step Is Required

Kubernetes requires a CNI plugin for:
- pod networking
- pod-to-pod communication
- service networking

Without CNI:
- nodes remain NotReady
- pods cannot communicate

---

# Step 5 — Verify System Pods

Check kube-system namespace:

```bash
kubectl get pods -n kube-system
```

Verify:
- CoreDNS
- kube-proxy
- calico-node
- calico-kube-controllers

---

# Step 6 — Generate Join Command

Create worker join command:

```bash
kubeadm token create --print-join-command
```

Save the generated command.

---

# Troubleshooting

## Issue: Port Already In Use

Check ports:

```bash
sudo ss -tulpn
```

Possible cause:
- existing Kubernetes installation
- MicroK8s conflict

---

# Verification

Check node status:

```bash
kubectl get nodes
```

Expected:

```txt
STATUS: Ready
```

---

# Final Result

After completing this step:

- Kubernetes cluster becomes operational
- Control plane is active
- API server is reachable
- Cluster networking is enabled

---

# Next Step

Proceed to:

```txt
05-worker-join.md
```