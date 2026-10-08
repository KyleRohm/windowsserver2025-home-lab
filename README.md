# Windows Server 2025 Home Lab

## Overview

This project documents the design, deployment, configuration, and administration of a Windows Server 2025 home lab running in VMware Workstation.

The goal of this lab is to develop hands-on experience with Windows Server administration, Active Directory, DNS, domain management, file-server configuration, permissions, and troubleshooting.

The lab is being built incrementally, with each phase documented along the way.

---

## Lab Objectives

- Deploy Windows Server 2025 as a virtual machine
- Configure and administer Windows Server
- Install and configure Active Directory Domain Services (AD DS)
- Configure DNS for the Active Directory environment
- Create and manage Organizational Units (OUs)
- Create and manage users and security groups
- Join Windows client computers to the domain
- Configure a Windows file server
- Configure shared folders
- Implement NTFS and share permissions
- Test access using different user accounts and security groups
- Troubleshoot common Windows networking, authentication, and permissions issues
- Document the configuration and troubleshooting process

---

## Lab Environment

| Component | Technology |
|---|---|
| Hypervisor | VMware Workstation |
| Server OS | Windows Server 2025 |
| Client OS | Windows 11 |
| Directory Services | Active Directory Domain Services |
| DNS | Windows Server DNS |
| File Services | Windows Server File Server |
| Virtual Networking | NAT |

### Virtual Machines

| Device Name | Operating System | Role | Status |
|---|---|---|---|
| FileServer01 | Windows Server 2025 | Active Directory| ✔️ Completed |
| " | " | Domain Server | ✔️ Completed |
| " | " | File Server | ⚪ Planned |
| Computer01 | Windows 11 Pro | Domain-Joined Client | ✔️ Completed |

> **Note:** The lab environment will be expanded as additional services and client systems are added.

---

## Network Architecture

The lab uses Network Address Translation (NAT) to allow the virtual machines to communicate with each other and access the network.

### Planned Architecture


                    Home Network
                         │
                    VMware Network
                         │
              ┌──────────┴─────────┐
              │                    │
        FileServer01            Client01
     Windows Server 2025       Windows 11
              │                    │
              │                    │
       Active Directory ◄──────────┘
              │
             DNS
              │
        File Services

## 01 Windows Server 2025 VM Deployment

This repository documents the setup and configuration of a Windows Server 2025 virtual machine in VMware Workstation.

[Read the full guide](01-server-setup/README.md).

## 02 Active Directory Domain Services

This repository documents the setup and configuration of Active Directory Domain Services within Windows Server 2025.

[Read the full guide](02-active-directory-domain-services/README.md).

## 03 Active Directory Users and Client

This repository documents the setup of an Organizational Unit structure within Active Directory and deployment of a Windows 11 client.

[Read the full guide](03-active-directory-users-and-client/README.md).
