# 4.57 Remove Unauthorized Devices (Local, AD, Cloud Registered)

## Task

Identify and remove unauthorized, stale, or suspicious devices across local systems, Active Directory (AD), and Cloud Registered environments (Azure AD, Intune, AWS, Google Workspace).

---

## Conditions

Given access to local system inventories, Active Directory, and cloud platform management consoles.

---

## Standards

* Discover all registered and connected devices across environments.
* Validate devices against authorized inventory.
* Detect unauthorized, stale, or suspicious devices.
* Remove or disable unauthorized devices following policy and with owner coordination.

---

## End State

All unauthorized devices are identified, removed, and documented across local, enterprise, and cloud environments.

---

## Notes

- Unauthorized or unmanaged devices are high risk for compromise and lateral movement.
- Removal or disablement should follow local policy and include validation to avoid impact.
- Cloud-registered devices may remain persistent even after network removal → must be explicitly removed.

---

## Manual Steps

### Step 1: Identify All Active and Registered Devices

#### Local Network Devices (Windows)

```powershell
Get-ADComputer -Filter * -Properties LastLogonDate | Select-Object Name, LastLogonDate
```

Look for:

- LastLogonDate = stale (>90 days)
- Computers not listed in asset inventory
- Unknown naming conventions (ex: random strings)

#### Linux/MacOS Devices (via DHCP or Network Scans)

```bash
sudo nmap -sn 192.168.1.0/24
```

Look for:

- Devices with hostnames not matching standard naming conventions
- IP addresses not documented in DHCP reservations

> **Operator Note:** Flag any unknown or unmanaged IP/MAC addresses.

---

#### Active Directory

```powershell
Get-ADComputer -Filter * -Properties Enabled, LastLogonDate | Where-Object {$_.Enabled -eq $true} | Select-Object Name, LastLogonDate
```

- Look for enabled computers with no login activity (stale).
- Validate against organizational inventory.

---

#### Azure AD and Intune

```powershell
Get-AzureADDevice -All $true | Select-Object DisplayName, DeviceId, AccountEnabled
```

Or via Azure Portal:

Azure Portal → Azure AD → Devices → All Devices

- Look for:
  - Unmanaged or personal devices
  - Stale last check-in dates
  - Unauthorized platform types

---

#### AWS Systems Manager (SSM) + EC2 Instances

```bash
aws ssm describe-instance-information
aws ec2 describe-instances
```

Look for:

- Instances or devices not matching approved device tags/naming
- Instances running outside of standard VPCs/subnets

---

#### Google Workspace (Endpoint Management)

Admin Console → Devices → Overview → Endpoints

Look for:

- Devices not linked to managed users
- Devices marked as personal or unmanaged
- Devices not checked-in recently

> **Operator Note:** Stale or unknown registered devices are high risk.

---

### Step 2: Validate Device Authorization

- Compare findings to asset inventories
- Confirm owners or users of each device
- Validate with business owners or IT asset managers

> **Operator Note:** Temporary, BYOD, or contractor devices should be explicitly approved.

---

### Step 3: Disable or Remove Unauthorized Devices

#### Local (Windows AD)

```powershell
Disable-ADAccount -Identity "ComputerName"
Remove-ADComputer -Identity "ComputerName"
```

#### Azure AD

```powershell
Remove-AzureADDevice -ObjectId <DeviceID>
```

Or via Portal → Azure AD → Devices → Delete device.

#### Intune (Microsoft Endpoint Manager)

- Locate device
- Choose "Retire" or "Delete"

#### AWS

```bash
aws ec2 terminate-instances --instance-ids <instance-id>
aws ssm deregister-managed-instance --instance-id <instance-id>
```

#### Google Workspace

Admin Console → Devices → Select Device → Remove Device

> **Operator Note:** Where possible, disable or quarantine before deletion to prevent disruption.

---

### Step 4: Validate Removal and Audit

- Re-run inventory queries and confirm devices are removed.
- Review event/audit logs to confirm removal actions.
- Document removal reason, method, and approver.

---

## Dependencies

* Access to AD, Azure AD, Intune, AWS, Google Admin Console
* Approved asset and device inventory list
* Coordination with system and business owners

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell AD Modules | Windows | Built-in/RSAT | AD device management |
| AzureAD / MS Graph Module | PowerShell | Install-Module | Azure AD device management |
| AWS CLI | Cross-platform | Package manager | EC2 / SSM device management |
| Google Admin Console | Web | Native | Google Workspace device management |
| Nmap | Cross-platform | Package manager | Network scanning |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain authorized device inventory (local, AD, cloud).
- [ ] Collect all active and registered devices.
- [ ] Compare detected devices against authorized inventory.
- [ ] Validate ownership and status of suspicious devices.
- [ ] Disable or remove unauthorized devices.
- [ ] Validate removal and update inventory documentation.

### Best Practices

- Disable before deleting where possible → prevents accidental loss of forensic data.
- Stale devices should be reviewed regularly and removed if no longer needed.
- Enforce enrollment and endpoint management (Intune, SSM, Google MDM) for cloud registered devices.
- Implement alerts for new device registrations.

---

## References

- [Azure AD Device Management](https://learn.microsoft.com/en-us/azure/active-directory/devices/manage-devices)
- [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)
- [Google Endpoint Management](https://support.google.com/a/answer/173390)
- [Active Directory Computer Management](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/computer-management)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused guidance and multi-cloud/device examples | Leo |
