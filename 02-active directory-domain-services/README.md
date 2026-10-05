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
| Domain Name | `[YOUR-DOMAIN]` |
| IP Address | `[YOUR-IP]` |
| Subnet Mask | `[YOUR-SUBNET]` |
| Default Gateway | `[YOUR-GATEWAY]` |
| Preferred DNS | `[YOUR-DNS]` |

---

# 1. Configure Network Settings

Before installing Active Directory Domain Services, the Windows Server
was configured with a static IP address.

Active Directory and DNS require reliable network configuration so that
domain clients can consistently locate the Domain Controller.

### Network Configuration

The following network settings were configured:

- IP Address: `[YOUR-IP]`
- Subnet Mask: `[YOUR-SUBNET]`
- Default Gateway: `[YOUR-GATEWAY]`
- Preferred DNS Server: `[YOUR-DNS]`

### Configuration Steps

1. Opened **Settings → Network & Internet**.
2. Opened the network adapter properties.
3. Opened the IPv4 configuration.
4. Configured the server with a static IP address.
5. Configured the subnet mask.
6. Configured the default gateway.
7. Configured the appropriate DNS server.
8. Saved the network configuration.

### Verification

The network configuration was verified using:

```powershell
ipconfig /all
