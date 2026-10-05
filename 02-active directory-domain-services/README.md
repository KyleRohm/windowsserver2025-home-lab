# Active Directory Domain Services Configuration

## Overview

This project documents the installation and configuration of Active
Directory Domain Services (AD DS) on Windows Server 2025.

The Windows Server 2025 virtual machine created during the previous
phase of the home lab will be configured as a Domain Controller.

Active Directory will provide centralized authentication and management
of users, computers, and security groups within the lab environment.

## Objectives

- Configure the Windows Server 2025 server for Active Directory
- Install the Active Directory Domain Services role
- Promote the server to a Domain Controller
- Create a new Active Directory forest and domain
- Configure DNS as part of the Active Directory deployment
- Verify Active Directory functionality
- Create Organizational Units
- Create test user accounts
- Create security groups
- Configure group membership
- Verify domain authentication

---

## Prerequisites

- Windows Server 2025 installed
- Windows Server 2025 configured and updated
- VMware Workstation installed
- Server hostname configured
- Network connectivity verified
- Static IP address configured
- Administrative access to the Windows Server
- Windows Server 2025 configured with the appropriate DNS settings

---

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Operating System | Windows Server 2025 |
| Edition | Standard |
| Installation Type | Desktop Experience |
| Server Name | FileServer01 |
| Active Directory Role | Domain Controller |
| Domain Name | `homelab.local` |
| IP Address | `192.168.88.129` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `192.168.88.2` |
| Preferred DNS | `127.0.0.1` |

---

# 1. Configure Network Settings

Before installing Active Directory Domain Services, the Windows Server
was configured with a static IP address.

Active Directory and DNS require reliable network configuration so that
domain clients can consistently locate the Domain Controller.

### Network Configuration

The following network settings were configured:

- IP Address: `192.168.88.129`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.88.2`
- Preferred DNS: `127.0.0.1`

### Configuration Steps

1. Opened **Windows PowerShell → Typed `ipconfig` → Copied the IPv4 Address automatically assigned by DHCP**.
2. Opened **Settings → Network & internet → Ethernet**.
3. Clicked `Edit` for IP assignnemt.
4. Set configuration to `Manual` and pasted the IP address.
5. Configured the subnet mask.
6. Configured the default gateway.
7. Configured the DNS to the loopback address.
8. Saved the network configuration.

### Verification

![Static IP Configuration]
