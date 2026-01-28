## Active Directory Home Lab (Windows Server 2019 + Windows 11 Pro)

This repository documents the design, deployment, troubleshooting, and validation of a fully functional Active Directory environment built in a VMware Workstation Pro home lab.
The goal of this project is demonstrate real-world Windows Server, Active Directory, DNS, and client management skills aligned with entry-level IT, Help Desk, and Networking roles.

## Project Overview
This lab simulates a small enterprise Active Directory deployment using modern Microsoft operating systems. It includes a domain controller providing AD DS and DNS services, and a Windows 11 Pro client joined to the domain.

The project emphasizes:
- Correct AD + DNS Configuration
- Client-domain authentication
- Networking and firewall troubleshooting
- Understanding of user vs computer objects
- Enterprise-style documentation and validation

## Lab Architecture

### Virtualization Platform
- Hypervisor: VMWare Workstation Pro
- Network Mode: NAT (VMnet8)

Virtual Machines
| Role | Hostname | Operation Systems | Purpose |
|------|----------|-------------------|---------|
| Domain Controller | DC01 | Windows Server 2019 Standard (Desktop Experience) | AD DS, DNS |
| Client Workstation | CLIENT01 | Windows 11 Pro | Domain-joined endpoint |

### Network Configuration
| Component | Configuration |
|-----------|---------------|
| Subnet | 192.168.61.0/24|
| Gateway | 192.168.61.2 |
| DC01 IP | 192.168.61.132 (Static) |
| DC01 DNS | 127.0.0.1 |
| CLIENT01 DNS | 192.168.61.132 |

### Technologies Used
- Windows Server 2019 Standard Desktop Experience
- Windows 11 Pro
- Active Directory Domain Services (AD DS)
- DNS Server
- VMWare Workstation Pro
- NAT-based virtual networking
- PowerShell and Windows CLI tools