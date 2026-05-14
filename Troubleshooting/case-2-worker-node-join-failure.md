# FILE: troubleshooting/case-2-worker-node-join-failure.md

# Troubleshooting Case 2 — Worker Node Join Failure

Documenting a worker node registration failure caused by stale Kubernetes node configuration.

---

# Problem

Worker node failed to join Kubernetes cluster during:

```bash
kubeadm join
```

The node could not register with the control plane.

---

# Symptoms

kubeadm join returned configuration conflict errors:

```txt
[ERROR FileAvailable--etc-kubernetes-kubelet.conf]
```

Additional observations:
- node status unavailable
- kubelet instability
- cluster registration failure

---

# Investigation

Checked kubelet service:

```bash
systemctl status kubelet
```

Checked active kubelet process:

```bash
sudo lsof -i :10250
```

Inspected Kubernetes directories:

```bash
ls /etc/kubernetes
ls /var/lib/kubelet
```

Detected leftover configuration files from previous cluster attempts.

---

# Root Cause

The worker node contained stale Kubernetes configuration and kubelet state from previous failed join operations.

kubeadm detected conflicting node configuration and blocked cluster registration.

---

# Fix

Reset node state completely:

```bash
sudo kubeadm reset -f
```

Removed old Kubernetes files:

```bash
sudo rm -rf /etc/kubernetes/
sudo rm -rf /var/lib/kubelet/
sudo rm -rf /etc/cni/net.d/
```

Restarted services:

```bash
sudo systemctl daemon-reload
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

Rejoined cluster:

```bash
sudo kubeadm join 192.168.40.131:6443 \
--token <token> \
--discovery-token-ca-cert-hash sha256:<hash>
```

---

# Verification

Checked cluster nodes from master:

```bash
kubectl get nodes -o wide
```

Verified node status:

```txt
STATUS = Ready
```

---

# Result

After cleanup and rejoin:

- worker node successfully registered
- kubelet stabilized
- cluster communication restored
- workloads could be scheduled on worker node

---

# Lessons Learned

- kubeadm reset does not remove all Kubernetes state
- stale kubelet configuration causes join failures
- proper cleanup is critical before rejoining nodes
- node registration depends on clean kubelet state