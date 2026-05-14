# Kubernetes Cluster Deployment & Operations Lab

A self-managed multi-node Kubernetes cluster built using kubeadm on Ubuntu Linux.

This project focuses on real-world Kubernetes administration, cluster setup, networking, troubleshooting, and operational debugging using open-source tooling without managed cloud services.

---

# Project Overview

This lab environment was created to understand Kubernetes infrastructure from the ground up, including:

- Kubernetes cluster initialization
- Worker node integration
- Container runtime configuration
- Pod networking
- Linux system preparation
- Cluster troubleshooting
- Workload deployment and validation

The project documents both successful setup procedures and real operational failures encountered during deployment.

---

# Infrastructure Architecture

```txt
                    +----------------------+
                    |   Control Plane      |
                    |     k8s-master       |
                    |   192.168.40.131     |
                    +----------+-----------+
                               |
          -----------------------------------------
          |                                       |
+----------------------+         +----------------------+
|     Worker Node 1    |         |     Worker Node 2    |
|       node1u         |         |       node2u         |
|   192.168.40.132     |         |   192.168.40.133     |
+----------------------+         +----------------------+
```

---

# Technologies Used

## Kubernetes Components
- Kubernetes
- kubeadm
- kubelet
- kubectl

## Container Runtime
- containerd

## Networking
- Calico CNI

## Operating System
- Ubuntu 24.04 LTS

## Linux & Infrastructure
- Systemd
- Linux Networking
- Bash

---

# Key Features

- Multi-node Kubernetes cluster
- Manual kubeadm-based deployment
- Calico pod networking
- containerd runtime integration
- Linux kernel and sysctl configuration
- Namespace management
- Pod scheduling validation
- Cluster troubleshooting documentation
- Infrastructure validation testing

---

# Repository Structure

```txt
kubernetes-cluster-lab/
│
├── README.md
│
├── architecture/
│   ├── cluster-diagram.png
│   ├── network-flow.png
│   └── kubernetes-components.png
│
├── setup/
│   ├── 01-node-preparation.md
│   ├── 02-container-runtime-containerd.md
│   ├── 03-kubernetes-installation.md
│   ├── 04-master-initialization.md
│   ├── 05-worker-join.md
│   └── 06-validation.md
│
├── manifests/
│   ├── nginx-deployment.yaml
│   ├── nginx-service.yaml
│   ├── namespace-webapp.yaml
│   ├── test-pod.yaml
│   └── sample-app.yaml
│
├── troubleshooting/
│   ├── case-1-kubeadm-port-conflict.md
│   ├── case-2-worker-node-join-failure.md
│   └── troubleshooting-template.md
│
├── screenshots/
│   ├── nodes-ready.png
│   ├── pods-running.png
│   ├── nginx-deployment.png
│   ├── kube-system-pods.png
│   └── cluster-wide-view.png
│
├── scripts/
│   ├── install-k8s.sh
│   ├── reset-node.sh
│   └── join-cluster.sh
│
└── video/
    └── demo-link.txt
```

---

# Cluster Setup Workflow

## 1. Node Preparation
- Configure hostnames
- Update `/etc/hosts`
- Disable swap
- Load kernel modules
- Configure sysctl networking

## 2. Container Runtime Setup
- Install containerd
- Configure SystemdCgroup
- Enable runtime services

## 3. Kubernetes Installation
- Install kubeadm
- Install kubelet
- Install kubectl

## 4. Control Plane Initialization
- Initialize Kubernetes cluster
- Configure kubectl access
- Deploy Calico networking

## 5. Worker Node Integration
- Join worker nodes
- Validate cluster communication
- Verify node readiness

## 6. Validation & Testing
- Deploy nginx workload
- Verify pod scheduling
- Test namespaces and services

---

# Cluster Validation

## Check Nodes

```bash
kubectl get nodes -o wide
```

## Check System Pods

```bash
kubectl get pods -n kube-system
```

## Deploy Test Workload

```bash
kubectl create deployment nginx --image=nginx
kubectl get pods -o wide
```

## Expose Service

```bash
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get svc
```

---

# Troubleshooting Experience

This project includes documentation for real Kubernetes operational issues encountered during deployment.

## Issues Resolved

### kubeadm Initialization Failure
- Kubernetes port conflicts
- Existing MicroK8s interference
- Control-plane startup failures

### Worker Node Join Failures
- kubelet configuration conflicts
- Stale Kubernetes state
- Node registration issues

### Container Runtime Problems
- containerd configuration mismatch
- cgroup driver alignment
- Runtime restart validation

---

# Skills Demonstrated

## Kubernetes Administration
- Cluster lifecycle management
- Node operations
- Namespace management
- Workload scheduling

## Linux System Engineering
- Systemd service management
- Kernel module configuration
- Sysctl networking
- Process troubleshooting

## Infrastructure Troubleshooting
- kubeadm debugging
- kubelet recovery
- Runtime diagnostics
- Cluster state cleanup

## Networking
- Pod networking
- Cluster communication
- CNI configuration
- Service exposure

---

# Lessons Learned

- Kubernetes is highly dependent on proper runtime configuration
- Existing Kubernetes distributions can conflict with kubeadm clusters
- kubeadm reset does not fully clean cluster networking state
- Calico networking requires proper pod CIDR alignment
- Systemd cgroup configuration is critical for kubelet stability

---

# Future Improvements

Planned enhancements include:

- Laravel application deployment
- Python application deployment
- Ingress Controller setup
- Persistent Volume integration
- CI/CD pipeline implementation
- Helm package management
- Prometheus monitoring
- Grafana dashboards

---

# Screenshots

Cluster screenshots available in:

```txt
/screenshots
```

Includes:
- Node status
- kube-system pods
- Deployment validation
- Namespace workloads
- Service exposure

---

# Demo Video

Cluster walkthrough video available in:

```txt
/video/demo-link.txt
```

The demo includes:
- Cluster architecture overview
- Kubernetes validation
- Workload deployment
- Troubleshooting explanation
- Repository walkthrough

---

# Final Outcome

Successfully deployed and operated a functional multi-node Kubernetes cluster capable of running containerized workloads across distributed worker nodes.

This project demonstrates practical experience with:
- Kubernetes administration
- Linux infrastructure
- Cluster troubleshooting
- Container runtime management
- Infrastructure validation
- Operational debugging