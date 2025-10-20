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
### Network Configuration ✅
### VMnet2 Setup:
- Network: 192.168.20.0/24
- Type: Host-only
- DHCP: Disabled
### Verification:
```
Powershell

# On Windows host
ipconfig | findstr "VMware"
```
## 💻 Virtual Machines Deployed
### 1. VyOS Router (Portal-VyOS) ✅
***Configuration:***
- vCPU: 1
- RAM: 1 GB
- Disk: 2 GB
- NIC1: VMnet2 (192.168.20.1)
- NIC2: NAT (Internet)
***Services:***
- NAT/Masquerading
- DNS Forwarding
- Gateway
### 2. AlmaLinux (File-AlmaLinux) ✅
***Configuration:***
- vCPU: 4
- RAM: 2 GB
- Disk: 40 GB
- NIC: VMnet2 (192.168.20.10)
***Services:***
- dnf
- hostnamectl
- ping
- systemctl
- firewalld
### 3. AlmaLinux (Web-AlmaLinux) ✅
***Configuration:***
- vCPU: 4
- RAM: 2 GB
- Disk: 40 GB
- NIC: VMnet2 (192.168.20.11)
***Services:***
- dnf
- hostnamectl
- ping
- systemctl
- firewalld
### 4. Windows Server 2022 (DC1-WindowsServer2022) ✅
***Configuration:***
- vCPU: 8
- RAM: 4 GB
- Disk: 60 GB
- NIC: VMnet2 (192.168.20.50)
***Services:***
- Network Settings
- Server Manager

### 5. Windows Server 2025 (DC2-WindowsServer2025) ✅
***Configuration:***
- vCPU: 8
- RAM: 4 GB
- Disk: 60 GB
- NIC: VMnet2 (192.168.20.51)
***Services:***
- Network Settings
- Server Manager
### 6. Kali (AW-Kali) ✅
***Configuration:***
- vCPU: 8
- RAM: 3 GB
- Disk: 50 GB
- NIC: VMnet2 (192.168.20.100)
### 7. Ubuntu (AW-Ubuntu) ✅
***Configuration:***
- vCPU: 
- RAM: