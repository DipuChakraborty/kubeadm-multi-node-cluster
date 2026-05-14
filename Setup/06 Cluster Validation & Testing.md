# 06 - Cluster Validation & Testing

Validate Kubernetes cluster functionality after setup.

This step confirms that:
- cluster components are healthy
- networking works correctly
- workloads can run successfully

---

# Purpose

The goal of this step is to:

- verify cluster health
- test workload deployment
- validate networking
- confirm node communication
- ensure Kubernetes is operational

---

# Step 1 — Check Cluster Nodes

Verify node status:

```bash
kubectl get nodes -o wide
```

Expected:
- all nodes show Ready
- internal IP addresses visible
- control plane operational

Example:

```txt
NAME         STATUS   ROLES           VERSION
k8s-master   Ready    control-plane   v1.35.x
node1u       Ready    <none>          v1.35.x
node2u       Ready    <none>          v1.35.x
```

---

# Why This Step Is Required

This confirms:
- kubelet communication
- API server accessibility
- cluster registration success

---

# Step 2 — Verify kube-system Pods

Check system namespace:

```bash
kubectl get pods -n kube-system
```

Verify:
- CoreDNS
- kube-proxy
- calico-node
- calico-kube-controllers

Expected:
- STATUS = Running

---

# Why This Step Is Required

kube-system contains critical infrastructure services.

If these Pods fail:
- networking breaks
- DNS fails
- workloads become unstable

---

# Step 3 — Deploy Test Application

Create nginx deployment:

```bash
kubectl create deployment nginx --image=nginx
```

Check deployment:

```bash
kubectl get deployments
```

Check Pods:

```bash
kubectl get pods -o wide
```

---

# Why This Step Is Required

This validates:
- scheduler functionality
- container runtime
- Pod creation
- node workload distribution

---

# Step 4 — Expose Application

Expose deployment using NodePort:

```bash
kubectl expose deployment nginx \
--port=80 \
--type=NodePort
```

Check service:

```bash
kubectl get svc
```

---

# Why This Step Is Required

This validates:
- Kubernetes service networking
- kube-proxy rules
- external accessibility

---

# Step 5 — Test Namespace Isolation

Create namespace:

```bash
kubectl create namespace webapp
```

Deploy test Pod:

```bash
kubectl run test-nginx \
--image=nginx \
-n webapp
```

Verify namespace Pods:

```bash
kubectl get pods -n webapp
```

---

# Why This Step Is Required

Namespaces provide:
- workload isolation
- resource separation
- multi-environment organization

---

# Step 6 — Verify Cluster Networking

Run:

```bash
kubectl get pods -o wide
```

Verify:
- Pods receive IP addresses
- Pods communicate across nodes
- Calico networking functions properly

---

# Troubleshooting

## Issue: Pods Stuck in Pending

Possible causes:
- node NotReady
- CNI networking issue
- insufficient resources

Check:

```bash
kubectl describe pod <pod-name>
```

---

# Verification Commands

## Nodes

```bash
kubectl get nodes
```

## System Pods

```bash
kubectl get pods -n kube-system
```

## Services

```bash
kubectl get svc
```

## Deployments

```bash
kubectl get deployments
```

---

# Final Result

After completing validation:

- Kubernetes cluster is fully operational
- workloads deploy successfully
- networking functions correctly
- worker nodes schedule Pods
- services are accessible

The cluster is now ready for application deployment and future DevOps integrations.

---

# Project Outcome

Successfully built and validated:
- self-managed Kubernetes cluster
- multi-node infrastructure
- container runtime integration
- cluster networking
- workload scheduling
- operational troubleshooting workflow