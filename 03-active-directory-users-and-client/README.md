[← Back to Main README](../README.md)

# Active Directory Users and Windows 11 Client

## Overview

This section documents the creation of Organizational Units (OUs) and user accounts in the Active Directory environment.

A Windows 11 virtual machine was then deployed and configured as a domain-joined client.

## Objectives

- Create an Organizational Unit structure
- Add HR, IT, and Sales folders with user accounts
- Create and configure a Windows 11 client virtual machine
- Join the Windows 11 client to the Active Directory domain
- Verify network connectivity between the Windows 11 client and Domain Controller
- Follow best practices for domain-joined clients

---

## Prerequisites

- Windows Server 2025 configured as a Domain Controller
- Active Directory Domain Services installed
- IP and DNS settings configured
- DNS configured and operational

---

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Operating System | Windows Server 2025 |
| Domain Controller | FileServer01 |
| Active Directory Domain | homelab.local |
| DNS Server | 192.168.88.129 |

---

## Create Organizational Units

A corporate OU structure with departments was created to organize the users and computers within the lab environment.

The following structure was created:

```text
<homelab.local>
│
└── TX
    │
    ├── HR
    │
    ├── IT
    │
    └── Sales
