# 🏠 Homelab-DAM

> A personal homelab running on three physical machines — a Proxmox cluster with Kubernetes on top. Everything is a work in progress, continuously expanded and improved over time.

---

## 📐 Infrastructure Overview

### Hardware

| Node | CPU | RAM | Storage (/) | Role |
|------|-----|-----|-------------|------|
| `pve` | Intel Core i5-6500 @ 3.20GHz (4 cores) | 15.51 GiB | 188.86 GiB | Primary — K8s controller host |
| `pve02` | Intel Core i5-6500 @ 3.20GHz (4 cores) | 31.24 GiB | 69.18 GiB | Secondary — K8s worker host |
| `pve03` | Intel Core i5-5200U @ 2.20GHz (4 cores) | 5.71 GiB | 95.94 GiB | Tertiary — K8s worker host |

### Software Stack

| Component | Details |
|-----------|---------|
| Hypervisor | Proxmox VE 9.1.7 |
| Kernel | Linux 6.17.13-2-pve (pve / pve02), 6.14.8-2-pve (pve03) |
| Orchestration | Kubernetes (1 controller + 8 worker nodes) |
| Distributed Storage | Longhorn |
| Load Balancer | MetalLB (bare-metal LoadBalancer) |
| Ingress | NGINX Ingress Controller |
| VPN | WireGuard |

All Kubernetes pod configurations can be found in the [`/kubernetes`](./kubernetes) directory.

---

## 🖥️ Virtual Machines & Containers

### Node: `pve`

| VM ID | Name | Type | Role |
|-------|------|------|------|
| 100 | wireguard | LXC | WireGuard VPN gateway |
| 101 | pulse | LXC | Monitoring (Pulse) |
| 200 | K8s-ctrl | VM | Kubernetes controller node |
| 201 | K8s-node-1 | VM | Kubernetes worker node |
| 850 | K8s-node-clone | VM | Kubernetes worker node |

### Node: `pve02`

| VM ID | Name | Type | Role |
|-------|------|------|------|
| 203 | K8s-node-2 | VM | Kubernetes worker node |
| 204 | K8s-node-3 | VM | Kubernetes worker node |
| 205 | K8s-node-4 | VM | Kubernetes worker node |
| 103 | K8s-node-clone | VM | Kubernetes worker node |

### Node: `pve03`

| VM ID | Name | Type | Role |
|-------|------|------|------|
| 210 | K8s-node-5 | VM | Kubernetes worker node |
| 102 | K8s-node-clone | VM | Kubernetes worker node |

---

## ⚙️ Services

### 🌐 Homepage
A custom homelab dashboard running on Kubernetes. Provides a unified entry point to all services, displaying live system stats (CPU, RAM), quick-launch tiles for each service, and useful external links.

Pod configuration can be found in the [`/kubernetes/homepage`](./kubernetes/homepage) directory.

---

### ☁️ Nextcloud
A fully self-hosted cloud storage and file synchronization platform — a private alternative to Dropbox. Used to sync files between phone and home computer without relying on any third-party cloud provider. Also supports contacts, calendars, notes, and photo backups.

Pod configuration can be found in the [`/kubernetes/nextcloud`](./kubernetes/nextcloud) directory.

---

### 💪 Wger
A self-hosted workout management application for planning, scheduling, and tracking training sessions. Full gym log with support for custom exercise definitions, workout routines, training calendars, and progress tracking. Full data ownership with no subscription required.

Pod configuration can be found in the [`/kubernetes/wger`](./kubernetes/wger) directory.

---

### 📊 Pulse
A lightweight monitoring tool used to keep an eye on the health of containers and VMs running outside the Kubernetes cluster. Provides an at-a-glance overview of resource usage, uptime, and service status — complementing the Kubernetes Dashboard for infrastructure not directly managed by Kubernetes.

Runs as a standalone LXC container on `pve`.

---

### 🔒 WireGuard
A modern, lightweight VPN that allows secure remote access to the homelab from anywhere. Used to tunnel into the home network when away, giving full access to all internal services as if on the local network.

Runs as a standalone LXC container on `pve`, outside of Kubernetes, for maximum reliability.

---

### 🐄 Longhorn
A cloud-native distributed block storage system for Kubernetes. Provides persistent storage for all stateful workloads in the cluster with built-in replication across nodes, snapshots, and backup support.

Pod configuration can be found in the [`/kubernetes/longhorn`](./kubernetes/longhorn) directory.

---

### 🌐 MetalLB
A bare-metal load balancer for Kubernetes. Since the cluster runs on physical hardware without a cloud provider, MetalLB provides `LoadBalancer`-type service support by assigning IP addresses from a configured address pool on the local network.

Configuration can be found in the [`/kubernetes/metallb`](./kubernetes/metallb) directory.

---

### 🔀 NGINX Ingress Controller
Manages external HTTP/HTTPS access to services running inside the Kubernetes cluster. Works in tandem with MetalLB to route traffic from the local network to the correct pods based on hostname and path rules.

Configuration can be found in the [`/kubernetes/ingress`](./kubernetes/ingress) directory.

---

## 📁 Repository Structure

```
.
├── kubernetes/
│   ├── homepage/
│   ├── nextcloud/
│   ├── wger/
│   ├── longhorn/
│   ├── metallb/
│   └── ingress/
└── README.md
```

---

## 📌 Notes

- All three Proxmox nodes form a cluster with quorum enabled (`Homelab` datacenter).
- The Kubernetes cluster consists of 1 controller node and 8 worker nodes spread across all three Proxmox hosts.
- MetalLB + NGINX Ingress Controller handles all external service exposure within the home network.
- WireGuard and Pulse run as LXC containers directly on Proxmox for reliability and low overhead.
- All nodes run Proxmox VE with the non-production repository enabled for latest updates.
