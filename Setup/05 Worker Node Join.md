# 05 - Worker Node Join

Connect worker nodes to the Kubernetes control plane.

This step expands the cluster by attaching worker nodes where workloads and Pods will run.

---

# Purpose

The goal of this step is to:

- Join worker nodes to cluster
- Enable workload distribution
- Expand cluster resources
- Validate node communication

This step is performed on:
- Worker Node 1
- Worker Node 2

---

# Worker Node Role

Worker nodes are responsible for:
- running Pods
- executing containers
- communicating with control plane
- handling workloads

Main components:
- kubelet
- kube-proxy
- containerd

---

# Step 1 — Verify Prerequisites

Before joining:
- Kubernetes packages must be installed
- containerd must be running
- swap must be disabled
- network connectivity must work

Verify containerd:

```bash
systemctl status containerd
```

Verify kubelet:

```bash
systemctl status kubelet
```

---

# Step 2 — Reset Existing Cluster State (If Needed)

If node was previously joined or failed:

```bash
sudo kubeadm reset -f
```

Remove leftover Kubernetes files:

```bash
sudo rm -rf /etc/kubernetes/
sudo rm -rf /var/lib/kubelet/
sudo rm -rf /etc/cni/net.d/
```

Restart services:

```bash
sudo systemctl daemon-reload
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

---

# Why This Step Is Required

Old cluster state can cause:
- kubelet conflicts
- certificate mismatches
- join failures
- stale node configuration

Resetting ensures clean node registration.

---

# Step 3 — Join Worker Node

Run the join command generated from master node.

Example:

```bash
sudo kubeadm join 192.168.40.131:6443 \
--token <token> \
--discovery-token-ca-cert-hash sha256:<hash>
```

---

# What Happens During Join

During join process:
- kubelet registers node
- certificates are validated
- node connects to API server
- cluster trust is established

---

# Step 4 — Verify Node from Master

Run on master node:

```bash
kubectl get nodes -o wide
```

Expected output:

```txt
NAME         STATUS   ROLES           AGE
k8s-master   Ready    control-plane
node1u       Ready    <none>
node2u       Ready    <none>
```

---

# Step 5 — Verify Pod Scheduling

Deploy test workload:

```bash
kubectl create deployment nginx --image=nginx
```

Check Pod placement:

```bash
kubectl get pods -o wide
```

Verify Pods are scheduled across worker nodes.

---

# Troubleshooting

## Issue: kubelet.conf already exists

Example error:

```txt
[ERROR FileAvailable--etc-kubernetes-kubelet.conf]
```

Cause:
- leftover kubelet state
- previous failed join attempt

Fix:
- reset node completely
- remove old configuration

---

# Verification

Check cluster nodes:

```bash
kubectl get nodes
```

Expected:

```txt
All nodes in Ready state
```

---

# Final Result

After completing this step:

- Worker nodes successfully join cluster
- Workloads can run across multiple nodes
- Cluster becomes distributed
- Scheduling functionality is operational

---

# Next Step

Proceed to:

```txt
06-validation.md
```