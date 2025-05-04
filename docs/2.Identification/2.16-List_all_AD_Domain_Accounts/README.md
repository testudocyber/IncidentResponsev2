# List All AD Domain Accounts

## Task Identify all AD Domain Accounts

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and view the list of AD accounts as is.

> **Operator Note:** Identifying all AD domain accounts allows responders to detect new or unauthorized accounts created by threat actors.

## Standards

* The team member accesses the compromised system.  
* The team member uses the approved script or tools to output the AD users to a text file for analysis.  
* The resulting scan data provides a list of AD users for later correlation and investigation.

> **Operator Note:** Ensure export includes key attributes (account name, status, OU) for effective analysis.

## End State

All users of the domain are enumerated and any newly created accounts will be identified.

> **Operator Note:** Reported user list should be shared with mission owner and reviewed for suspicious accounts, disabled accounts re-enabled, and naming convention anomalies.

## Manual Steps

### PowerShell (local or remote with RSAT)

* Get all users of the current domain:

```powershell
Get-ADUser -Filter *
```

* Count number of users:

```powershell
$dcUsers = Get-ADUser -Filter *
$dcUsers.Count
```

Example:

```
6609
```

* List disabled AD accounts:

```powershell
Get-ADUser -Filter { Enabled -eq "False" } | Select-Object SamAccountName
```

* List all accounts with attributes:

```cmd
dsquery * -limit 0
```

Example:

```
"CN=Sochan\, Carlenel,OU=Users,OU=Santa Ana,DC=team01,DC=tgt"
"CN=Team01-WK4355,OU=Computers,OU=Los Angeles,DC=team01,DC=tgt"
```

> **Operator Note:** dsquery output provides Distinguished Name (DN), useful for OU and account path analysis.

## Running Script

* Run the `2.16_ListADDomainAccounts.ps1` script:

```powershell
.\2.16_ListADDomainAccounts.ps1
```

* Example output:

```plaintext
[+] Current Domain: team01.tgt
[+] There are 6609 users in team01.tgt
[+] Writing domain users to all_ad_domain_accounts.txt...
[+] Done!
```

* Review output file:

```plaintext
C:\Path\To\all_ad_domain_accounts.txt
```

## Dependencies

* PowerShell
* RSAT: Active Directory Module for PowerShell

Install RSAT (if missing):

```powershell
Add-WindowsCapability -Online -Name "Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0"
```

## Other available tools

* **Net User (Basic command):**

```cmd
net user /domain
```

* **ADExplorer (Sysinternals):** GUI for viewing AD objects  
* **PowerView (Optional advanced use):** Advanced AD enumeration

> **Operator Note:** PowerView should be used carefully and only when authorized. ADExplorer is suitable for offline review.

## References

[PowerShell AD Module Cmdlets](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)  
[Sysinternals ADExplorer](https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate connectivity and admin rights.
- [ ] Run PowerShell or provided script to export full user list.
- [ ] Review for disabled accounts, new accounts, and naming anomalies.
- [ ] Save and secure export file.
- [ ] Compare against known baselines (if available).

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | PowerShell + AD Module | Primary AD user enumeration |
| Windows | dsquery | Legacy quick command |
| Universal | ADExplorer | GUI-based AD object viewer |
| Advanced (Windows/Linux) | PowerView | Advanced AD enumeration (optional use) |

### Alternate Commands

#### Get AD users and export to CSV:

```powershell
Get-ADUser -Filter * | Export-Csv -Path C:\ADUsers.csv -NoTypeInformation
```

#### Get disabled users only:

```powershell
Search-ADAccount -AccountDisabled | Select-Object Name, Enabled
```

### Best Practices

- Run enumeration after confirming IR authority.
- Schedule large queries during off-peak hours.
- Use CSV format for easier sorting/filtering.
- Validate new or unexpected accounts with system owner.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched AD user enumeration tooling, PowerShell scripts, and operator recommendations | Leo |
