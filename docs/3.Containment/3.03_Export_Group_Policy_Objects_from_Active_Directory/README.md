# Export Group Policy Objects (GPOs) from Active Directory

## Task Export Group Policy Objects (GPOs) from Active Directory

## Conditions

Given access to a Domain Controller (DC), domain account with the required permissions, and a workstation with Remote Server Administration Tools (RSAT).

> **Operator Note:** Exporting Group Policy Objects provides visibility into enforced security settings, startup scripts, and other critical system configuration controls.

## Standards

* Team member runs the Group Policy Management Console or PowerShell on the workstation and locates all relevant GPOs for export.  
* Team member saves the GPO data to a local workstation for review.  
* Team member ensures that the export includes both settings and metadata (creation, modification dates, linked OUs).

> **Operator Note:** Consider both human-readable (HTML/Report) and machine-parseable (XML or Backup) formats.

## End State

All Group Policy Objects have been exported to local files, organized for offline review and analysis.

---

## Notes

* PowerShell Group Policy Cmdlets are only available for:
  - Windows Server 2008 R2 and higher
  - Windows 7 and higher (with RSAT installed)

* Exported GPOs should include metadata:
  - DisplayName
  - GPOStatus
  - WMIFilter
  - Creation and Modification Time
  - User and Computer settings

---

## Manual Steps

### PowerShell Method (Preferred)

#### Import GroupPolicy module:

```powershell
Import-Module GroupPolicy
```

#### Export GPO metadata and display properties:

```powershell
$GPOs = Get-GPO -All -Server <DomainController> | Select-Object ID, Path, DisplayName, GPOStatus, WMIFilter, CreationTime, ModificationTime, User, Computer
$GPOs | Export-Csv ".\GPO_Metadata.csv" -NoTypeInformation
```

#### Export full GPO Backup (Best method for forensic review):

```powershell
Backup-GPO -All -Path "C:\GPO_Backups"
```

> **Operator Note:** Backup-GPO includes all settings, links, and can be restored or reviewed later using `Get-GPOReport`.

#### Export GPO into human-readable format (HTML Report):

```powershell
Get-GPO -All | ForEach-Object {
    Get-GPOReport -Name $_.DisplayName -ReportType Html -Path ".\Reports\$($_.DisplayName).html"
}
```

> **Operator Note:** HTML reports are useful for offline review and presentations.

---

### Group Policy Management Console (GPMC) GUI (Alternative)

* Open GPMC (`gpmc.msc`).
* Expand Forest → Domains → <Your Domain> → Group Policy Objects.
* Right-click a GPO → Save Report → Choose HTML or XML.

> **Operator Note:** This is suitable for single or selective GPO exports.

---

## Running Script

```powershell
Import-Module GroupPolicy
Backup-GPO -All -Path "C:\GPO_Backups"
```

* Store exported files under incident response documentation repository.

---

## Dependencies

* RSAT with Group Policy Management Console and GroupPolicy PowerShell module.
* Domain admin or delegated GPO read permissions.
* Sufficient storage space for full backup of GPOs.

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + GroupPolicy module | Windows | Full export + reports + backup |
| GPMC GUI | Windows | Manual single GPO export |
| AGPM (Advanced Group Policy Management) | Windows | (Optional) Centralized change auditing and rollback |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Verify RSAT tools and GroupPolicy module are installed.
- [ ] Use PowerShell Backup-GPO to export all GPOs.
- [ ] Generate HTML reports for readability.
- [ ] Review exported data for:
  - Recently modified or created GPOs.
  - GPOs linked to unexpected OUs.
  - Scripts, WMI filters, and restricted groups settings.
- [ ] Securely store exported data.

### Best Practices

- Use Backup-GPO for comprehensive export (preferred).
- Use HTML report generation for easy offline review.
- Validate permissions before performing bulk export.
- Use AGPM if available for audit trail and change control history.

---

## References

[Group Policy Cmdlets](https://technet.microsoft.com/en-us/library/ee461027.aspx)  
[Backup-GPO Documentation](https://docs.microsoft.com/en-us/powershell/module/grouppolicy/backup-gpo)  
[PowerShell GPO Examples](https://github.com/WiredPulse/PowerShell)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Full original + enriched operator procedures, GUI + PowerShell, operator recommendations and alternate tooling | Leo |
