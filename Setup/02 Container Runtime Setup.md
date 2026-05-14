# 02 - Container Runtime Setup (containerd)

Install and configure containerd as the container runtime for Kubernetes.

containerd is responsible for running and managing containers on each Kubernetes node.

---

# Purpose

The goal of this step is to:

- Install container runtime
- Configure Kubernetes-compatible cgroup settings
- Enable container lifecycle management
- Prepare nodes to run Pods

This setup must be completed on:
- Master Node
- Worker Node 1
- Worker Node 2

---

# Why containerd

Kubernetes does not run containers directly.

Kubernetes communicates with a container runtime using the Container Runtime Interface (CRI).

containerd is:
- lightweight
- stable
- CNCF-supported
- production-grade

---

# Step 1 — Install containerd

Update package index:

```bash
sudo apt update
```

Install containerd:

```bash
sudo apt install -y containerd
```

---

# Step 2 — Create Default Configuration

Create containerd configuration directory:

```bash
sudo mkdir -p /etc/containerd
```

Generate default configuration:

```bash
containerd config default | sudo tee /etc/containerd/config.toml
```

---

# Why This Step Is Required

The default configuration file allows customization of:
- runtime settings
- cgroups
- storage drivers
- Kubernetes compatibility

---

# Step 3 — Configure Systemd Cgroup Driver

Open configuration file:

```bash
sudo nano /etc/containerd/config.toml
```

Find:

```txt
SystemdCgroup = false
```

Change to:

```txt
SystemdCgroup = true
```

---

# Why This Step Is Required

Kubernetes kubelet uses systemd for resource management.

If cgroup drivers mismatch:
- kubelet becomes unstable
- nodes may stay NotReady
- pods may fail to start

---

# Step 4 — Restart containerd

Restart service:

```bash
sudo systemctl restart containerd
```

Enable service at boot:

```bash
sudo systemctl enable containerd
```

---

# Step 5 — Verify containerd Status

Check service status:

```bash
systemctl status containerd
```

Verify runtime:

```bash
ctr version
```

Check runtime information:

```bash
ctr info
```

---

# Why This Step Is Required

Verification ensures:
- runtime is active
- configuration applied correctly
- Kubernetes can communicate with runtime

---

# Troubleshooting

## Issue: containerd service fails

Check logs:

```bash
journalctl -u containerd -xe
```

Common causes:
- invalid config.toml syntax
- incorrect cgroup configuration
- corrupted runtime state

---

# Verification

Run:

```bash
systemctl is-active containerd
```

Expected output:

```txt
active
```

---

# Final Result

After completing this step:

- containerd runtime becomes operational
- Kubernetes nodes can run containers
- kubelet can communicate with CRI runtime
- nodes are ready for Kubernetes installation

---

# Next Step

Proceed to:

```txt
03-kubernetes-installation.md
```