[← Back to Main README](../README.md)

# Group Policy Objects (GPO) Configuration

## Overview

This section documents the creation and configuration of Group Policy Objects (GPOs) in the Windows Server 2025 home lab.

Group Policy allows administrators to centrally manage user and computer settings across an Active Directory domain. GPOs can be linked to domains, sites, and Organizational Units (OUs) to enforce security policies and standardize workstation configurations.

In this lab, Group Policy will be used to configure the following:
* Setup strong password requirements
* Map network drives for users upon login
* Set a default desktop wallpaper for all users
* Prevent users from using USB drives
* Strengthen Windows 11 workstation security

## Objectives

- Open and manage Group Policy Management
- Create and link Group Policy Objects
- Configure domain password and account lockout policies
- Configure Windows 11 workstation security settings
- Configure departmental network drive mappings
- Control GPO scope through Organizational Units and security groups
- Apply Group Policy updates
- Verify GPO application and troubleshoot policy issues

---

## Prerequisites

- Windows Server 2025 configured as a Domain Controller
- Active Directory Domain Services installed and operational
- Group Policy Management installed
- Active Directory domain configured as `homelab.local`
- Organizational Units configured for `TX`, `HR`, `IT`, and `Sales`
- Domain-joined Windows 11 client named `Computer01`
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
| NetBIOS Domain | `HOMELAB` |
| Windows Client | Windows 11 |
| Client Computer Name | `Computer01` |
| Organizational Unit | `TX` |
| Departmental OUs | `HR`, `IT`, `Sales` |
| File Server | `FileServer01` |
| Group Policy Management | Group Policy Management Console (GPMC) |

---

## Open Group Policy Management

Group Policy Management is used to create, configure, link, and troubleshoot Group Policy Objects.

### Configuration Steps

1. Sign in to `FileServer01` using an account with administrative privileges.
2. Open **Server Manager**.
3. Select **Tools → Group Policy Management**.
4. Expand **Forest: homelab.local**.
5. Expand **Domains**.
6. Expand `homelab.local`.
7. Review the existing domain and Organizational Unit structure.

The Group Policy Management Console should display the domain and its available Group Policy Objects.

![Group Policy Management Console](screenshots/30-group-policy-management.png)

---

## Create a Domain Password and Account Lockout Policy

Create a GPO to establish password requirements and account lockout settings for domain user accounts.

Domain password and account lockout policies should be configured through a GPO linked at the domain level. Linking these account policies only to a departmental OU will not establish the domain-wide account policy.

### Create the GPO

1. Open **Group Policy Management**.
2. Right-click `homelab.local` under **Domains**.
3. Select **Create a GPO in this domain, and Link it here**.
4. Name the GPO `Domain Password and Account Lockout Policy`.
5. Select **OK**.
6. Right-click the new GPO.
7. Select **Edit**.

### Configure Password Policy

In the Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

Configure the following settings:

| Policy | Example Configuration |
|---|---|
| Enforce password history | 10 passwords remembered |
| Minimum password length | 12 characters |
| Password must meet complexity requirements | Enabled |
| Minimum password age | 1 day |

Leave the maximum password age unconfigured unless a defined requirement calls for periodic password changes. Password changes should also be required when a password is compromised or otherwise needs to be replaced.

### Configure Account Lockout Policy

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Configure the following settings:

| Policy | Example Configuration |
|---|---|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

Windows may prompt you to confirm related account lockout settings when configuring the threshold. Review the resulting values and confirm that they match the intended configuration.

These settings help reduce the risk of password guessing while allowing accounts to unlock automatically after the configured duration.

### Save and Verify the GPO

1. Close the Group Policy Management Editor.
2. Return to Group Policy Management.
3. Confirm that `Domain Password and Account Lockout Policy` is linked to `homelab.local`.
4. Confirm that the GPO is enabled.

> **Security Note:** These are example lab settings, not a complete production security baseline. Account lockout thresholds should be evaluated alongside the risk of denial-of-service attacks against user accounts.

![Domain Password and Account Lockout Policy](screenshots/31-domain-password-policy.png)

---

## Create a Workstation Security Baseline GPO

Create a second GPO to configure security settings for domain-joined Windows workstations.

For this lab, the GPO will be linked to the `TX` OU. Since `Computer01` is located in `TX → IT`, its computer settings can receive the policy through the OU hierarchy.

### Create the GPO

1. Open **Group Policy Management**.
2. Expand `homelab.local`.
3. Expand the `TX` OU.
4. Right-click `TX`.
5. Select **Create a GPO in this domain, and Link it here**.
6. Name the GPO `Workstation Security Baseline`.
7. Select **OK**.
8. Right-click the GPO and select **Edit**.

