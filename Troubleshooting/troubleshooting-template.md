# FILE: troubleshooting/troubleshooting-template.md

# Kubernetes Troubleshooting Template

Reusable troubleshooting structure for documenting Kubernetes operational incidents.

---

# Problem

Brief description of the issue.

Example:

```txt
Cluster initialization failed during kubeadm init
```

---

# Symptoms

Describe:
- error messages
- failing services
- abnormal behavior
- system impact

Example:

```txt
[ERROR Port-10250]: Port is in use
```

---

# Investigation

Document:
- commands used
- logs inspected
- diagnostic process
- findings discovered

Example commands:

```bash
kubectl get pods
journalctl -u kubelet -xe
ss -tulpn
```

---

# Root Cause

Explain:
- exact technical reason
- conflicting service
- configuration issue
- infrastructure failure

Example:

```txt
Existing Kubernetes runtime occupied required ports
```

---

# Fix

Document all recovery steps.

Examples:

```bash
sudo kubeadm reset -f
sudo systemctl restart containerd
```

---

# Verification

Validate recovery.

Examples:

```bash
kubectl get nodes
kubectl get pods -A
```

Expected:
- nodes Ready
- Pods Running
- services operational

---

# Result

Describe final outcome.

Example:

```txt
Cluster restored successfully and worker nodes rejoined
```

---

# Lessons Learned

Document:
- prevention methods
- operational insights
- infrastructure improvements
- future recommendations

Example:

```txt
Always clean old Kubernetes state before rebuilding clusters
```