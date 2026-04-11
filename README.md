# 🏠 Homelab-DAM

> A personal homelab running on three physical machines, a Proxmox cluster with Kubernetes on top. Everything is a work in progress, continuously expanded and improved over time.

---

## 📐 Infrastructure Overview

### Hardware

| Node | CPU | RAM | Storage (/) | Role |
|------|-----|-----|-------------|------|
| `pve` | Intel Core i5-6500 @ 3.20GHz (4 cores) | 16 GiB | 200 GiB | Primary — K8s controller host |
| `pve02` | Intel Core i5-6500 @ 3.20GHz (4 cores) | 32 GiB | 2200 GiB | Secondary — K8s worker host |
| `pve03` | Intel Core i5-5200U @ 2.20GHz (4 cores) | 6 GiB | 500 GiB | Tertiary — K8s worker host |

<img width="828" height="975" alt="image" src="https://github.com/user-attachments/assets/298eebb6-9a0b-4423-8473-a4d4872b0c5c" />

### Software Stack

| Component | Details |
|-----------|---------|
| Hypervisor | Proxmox VE 9.1.7 |
| Kernel | Linux 6.17.13-2-pve (pve / pve02), 6.14.8-2-pve (pve03) |
| Orchestration | Kubernetes (1 controller + 5 worker nodes) |
| Distributed Storage | Longhorn |
| Load Balancer | MetalLB (bare-metal LoadBalancer) |
| Ingress | NGINX Ingress Controller |
| VPN | WireGuard |

All Kubernetes pod configurations can be found in the [`/kubernetes`](./kubernetes) directory.

---

## ⚙️ Services

☸️ Kubernetes
A self-hosted Kubernetes cluster managed through Proxmox, consisting of one controller node and eight worker nodes spread across all three physical hosts. The cluster is the backbone of the homelab and runs the majority of all services.
The cluster is set up using kubeadm with a dedicated controller VM and worker nodes provisioned as Proxmox VMs. Storage is handled by Longhorn, load balancing by MetalLB, and external traffic routing by the NGINX Ingress Controller.
<img width="1656" height="317" alt="image" src="https://github.com/user-attachments/assets/6b98cb24-876c-4aed-a923-1c531bf15782" />

>📖 Tutorial: A step-by-step guide on how I set up the Kubernetes cluster on Proxmox including VM provisioning, kubeadm initialization, joining worker nodes, and installing core components will be added here.

---

### 🌐 Homepage
A custom homelab dashboard running on Kubernetes. Provides a unified entry point to all services, displaying live system stats (CPU, RAM), quick-launch tiles for each service, and useful external links.
<img width="1813" height="586" alt="image" src="https://github.com/user-attachments/assets/966cb9b3-6f0e-4e4c-9cfe-303c3d02e9f3" />

Pod configuration can be found in the [`/kubernetes/homepage`](./kubernetes/homepage) directory.

---

### ☁️ Nextcloud
A fully self-hosted cloud storage and file synchronization platform, a private alternative to Dropbox. Used to sync files between phone and home computer without relying on any third-party cloud provider. Also supports contacts, calendars, notes, and photo backups.

Pod configuration can be found in the [`/kubernetes/nextcloud`](./kubernetes/nextcloud) directory.

---

### 💪 Wger
A self-hosted workout management application for planning, scheduling, and tracking training sessions. Full gym log with support for custom exercise definitions, workout routines, training calendars, and progress tracking. Full data ownership with no subscription required.

Pod configuration can be found in the [`/kubernetes/wger`](./kubernetes/wger) directory.

---
### 🐄 Longhorn
A cloud-native distributed block storage system for Kubernetes. Provides persistent storage for all stateful workloads in the cluster with built-in replication across nodes, snapshots, and backup support.
<img width="1500" height="300" alt="image" src="https://github.com/user-attachments/assets/c56dcbbf-2b8a-4d10-8e34-585896547fe0" />

Pod configuration can be found in the [`/kubernetes/longhorn`](./kubernetes/longhorn) directory.

---

### 📊 Pulse
A lightweight monitoring tool used to keep an eye on the health of containers and VMs running outside the Kubernetes cluster. Provides an at-a-glance overview of resource usage, uptime, and service status — complementing the Kubernetes Dashboard for infrastructure not directly managed by Kubernetes.

Runs as a standalone LXC container on `pve`.

---

### 🔒 WireGuard
A modern, lightweight VPN that allows secure remote access to the homelab from anywhere. Used to tunnel into the home network when away, giving full access to all internal services as if on the local network.

Runs as a standalone LXC container on `pve`, outside of Kubernetes, for maximum reliability.

---

### 🌐 MetalLB
A bare-metal load balancer for Kubernetes. Since the cluster runs on physical hardware without a cloud provider, MetalLB provides `LoadBalancer`-type service support by assigning IP addresses from a configured address pool on the local network.

Configuration can be found in the [`/kubernetes/metallb`](./kubernetes/metallb) directory.

---

### 🔀 NGINX Ingress Controller
Manages external HTTP/HTTPS access to services running inside the Kubernetes cluster. Works in tandem with MetalLB to route traffic from the local network to the correct pods based on hostname and path rules.

Configuration can be found in the [`/kubernetes/ingress`](./kubernetes/ingress) directory.

---

## 📌 Notes

- WireGuard and Pulse run as LXC containers directly on Proxmox for reliability and low overhead.
- All nodes run Proxmox VE with the non-production repository enabled for latest updates.
