# FILE: troubleshooting/case-1-kubeadm-port-conflict.md

# Troubleshooting Case 1 — kubeadm Port Conflict

Documenting a Kubernetes control-plane initialization failure caused by existing services occupying required Kubernetes ports.

---

# Problem

Kubernetes cluster initialization failed during:

```bash
kubeadm init
```

The control plane could not start successfully.

---

# Symptoms

During initialization, kubeadm returned multiple port conflict errors:

```txt
[ERROR Port-10259]: Port 10259 is in use
[ERROR Port-10257]: Port 10257 is in use
[ERROR Port-10250]: Port 10250 is in use
```

Additional observations:
- kubeadm initialization stopped
- API server failed to start
- control-plane setup was incomplete

---

# Investigation

Checked active listening ports:

```bash
sudo ss -tulpn | grep -E '10250|10257|10259'
```

Result showed existing Kubernetes-related process:

```txt
kubelite process running
```

Verified process details:

```bash
ps -fp <PID>
```

Investigated installed Kubernetes distributions:

```bash
snap list | grep microk8s
```

---

# Root Cause

An existing MicroK8s installation was still running background Kubernetes services.

The `kubelite` process occupied critical Kubernetes control-plane ports required by kubeadm.

This created conflicts during cluster initialization.

---

# Fix

Stopped MicroK8s services:

```bash
sudo microk8s stop
```

Removed MicroK8s installation:

```bash
sudo snap remove microk8s
```

Cleaned old Kubernetes state:

```bash
sudo rm -rf /etc/kubernetes
sudo rm -rf /var/lib/kubelet
sudo rm -rf /var/lib/etcd
```

Restarted container runtime:

```bash
sudo systemctl restart containerd
```

Re-ran cluster initialization:

```bash
sudo kubeadm init \
--apiserver-advertise-address=192.168.40.131 \
--pod-network-cidr=10.244.0.0/16
```

---

# Verification

Checked cluster nodes:

```bash
kubectl get nodes
```

Checked system Pods:

```bash
kubectl get pods -n kube-system
```

---

# Result

After cleanup and reinitialization:

- Kubernetes control plane started successfully
- kubeadm init completed successfully
- kube-system Pods became operational
- worker nodes were able to join cluster

---

# Lessons Learned

- Existing Kubernetes distributions can conflict with kubeadm
- Port conflicts are common during reinstallation
- Old cluster state must be fully removed before rebuilding
- Infrastructure cleanup is critical during troubleshooting