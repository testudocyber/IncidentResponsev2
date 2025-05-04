# Export Groups from Active Directory

## Task Export List of Groups from Active Directory

## Conditions

Given domain credentials with the appropriate permissions, and an incident response workstation with necessary tools and access to the Primary Domain Controller (DC) for the target domain.

> **Operator Note:** Identifying AD groups and their attributes allows responders to detect rogue groups, privilege escalation attempts, and unauthorized changes.

## Standards

* Team selects the best method to connect to the Domain Controller and collect required group information.  
* Team member obtains Domain group information including:

| Canonical Name | Display Name | Distinguished Name |
|----------------|--------------|--------------------|
| Group Category | Group Scope | Object GUID |
| sAMAccountName | Security Identifier (SID) | Creation Date/Time |
| Modified Date/Time | | |

* Group data is exported to a CSV file and stored according to incident response SOP.

> **Operator Note:** Attributes such as creation and modification dates help identify suspicious group creation or changes during an incident.

## End State

All Domain Groups have been exported to a CSV file with sufficient information to allow for detection of malicious activity.

---

## Manual Steps

### PowerShell Method (Preferred)

#### Load Active Directory module:

```powershell
Import-Module ActiveDirectory
```

#### Export group information:

```powershell
Get-ADGroup -Filter * -Properties * |
Select-Object Name, DisplayName, DistinguishedName, GroupCategory, GroupScope, ObjectGUID, sAMAccountName, SID, whenCreated, whenChanged |
Export-Csv ADGroupsExport.csv -NoTypeInformation
```

> **Operator Note:** Adding `whenCreated` and `whenChanged` provides vital timeline data for analysis.

#### Optional: Export group membership for each group (advanced analysis):

```powershell
Get-ADGroup -Filter * | ForEach-Object {
    Get-ADGroupMember $_ | Select-Object Name, SamAccountName, objectClass | Export-Csv "$($_.Name)_Members.csv" -NoTypeInformation
}
```

> **Operator Note:** This optional step helps in identifying suspicious group members.

---

### RSAT / ADUC Method (Alternative)

#### Open ADUC:

```plaintext
dsa.msc
```

#### Use "Find" or search filters to view and export groups:

- View → Add/Remove Columns to select useful attributes.
- Export manually to CSV or Excel.

> **Operator Note:** Use only if PowerShell is unavailable. This method provides limited attributes.

---

## Running Script

* Use the PowerShell method above and store outputs in incident shared folder for later review.

---

## Dependencies

* Windows system with RSAT + Active Directory Module installed:

```powershell
Import-Module ActiveDirectory
```

* Domain credentials with read access.

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + AD Module | Windows | Primary export method for comprehensive group attributes |
| ADUC (RSAT - dsa.msc) | Windows | Manual export |
| ADExplorer (Sysinternals) | Windows | AD object viewer, supports export |
| LDAP Search Tools | Cross-platform | Lightweight alternate method |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate PowerShell and AD module installation.
- [ ] Verify domain credentials and connectivity.
- [ ] Use PowerShell to export group objects and key attributes.
- [ ] Optionally export group membership details for deeper analysis.
- [ ] Review exported CSV file for anomalies and secure per SOP.

### Best Practices

- Always export creation and modified date for timeline analysis.
- Look for group names outside normal naming conventions.
- Identify newly created groups during the incident timeframe.
- Document and preserve exported files for forensics.

---

## References

[Get-ADGroup PowerShell syntax](https://technet.microsoft.com/en-us/library/ee617196.aspx)  
[Get-ADGroupMember syntax](https://technet.microsoft.com/en-us/library/ee617193.aspx)  
[Compare-Object syntax](https://technet.microsoft.com/en-us/library/ee156812.aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Full original + enriched PowerShell export, alternate tooling, operator checklist and recommendations | Leo |