### Configure Windows Defender Firewall

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Windows Defender Firewall with Advanced Security`

1. Select **Windows Defender Firewall with Advanced Security**.
2. Open its properties.
3. Configure the firewall to remain enabled for the Domain, Private, and Public profiles.
4. Retain the default inbound-blocking behavior unless a specific application or lab requirement requires an exception.
5. Review the outbound rules and leave the default outbound behavior unchanged unless a specific requirement calls for a change.
6. Apply the settings.

Keeping the firewall enabled helps protect the workstation from unsolicited inbound network connections.

### Configure Automatic Screen Lock

Navigate to:

`User Configuration → Policies → Administrative Templates → Control Panel → Personalization`

Configure the following settings, where available in the installed Windows administrative templates:

| Policy | Configuration |
|---|---|
| Password protect the screen saver | Enabled |
| Screen saver timeout | Enabled; 600 seconds |
| Force specific screen saver | Configure only if required for the lab |

The screen saver timeout is set to 600 seconds (10 minutes). Password protection requires the user to authenticate again after the workstation locks.

> **Note:** User Configuration settings apply according to the user's Group Policy scope, not simply because the computer is located in the `TX` OU. The workstation firewall settings are computer policies and follow the computer account's OU.

### Configure an Interactive Logon Message

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options`

Configure the following policies:

- **Interactive logon: Message title for users attempting to log on**
- **Interactive logon: Message text for users attempting to log on**

Example configuration:

```text
Title: Authorized Users Only

Message: This system is for authorized users only.
Unauthorized access is prohibited.
```

This setting displays a notice before users sign in. A logon notice is not a substitute for access controls or other security measures.

### Save and Verify the GPO

1. Close the Group Policy Management Editor.
2. Return to Group Policy Management.
3. Confirm that `Workstation Security Baseline` is linked to the `TX` OU.
4. Confirm that the GPO link is enabled.

![Workstation Security Baseline](screenshots/32-workstation-security-baseline.png)

---

## Configure Departmental Network Drive Mappings

Create a third GPO to map departmental shared folders as network drives based on Active Directory security group membership.

This configuration integrates Group Policy with the file shares created in the previous section.

### Create the GPO

1. Open **Group Policy Management**.
2. Right-click the `TX` OU.
3. Select **Create a GPO in this domain, and Link it here**.
4. Name the GPO `Department Network Drive Mapping`.
5. Select **OK**.
6. Right-click the GPO and select **Edit**.

### Configure the HR Network Drive

Navigate to:

`User Configuration → Preferences → Windows Settings → Drive Maps`

1. Right-click **Drive Maps**.
2. Select **New → Mapped Drive**.
3. Set **Action** to `Update`.
4. Set **Location** to `\\FileServer01\HR`.
5. Choose an available drive letter, such as `H:`.
6. Enter a label such as `HR Shared Folder`.
7. Open the **Common** tab.
8. Enable **Item-level targeting**.
9. Select **Targeting**.
10. Add a **Security Group** targeting item.
11. Specify the `HOMELAB\HR` security group.
12. Save the configuration.

The drive mapping should be applied only when the logged-on user belongs to the HR security group.

### Configure the Sales Network Drive

Create another mapped drive item with the following settings:

| Setting | Configuration |
|---|---|
| Action | Update |
| Location | `\\FileServer01\Sales` |
| Drive Letter | `S:` |
| Label | Sales Shared Folder |
| Item-level targeting | `HOMELAB\Sales` security group |

### Configure the IT Network Drive

Create another mapped drive item with the following settings:

| Setting | Configuration |
|---|---|
| Action | Update |
| Location | `\\FileServer01\IT` |
| Drive Letter | `I:` |
| Label | IT Shared Folder |
| Item-level targeting | `HOMELAB\IT` security group |

Use the actual domain and security group names configured in Active Directory.

### Verify the Drive Mapping GPO

1. Close the Group Policy Management Editor.
2. Confirm that `Department Network Drive Mapping` is linked to the `TX` OU.
3. Confirm that the GPO is enabled.
4. Verify that each mapped drive item has the correct UNC path and item-level targeting.

> **Important:** Drive mapping preferences do not grant file access by themselves. The corresponding share and NTFS permissions must still allow the user to access the folder. Also, user settings apply only to users within the GPO's effective scope. If a test user is outside the `TX` OU, the mapping policy may need to be linked or scoped differently.

![Department Network Drive Mapping](screenshots/33-department-drive-mapping.png)

---

## Apply Group Policy Updates

After configuring the GPOs, refresh Group Policy on `Computer01` to retrieve the latest settings.

### Force a Group Policy Update

1. Sign in to `Computer01` using a domain account.
2. Open **Command Prompt** or **PowerShell**.
3. Run the following command:

```powershell
gpupdate /force
```

4. Wait for the computer and user policy updates to finish.
5. Restart the computer or sign out and back in if prompted or required for the settings being tested.

Computer policies and user policies may refresh at different times. A forced refresh does not eliminate the need to restart or sign in again for settings that require it.

![Group Policy Update](screenshots/34-group-policy-update.png)

---

## Verify Applied Group Policies

Use the Resultant Set of Policy tools to confirm which policies are applied to `Computer01`.

### Verify with GPResult

Open Command Prompt on `Computer01` and run:

```powershell
gpresult /r
```

Review the output for:

- The computer's domain membership
- The applied computer Group Policy Objects
- The applied user Group Policy Objects
- Any GPOs that were denied or not applied

To generate a more detailed HTML report, run:

