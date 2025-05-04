# Export Users from Active Directory

## Task Export List of Users from Active Directory

## Conditions

Given domain credentials with the appropriate permissions and an incident response workstation with necessary tools.

> **Operator Note:** Exporting user accounts from Active Directory is critical for identifying unauthorized accounts, password policy violations, and lateral movement indicators.

## Standards

* Team member checks the schema of the Active Directory (AD) structure and determines best grouping for exporting users (i.e., all to one file; by OU; other breakdown).  
* Team member obtains Domain user information that may include the following:

| Expiration date | badPwdCount | Distinguished name |
| --- | --- | --- |
| Enabled | Given Name | LastBadPasswordAttempt |
| Last Logon Date/Time | LockedOut | Group membership |
| Name | GUID | PasswordExpired |
| PasswordLastSet | PasswordNeverExpires | PasswordNotRequired |
| sAMAccountName | SID | Surname |
| User Principal Name | When Created | |

* User data is exported to a CSV file and stored according to team SOP.

> **Operator Note:** Exported user account data helps responders identify suspicious accounts, accounts without expiration dates, accounts never logged in, etc.

## End State

All Domain User accounts have been exported to a CSV file with the necessary information to allow for detection of malicious activity.

---

## Notes

The choice of tool affects export depth and format.

- PowerShell with Active Directory module is preferred.
- Multi-valued attributes (like MemberOf) require flattening into a string (joined by semicolon) for export.
- Avoid simple wildcard exports that lose nested attributes or cause export issues.

Example of **Not recommended** command:

```powershell
Get-ADUser -filter * -Properties * | Export-Csv UnSat.csv
```

---

## Manual Steps

### PowerShell Method (Preferred)

#### Open PowerShell ISE or PowerShell:

```powershell
PowerShell_ISE.exe
```

#### Validate PowerShell environment:

```powershell
echo $PSVersionTable
[Environment]::Is64BitProcess
Get-ExecutionPolicy
```

Ensure version >= 2.0 and `ByPass` or `Unrestricted` execution policy.

#### Import AD Module:

```powershell
Import-Module ActiveDirectory
```

#### Export users by OU (Scripted Option):

```powershell
$output = Read-Host "'Y' for output to file or any key for output in GUI table view"
$fqdn = Read-Host "Enter FQDN domain"
$cred = Get-Credential

Write-Host "Contacting $fqdn domain..." -ForegroundColor Yellow
$domain = (Get-ADDomain $fqdn -Credential $cred)

$OUlist = @(Get-ADOrganizationalUnit -Filter * -Credential $cred -SearchBase $domain.DistinguishedName)

$newlist = @{}
foreach ($_objectitem in $OUlist) {
    $getUser = Get-ADUser -Filter * -Credential $cred -SearchBase $_objectItem.DistinguishedName
    $newlist.add($_objectItem.DistinguishedName, $getUser.Count)
}

if ($output -eq "Y") {
    $newlist | ft -AutoSize | Out-File .\UsersByOU.txt
} else {
    $newlist | Out-GridView
}
```

#### Export all user data from specific OU:

```powershell
Get-ADUser -SearchBase "OU=Users,DC=Army,DC=Mil" -Filter * -Properties * |
Select-Object ObjectGUID, whenCreated, AccountExpirationDate, lastLogonTimestamp, @{name="MemberOf";expression={$_.memberof -join ";"}}, PasswordExpired, PasswordLastSet, PasswordNeverExpires, PasswordNotRequired, LastBadPasswordAttempt, badPwdCount, LockedOut |
Export-Csv ADUsersExport.csv -NoTypeInformation
```

---

### RSAT / ADUC Method (Alternative)

#### Open ADUC:

```plaintext
dsa.msc
```

#### Perform search and export:

- Use "Find" → "View" → "Choose Columns" → Add "Published At"
- Sort users and document OUs and user accounts manually

> **Operator Note:** This is slow and limited. Use only if PowerShell is not available.

---

## Running Script

* Use `UsersByOU.ps1` as example.
* Save results to incident directory for archiving.

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
| PowerShell + AD Module | Windows | Best and most scriptable export method |
| ADUC (RSAT - dsa.msc) | Windows | Manual enumeration and export |
| ADExplorer (Sysinternals) | Windows | AD object viewing, supports export (optional) |
| LDAP Search Tools (Linux, Mac) | Cross-platform | Lightweight, simple exports (optional use) |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Verify PowerShell and Active Directory module are installed and functioning.
- [ ] Verify domain access and credentials.
- [ ] Use PowerShell to export users from entire domain or per OU.
- [ ] Validate exported CSV file includes all key attributes.
- [ ] Compare user export to baseline for anomalies or unauthorized accounts.
- [ ] Secure exported file per SOP for analysis and chain of custody.

### Best Practices

- Always include user attributes that provide indicators of suspicious activity:
  - PasswordNeverExpires
  - AccountExpirationDate
  - LastBadPasswordAttempt
  - Enabled / Disabled status
  - MemberOf group information

- Avoid default “Get-ADUser -Properties *” unless filtering properties properly.
- Use PowerShell scripting for repeatability and auditability.
- Validate exports before archiving to ensure data integrity.

---

## References

[How Security Identifiers Work](https://technet.microsoft.com/en-us/library/cc778824(v=ws.10).aspx)  
[SID vs. GUID](https://technet.microsoft.com/en-use/library/cc961625.aspx)  
[List of PowerShell AD scripts](https://github.com/WiredPulse/PowerShell)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Enriched version with operator checklist, tooling, alt commands, best practices | Leo |
