[← Back to Main README](../README.md)

# Group Policy Objects (GPO) Configuration

## Overview

This section documents the creation and configuration of Group Policy Objects (GPOs) in the Windows Server 2025 home lab.

## Objectives

- Setup strong password requirements
- Map network drives for users upon login
- Set a default desktop wallpaper for all users
- Prevent users from using USB drives
- Strengthen Windows 11 workstation security
- Control GPO scope through Organizational Units and security groups
- Apply Group Policy updates
- Verify GPO application and troubleshoot policy issues

---

## Prerequisites

- Windows Server 2025 configured as a Domain Controller
- Active Directory Domain Services installed and operational
- Group Policy Management installed
- AD organizational structure created
- Domain-joined Windows 11 client`
- Active Directory user accounts and departmental security groups configured
- Departmental file shares configured and accessible on `FileServer01`
- Administrative access to the Domain Controller

---

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Operating System | Windows Server 2025 |
| Domain Controller | `FileServer01` |
| Active Directory Domain | `homelab.local` |
| Windows Client | Windows 11 |
| Client Computer Name | `Computer01` |

---

## Create a Domain Password and Account Lockout Policy

Created a GPO to establish password requirements and account lockout settings for domain user accounts.

> Note: Domain password and account lockout policies should be configured through a GPO linked at the domain level. Linking these account policies only to a departmental OU will not establish the domain-wide account policy.

### Create the GPO

1. Opened **Group Policy Management**.
2. Right-clicked `homelab.local` under **Domains**.
3. Selected **Create a GPO in this domain, and Link it here**.
4. Named the GPO `Domain Password and Account Lockout Policy`.
5. Select **OK**.
6. Right-clicked the new GPO.
7. Select **Edit**.

### Configure Password Policy

In the Group Policy Management Editor, navigated to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

Configured the following settings:

| Policy | Example Configuration |
|---|---|
| Minimum password length | 12 characters |
| Password must meet complexity requirements | Enabled |
| Maximum password age | 90 day |
| Enforce password history | 10 passwords remembered |
| Minimum password age | 1 day |

> Note: The remaining policies were left as default.
> 
> Enabled **User must change password at next logon** and disabled **Password never expires** for all users in Active Directory to test password requirements later.


### Configure Account Lockout Policy

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Configure the following settings:

| Policy | Example Configuration |
|---|---|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 0 minutes |
| Reset account lockout counter after | 30 minutes |

**Summary**:
These settings help reduce the risk of password guessing by locking accounts after five consecutive failed logon attempts. Locked accounts must be manually unlocked by an administrator, while the failed-attempt counter resets after 30 minutes.

### Save and Verify the GPO

1. Closed the Group Policy Management Editor.
2. Returned to Group Policy Management.
3. Confirmed that `Domain Password and Account Lockout Policy` is linked to `homelab.local`.
4. Confirmed that the GPO is enabled.

![Domain Password and Account Lockout Policy](screenshots/45-password-lockout-enabled.png)

![Domain Password and Account Lockout Policy 2](screenshots/46-password-lockout-enabled2.png)

---

## Create a Workstation Security Baseline GPO

Create a second GPO to configure security settings for domain-joined Windows workstations.

### Create the GPO

1. Opened **Group Policy Management**.
2. Expanded OU and right-clicked `TX`.
3. Selected **Create a GPO in this domain, and Link it here**.
4. Named the GPO `Workstation Security Baseline` → Selected **OK**.
5. Right-clicked the GPO and selected **Edit**.

### Configure Windows Defender Firewall

Navigated to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Windows Defender Firewall with Advanced Security`

1. Selected **Windows Defender Firewall with Advanced Security**.
2. Configured the firewall to remain enabled for the Domain, Private, and Public profiles.
3. Left the default inbound and outbound connections → Applied

> Keeping the firewall enabled helps protect the workstation from unsolicited inbound network connections.

![Firewall Settings](screenshots/47-firewall-settings.png)

### Configure Automatic Screen Lock

Navigate to:

`User Configuration → Policies → Administrative Templates → Control Panel → Personalization`

Configured the following settings:

| Policy | Configuration |
|---|---|
| Enable screen saver | Enabled |
| Password protect the screen saver | Enabled |
| Screen saver timeout | Enabled; 600 seconds |

> The screen saver timeout is set to 600 seconds (10 minutes). Password protection requires the user to authenticate again after the workstation locks.

### Configure an Interactive Logon Message

