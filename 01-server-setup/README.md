# Windows Server 2025 VM Deployment

## Overview

This project documents the installation and initial configuration of a
Windows Server 2025 virtual machine using VMware Workstation.

This server will serve as the foundation for the home lab and will
eventually be configured as a Domain Controller and File Server.

## Objectives

- Create a Windows Server 2025 virtual machine
- Configure the VM's hardware resources
- Install Windows Server 2025
- Configure the server hostname
- Configure network connectivity
- Apply Windows Updates
- Prepare the server for Active Directory Domain Services
- Document the installation and troubleshooting process

---

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Operating System | Windows Server 2025 |
| Edition | [Standard] |
| Installation Type | [Desktop Experience] |
| Number of processors | [2] |
| Number of cores per processor | [2] |
| Memory | [4 GB] |
| Storage | [60 GB] |
| Network Adapter | [NAT] |
| Server Name | [Windows Server 2025] |

---

## 1. Create the Virtual Machine

A new virtual machine was created in VMware Workstation.

### VM Configuration

- Virtual machine name: `Windows Server 2025`
- Guest operating system: Microsoft Windows
- Version: Windows Server 2025
- Number of processors: [2]
- Number of cores per processor: [2]
- Memory: [4] GB
- Storage: [60] GB
- Network: [NAT]

01-server-setup/01-windows-server-2025-vm-settings.png

---

## 2. Install Windows Server 2025

The Windows Server 2025 installation media was mounted to the
virtual machine and the operating system installation was completed.

### Installation Steps

1. Booted the VM from the Windows Server 2025 installation media.
2. Selected the appropriate Windows Server 2025 edition.
3. Selected the Desktop Experience installation option.
4. Accepted the Microsoft Software License Terms.
5. Selected the virtual disk as the installation destination.
6. Completed the Windows installation.
7. Created the initial local administrator account.
8. Logged into Windows Server for the first time.

### Screenshot

![Windows Server Installation](screenshots/03-windows-installation.png)

---

## 3. Initial Server Configuration

After installation, the server was configured with basic settings
required for the home lab.

### Configuration

- Server hostname configured
- Time zone verified
- Windows Updates installed
- Network connectivity tested
- Windows Firewall verified
- Server Manager reviewed

### Hostname

The server was renamed to:

`[SERVER-01]`

### Screenshot

![Server Manager](screenshots/04-server-manager.png)

---

## 4. Network Configuration

The server was configured with a static IP address to provide a
consistent network address for future Active Directory services.

### Network Configuration

```text
IP Address:      [192.168.x.x]
Subnet Mask:     [255.255.255.0]
Default Gateway: [192.168.x.x]
Preferred DNS:   [192.168.x.x]
