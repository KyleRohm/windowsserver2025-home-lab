[← Back to Main README](../README.md)

# Active Directory Users and Windows 11 Client

## Overview

After configuring the Windows Server 2025 Domain Controller, the Active Directory environment was populated with Organizational Units, user accounts, and security groups.

A Windows 11 virtual machine was then configured as a domain-joined client and added to the appropriate Organizational Unit.

## Objectives

- Create an Organizational Unit structure
- Create HR, IT, and Sales Organizational Units
- Create test user accounts
- Configure user accounts for their respective departments
- Create and configure a Windows 11 client virtual machine
- Install VMware Tools
- Install Windows updates
- Configure the Windows 11 client with the Domain Controller as its DNS server
- Verify network connectivity between the Windows 11 client and Domain Controller
- Join the Windows 11 client to the Active Directory domain
- Verify domain authentication
- Configure security group membership
- Move the Windows 11 computer account to the appropriate Organizational Unit
- Add a description to the computer account

---

## Prerequisites

- lipsum orem

---

## Lab Environment

## Create Organizational Units

A `TX` Organizational Unit was created to organize the users and computers within the lab environment.

The following Organizational Unit structure was created:

```text
<YOUR-DOMAIN>
│
└── TX
    │
    ├── HR
    │
    ├── IT
    │
    └── Sales