```powershell
gpresult /h "%USERPROFILE%\Desktop\gpresult.html"
```

Open the generated report on the desktop and review the applied policies.

### Verify with Group Policy Results

1. Open **Group Policy Management** on `FileServer01`.
2. Right-click **Group Policy Results**.
3. Select **Group Policy Results Wizard**.
4. Select `Computer01` as the target computer.
5. Select the appropriate logged-on user if prompted.
6. Complete the wizard.
7. Review the resulting report for applied and denied GPOs.

![Group Policy Results](screenshots/35-group-policy-results.png)

---

## Test Group Policy Configuration

Verify that each GPO behaves as intended.

### Test Domain Password Policy

1. Confirm that the password policy GPO is linked at the domain level.
2. Review the configured password requirements in Group Policy Management.
3. Use a dedicated test account to validate password requirements where appropriate.
4. Avoid repeated failed attempts against important accounts while testing account lockout behavior.

Password policy changes do not necessarily force existing passwords to change immediately. Test the configured requirements with a suitable test account and document the observed result.

### Test Workstation Security Settings

On `Computer01`:

1. Run `gpupdate /force`.
2. Run `gpresult /r`.
3. Confirm that `Workstation Security Baseline` appears in the applied computer policies.
4. Verify that Windows Defender Firewall remains enabled.
5. Verify that the configured logon notice appears.
6. Verify the screen lock behavior after the configured timeout, if the user policy is in scope.

### Test Departmental Drive Mappings

1. Sign in to `Computer01` as the HR test user.
2. Confirm that the `H:` drive appears and opens `\\FileServer01\HR`.
3. Sign out and sign in as the Sales test user.
4. Confirm that the `S:` drive appears and opens `\\FileServer01\Sales`.
5. Sign in as the IT test user.
6. Confirm that the `I:` drive appears and opens `\\FileServer01\IT`.
7. Verify that users cannot access departmental shares for which they lack permission.

The drive mappings depend on user policy scope, group membership, DNS resolution, SMB connectivity, and the existing file share permissions.

### Test Results

| Test | Expected Result | Status |
|---|---|---|
| Domain password policy configured | Password requirements are configured at the domain level | Pending |
| Account lockout policy configured | Lockout threshold and duration are configured | Pending |
| Workstation Security Baseline applied | GPO appears in applied computer policies | Pending |
| Windows Defender Firewall | Firewall remains enabled | Pending |
| Interactive logon notice | Configured notice appears before sign-in | Pending |
| HR drive mapping | HR user receives the HR drive | Pending |
| Sales drive mapping | Sales user receives the Sales drive | Pending |
| IT drive mapping | IT user receives the IT drive | Pending |
| Unauthorized file access | Access is denied where no applicable permission exists | Pending |

Update the status column after performing each test.

![Group Policy Testing](screenshots/36-group-policy-testing.png)

---

## Troubleshoot Group Policy

If a policy does not apply as expected, verify the following.

### Verify GPO Links

1. Open Group Policy Management.
2. Confirm that the GPO is linked to the correct domain or OU.
3. Confirm that the link is enabled.
4. Check whether inheritance is blocked or enforced links affect the expected result.

### Verify the Computer and User OU Locations

- Computer policies are processed according to the computer account's effective Group Policy scope.
- User policies are processed according to the user account's effective Group Policy scope, subject to any configured loopback processing.
- A computer located in `TX → IT` does not automatically cause HR or Sales user policies to apply to users in other OUs.

### Verify Security Filtering

1. Select the GPO in Group Policy Management.
2. Review **Scope → Security Filtering**.
3. Confirm that the intended computer or user has the required permissions to read and apply the GPO.
4. Check the **Delegation** tab if permissions appear incorrect.

Avoid removing `Authenticated Users` without first configuring appropriate read and apply permissions for the intended target accounts.

### Review Group Policy Results

Run:

```powershell
gpresult /r
```

For additional detail:

```powershell
gpresult /h "%USERPROFILE%\Desktop\gpresult.html"
```

Review the report for denied policies, filtering issues, and unexpected scope.

### Verify DNS and Domain Connectivity

From `Computer01`, run:

```powershell
nslookup homelab.local
nslookup FileServer01
```

Confirm that DNS resolves through the Active Directory DNS server and that the client can communicate with the Domain Controller.

---

## Final Group Policy Configuration

After completing this section, the lab should include:

- A domain-level password and account lockout policy
- A workstation security baseline linked to the appropriate OU
- A departmental network drive mapping GPO
- Group Policy scope configured for the intended users and computers
- Group Policy updates applied to the Windows 11 client
- Verification of applied policies using `gpresult` and Group Policy Results
- Testing of departmental drive mappings and file share access
- Troubleshooting of policy application and scope

## Skills Demonstrated

- Group Policy Management Console administration
- GPO creation, configuration, and linking
- Domain password and account lockout policy configuration
- Windows workstation security configuration
- Active Directory OU and GPO scope management
- Group Policy Preferences
- Security group-based drive mapping
- NTFS and SMB permission integration
- `gpupdate` and `gpresult` troubleshooting
- Group Policy Results analysis
```
