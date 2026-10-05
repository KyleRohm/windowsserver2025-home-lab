[← Back to Main README](../README.md)

# Active Directory Domain Services Configuration

## Overview

This section documents the installation and configuration of Active Directory Domain Services (AD DS).

The Windows Server 2025 virtual machine created during the previous section of the home lab will be configured as a Domain Controller.

Active Directory will provide centralized authentication and management of users, computers, and security groups within the lab environment.

## Objectives

- Configure the Windows Server 2025 server for Active Directory
- Configure the network settings as part of the Active Directory deployment
- Install the Active Directory Domain Services role
- Promote the server to a Domain Controller
- Create a new Active Directory forest and domain
- Verify Active Directory functionality
- Create Organizational Units
- Create test user accounts
- Create security groups
- Configure group membership
- Verify domain authentication

---

## Prerequisites

- Administrative access to the Windows Server
- Server hostname configured
- Windows Server 2025 updated
- Network connectivity verified

---

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Operating System | Windows Server 2025 |
| Edition | Standard |
| Installation Type | Desktop Experience |
| Server Name | FileServer01 |

---

## Configure Network Settings

Before installing Active Directory Domain Services, the Windows Server was configured with a static IP address.

The following settings were configured:

- IP Address
- Subnet Mask
- Default Gateway
- Preferred DNS

### Configuration Steps

1. Opened **Windows PowerShell** → Typed `ipconfig` → **Copied the IPv4 Address** automatically assigned by DHCP (`192.168.88.129`).
2. Opened **Settings → Network & internet → Ethernet**.
3. Clicked `Edit` for IP assignnemt.
4. Set configuration to `Manual` and pasted the IP address.
5. Configured the **subnet mask** to `255.255.255.0`
6. Configured the **default gateway** to `192.168.88.2`
7. Configured the **DNS** to the loopback address.
8. Saved the network configuration.

![Static IP Configuration](screenshots/08-static-ip-config.png)

---
