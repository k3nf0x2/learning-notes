---
project: Homelab VM Infrastructure
status: 🟢 Complete
date: 2025-10-18
tags:
  - project
  - vmware
  - networking
  - foundation
---
----
# 🖥️ VMware Homelab Infrastructure Setup
## 🎯 Objective Build a complete virtualized lab environment for learning system administration and cybersecurity.
---
## 🏗️ Network Architecture ```
```
Internet
	↓ 
Host Machine (Windows 11)
	↓ 
VMnet2 (192.168.20.0/24 - Host-only)
	↓ 
VyOS Router (192.168.20.1) ← Gateway & NAT
    ↓ 
    ├─> File Server (192.168.20.10)
	├─> Web Server (192.168.20.11) 
	├─> DC1 (192.168.20.50) 
	└─> Analyst Machines
		 ├─> Kali (192.168.20.100) 
		 ├─> Ubuntu (192.168.20.101) 
		 └─> Win10 (192.168.20.102)
```
---
## 📋 Bill of Materials

| **Component**      | **Specification** | **Purpose**      |
| :----------------- | ----------------- | ---------------- |
| Host CPU           | Min. 4 cores      | Run multiple VMs |
| Host RAM           | Min. 16 GB        | 2-4 GB per VM    |
| Host Disk          | Min. 200 GB free  | VM storage       |
| VMware Workstation | v17+              | Hypervisor       |

---
## 🚀 Implementation Steps
### Phase 1: Network Configuration ✅
### VMnet2 Setup:
- 