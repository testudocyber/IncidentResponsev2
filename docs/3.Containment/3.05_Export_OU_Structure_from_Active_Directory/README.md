# Export OU Structure from Active Directory

## Task Export Organizational Unit (OU) structure from Active Directory

## Conditions

Given access to a Domain Controller (DC), a domain account with required permissions, and a workstation with Remote Server Administration Tools (RSAT) and PowerShell.

> **Operator Note:** Exporting OU structure helps map the organizational design of Active Directory, identify abnormal/malicious OUs, and verify proper object placement.

## Standards

* Team member obtains the correct name for the target domain.  
* Team member determines and uses the appropriate tool to connect to the domain controller and export the OU structure to a file.  
* Exported OU structure includes Distinguished Name, Name, and optionally object counts and permissions.

## End State

All Organizational Unit (OU) information has been exported to a local file and validated for incident review.

---

## Notes

Multiple tools can perform this task, including:

- PowerShell (`Get-ADOrganizationalUnit`)
- dsquery (command-line)
- LDIFDE and CSVDE (optional export formats)

---

## Manual Steps

### PowerShell Method (Preferred)

#### Get all OUs in the domain:

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object -ExpandProperty DistinguishedName
```

#### Get OU names and distinguished names in table format:

```powershell
Get-ADOrganizationalUnit -Filter 'Name -like "*"' | Format-Table Name, DistinguishedName -AutoSize
```

#### Export OU list to CSV for offline review:

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName | Export-Csv ".\OU_Structure.csv" -NoTypeInformation
```

---

### dsquery Method (Alternate - command line)

#### List all OUs in domain:

```cmd
dsquery ou DC=<DOMAIN>,DC=<DOMAIN EXTENSION>
```

---

### Netdom Method (Additional)

#### List domain workstations:

```cmd
netdom query WORKSTATION
```

#### List domain servers:

```cmd
netdom query SERVER
```

#### List domain controllers:

```cmd
netdom query DC
```

#### List Organizational Units (where user can create machine objects):

```cmd
netdom query OU
```

#### List Primary Domain Controller:

```cmd
netdom query PDC
```

> **Operator Note:** Netdom output can help correlate OU locations and understand domain structure relationships quickly.

---

### Optional: LDIFDE and CSVDE

```cmd
ldifde -f ou_export.ldf -d "DC=contoso,DC=com" -p subtree -r "(objectClass=organizationalUnit)"
csvde -f ou_export.csv -r "(objectClass=organizationalUnit)"
```

---

## Running Script

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName | Export-Csv ".\OU_Structure.csv" -NoTypeInformation
```

---

## Dependencies

* RSAT with Active Directory Module (PowerShell)
* Domain credentials with read access
* Optional: dsquery, LDIFDE, CSVDE

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + AD Module | Windows | Primary export method |
| dsquery | Windows | Simple OU export |
| LDIFDE / CSVDE | Windows | Export to simple text formats |
| Netdom | Windows | Object discovery (workstations, servers, PDC, OUs) |
| ADExplorer (Sysinternals) | Windows | Visual OU mapping |
| BloodHound (optional, advanced use) | Windows/Linux | Graph-based AD analysis |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate PowerShell and RSAT availability.
- [ ] Export OU structure using PowerShell or dsquery.
- [ ] Use Netdom queries to gather context of domain topology.
- [ ] Save export files and validate OU accuracy.
- [ ] Investigate any suspicious or unexpected OUs.
- [ ] Secure export for review and incident reporting.

### Best Practices

- Use PowerShell for repeatable exports and CSV format.
- Review OU names and hierarchy for anomalies.
- Validate permissions if delegation or abuse is suspected.
- Document and retain OU exports for audit and recovery purposes.

---

## References

[Get-ADOrganizationalUnit PowerShell Cmdlet](https://technet.microsoft.com/en-us/library/ee617236.aspx)  
[Get-OU_Permissions](https://github.com/WiredPulse/PowerShell/blob/master/Active_Directory/Get-OU_Permissions.ps1)  
[Dsquery OU command line reference](https://technet.microsoft.com/en-us/library/cc770509(v=ws.11).aspx)  
[LDIFDE.EXE command line tool](https://technet.microsoft.com/en-us/library/cc731033(v=ws.11).aspx)  
[CSVDE.EXE command line tool](https://technet.microsoft.com/en-us/library/cc732101(v=ws.11).aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 2.0 | Final version with restored manual steps, full tooling, operator checklist, and best practices | Leo |
