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
- Downloaded copy of Windows 11 Client installation media

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

### Configuration Steps

- Opened **Server Manager** → **Tools** → **Active Directory Users and Computers**.
- Right-clicked `homelab.local`.
- Selected **New** → **Organizational Unit**.
- Created Organizational Unit `TX`.
- Right-clicked the `TX` OU.
- Selected **New** → **Organizational Unit**.
- Created an OU named `HR`.
- Repeated the process to create `IT` and `Sales`.

![User Creation](screenshots/12-organizational-structure-creation.PNG)

---

## Create User Accounts

Created three user accounts and place each in the appropriate OU.

### Account Creation Steps

1. Opened **Server Manager**.
2. Selected **Tools → **Active Directory Users and Computers**.
3. Navigated to `TX → HR`.
4. Right-clicked `HR` and selected **New** → **User**.
5. Entered the user's information.
6. Configured a username and password.
7. Selected **Finish**.
8. Repeated the process to create user accounts in `IT` and `Sales`.

![User Accounts](screenshots/3accounts.png)

### Assign Domain Admin Group

Applied the **Domain Admins** security group to the IT user account.

1. Double-clicked `Kyle Rohm` and selected **New → User**.
2. Clicked **Member Of** → **Add**
3. Typed `Domain Admins` into the text field → **Check Names** → **OK**
4. Applied changes.

![IT Domain Administrator](screenshots/14-domain-admin-security-group.png)

> This security group will be used later to join the Windows 11 client to the domain.

---

## VM Configuration

Created a Windows 11 virtual machine in VMware.

1. **VMware** → **Create a New Virtual Machine**
2. Went through the VM Wizard and entered a **TPM** password.
3. Set number of processors to **2** and cores per processor to **1**.
4. Memory: **4 GB** → Network Type: **NAT** → I/O controller: **LSI Logic SAS**
5. Virtual disk: **NVMe** → Selected **Create a new virtual disk** → Disk size: **80 GB**
6. Clicked **Customize Hardware** → **New CD/DVD (SATA)** and selected the saved Win 11 .iso.

![Windows 11 Client](screenshots/15-windows-11-client-hardware.png)

---

## Install Windows 11

Created a Windows 11 virtual machine to serve as the client computer for the Active Directory domain.

### Installation Steps

1. Powered on the configured Windows 11 VM.
2. Selected **English** → **Install Windows 11**.
3. Selected the **I don't have a product key** option.
4. Selected the **Windows 11 Pro** image.
> Note: Only *Windows 11 Pro* and *Windows 11 Enterprise* versions can be joined to an Active Directory domain.
5. Named the device: `Computer01`
6. **Setup for work or school** → **Sign-in options** → **Domain join instead**
7. Named the profile: `Kyle Rohm` → Setup a password and security questions.
8. Completed final installation steps, booted into Windows 11 and installed VMware Tools.

![Windows 11 Desktop](screenshots/16-windows11-desktop.PNG)

> Verified client hostname:

![Windows 11 Hostname](screenshots/17-windows11-hostname.png)

---

## Configure Manual DNS Settings

Configured the Windows 11 client to use `FileServer01` as its DNS server.

1. Opened **Settings**.
2. Selected **Network & internet** → **Ethernet**
3. Selected **Edit** next to **DNS server assignment**.
4. Changed the setting to **Manual** and enabled **IPv4**.
5. Entered `192.168.88.129` as the Preferred DNS server IP address and saved the configuration.

![Windows 11 DNS Configuration](screenshots/18-windows11-DNS.png)

---

### Test Network Connectivity

Verified that the client can communicate with the Domain Controller:

```powershell
ping 192.168.88.129
```
![Ping FileServer01](screenshots/19-windows11-DNS-verification.png)

---

## Run Windows Updates

Brought the Windows 11 client up-to-date before joining it to the Active Directory domain.

1. **Settings** → **Windows Update**
2. Installed all available updates.
3. Restarted the computer when required.
4. Repeated the process until updates were completed.

![Windows Update](screenshots/20-win-11-updates.PNG)

---

## Join Windows 11 Client to Active Directory Domain

Joined `Computer01` to the Active Directory domain hosted by `FileServer01`.

1. Typed **About** in the taskbar search bar → **About your PC**
2. Clicked **Domain or workgroup** → Went to the **Computer Name** tab and selected **Change**.
3. Selected **Domain** and entered `homelab.local`.
4. Entered the IT user's domain admin credentials that were previously made and restarted.

Verified domain membership:

![Windows 11 Domain Join](screenshots/21-win-11-domain-join-verification.png)

---

## Move Computer01 to the Correct OU

Moved the Windows 11 client computer account into the appropriate OU.

1. Opened **Active Directory Users and Computers** on `FileServer01`.
2. Located the computer account for `Computer01`.
3. Right-clicked `Computer01` → **Move**
4. Moved device into the `TX` computer folder.
5. Added `Kyle Rohm` as the description to assign ownership.

![Computer01 Organizational Unit](screenshots/22-computer01.png)

![Computer01 Description](screenshots/23-computer01-ownership.png)

---