Navigated to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options`

Configured the following policies:

- **Interactive logon: Message title for users attempting to log on**
- **Interactive logon: Message text for users attempting to log on**

Configured the following message:

```text
Title: WARNING: Unauthorized Access

Message: This system is restricted to authorized users.
Individuals who attempt unauthorized access will be prosecuted.
If you are not an authorized user, disconnect now.
```

### Save and Verify the GPO

1. Closed the Group Policy Management Editor.
2. Returned to Group Policy Management.
3. Confirmed that `Workstation Security Baseline` is linked to the `TX` OU.
4. Confirmed that the GPO link is enabled.

![Security Baseline](screenshots/50-security-baseline-settings2.png)

![Security Basedline 2](screenshots/49-security-baseline-settings.png)

---

## Configure Departmental Network Drive Mappings

Created a third GPO to map departmental shared folders as network drives based on Active Directory security group membership.

This configuration integrates Group Policy with the file shares created in the previous section.

> Note: Before proceeding, the shared drives that were manually mapped in the last section were removed so that this GPO can be used to map them for all users automatically.

### Create the GPO

1. Opened **Group Policy Management**.
2. Right-clicked the `TX` OU.
3. Selected **Create a GPO in this domain, and Link it here**.
4. Named the GPO `Department Network Drive Mapping` and selected **OK**.
5. Right-clicked the GPO and select **Edit**.

### Configure the HR Network Drive

Navigated to:

`User Configuration → Preferences → Windows Settings → Drive Maps`

1. Right-clicked **Drive Maps**.
2. Selected **New → Mapped Drive**.
3. Left **Action** as `Update`.
4. Set **Location** to `\\FileServer01\Company Data\HR`.
5. Entered `Human Resources` as the label.
6. Chose `H:` as the shared drive letter.
7. Opened the **Common** tab.
8. Enabled **Item-level targeting** and selected **Targeting**.
9. Added a **Security Group** targeting item.
10. Specified the `HOMELAB\HR` security group and saved the configuration.

> Note: The drive mapping will be applied only when the logged-on user belongs to the HR security group.

### Configured the Sales Network Drive

Created another mapped drive item with the following settings:

| Setting | Configuration |
|---|---|
| Action | Update |
| Location | `\\FileServer01\Company Data\Sales` |
| Drive Letter | `S:` |
| Label | Sales |
| Item-level targeting | `HOMELAB\Sales` security group |

### Configured the IT Network Drive

Create another mapped drive item with the following settings:

| Setting | Configuration |
|---|---|
| Action | Update |
| Location | `\\FileServer01\Company Data\IT` |
| Drive Letter | `I:` |
| Label | IT |
| Item-level targeting | `HOMELAB\IT` security group |

![Department Network Drive Mapping](screenshots/54-shared-drive-mappings-new.png)

### Verify the Drive Mapping GPO

1. Closed the Group Policy Management Editor.
2. Confirmed that `Department Network Drive Mapping` is linked to the `TX` OU.
3. Confirmed that the GPO is enabled.
4. Verified that each mapped drive item has the correct UNC path and item-level targeting.

> **Note:** These drive mapping preferences do not grant file access by themselves. The corresponding share and NTFS permissions created in the last section is what actually allows the users to access the folders.

![Network Drive Mapping](screenshots/56-network-drive-gpo-linked.png)

![Network Drive Mapping 2](screenshots/55-network-drive-gpo-enabled.png)

---

## Verify Applied Group Policies

### Logon Message:

Powered on `Computer01` and was presented with the logon message.

![Logon Message](screenshots/52-unauthorized-logon-message.png)

### Account Lockout:

Entered 5 invalid password attempts as `mary.delgado`.

![Account Lockout](screenshots/57-5-invalid-logon-attempts.png)

### New Password Complexity Requirements:

Required to set a new password and and tested complexity requirements.

![Password Complexity](screenshots/59-complexity-requirements-new.png)

### Shared Drive Mapping:

Ran `gpupdate /force` and selected **This PC** in File Explorer.

![Drive Mapping](screenshots/53-automatic-mapping-new.png)

### Windows Defender Firewall:

Tested firewall profile settings.

![Windows Defender Firewall](screenshots/61-firewall-settings.png)

### Workstation Security Baseline:

Verified security GPO was applied.

![Workstation Security Baseline](screenshots/60-workstation-security-baseline-policy.png)

*Note: These verification steps were successfully repeated on the other two user accounts.*

---
