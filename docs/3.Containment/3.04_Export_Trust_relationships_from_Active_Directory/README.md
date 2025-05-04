# Export Trust Relationships from Active Directory

## Task Export Trust Relationships from Active Directory

## Conditions

Given a Domain Controller (DC), domain account with appropriate permissions, and a domain workstation with Remote Server Administration Tools (RSAT) and PowerShell available.

> **Operator Note:** Identifying and reviewing domain trust relationships helps detect unexpected or unauthorized domain connections, which could be used for lateral movement or privilege escalation.

## Standards

* Team member obtains the correct name of the target domain.  
* Team member uses approved methods (PowerShell, WMI, or command line tools) to export trust relationship information to a local file for analysis.  
* Exported data includes:
  - Trust Partner
  - Trust Type
  - Trust Direction
  - Trust Attributes (Transitive/Non-Transitive)
  - Creation/Modification Dates (if available)

## End State

All Domain Trust relationship information has been exported to a local file and validated for review by the incident response team.

---

## Notes

The Active Directory Domain Services (AD DS) Cmdlets are available on:

- Windows Server 2012 R2 and higher
- Windows 8.1 and higher with RSAT installed

---

## Manual Steps

### PowerShell Method (Preferred)

#### Check and import AD module:

```powershell
Import-Module ActiveDirectory
```

#### Enumerate domain trusts using WMI:

```powershell
Get-WmiObject -Class Microsoft_DomainTrustStatus -Namespace ROOT\MicrosoftActiveDirectory |
Select-Object PSComputername, TrustedDomain, TrustAttributes, TrustDirection, TrustType |
Format-List
```

##### TrustAttributes meaning:

- `1` = Non-Transitive
- `2` = Transitive

##### TrustDirection meaning:

- `1` = Incoming Only
- `2` = Outgoing Only
- `3` = Two-way

#### Export trust relationships to CSV:

```powershell
Get-WmiObject -Class Microsoft_DomainTrustStatus -Namespace ROOT\MicrosoftActiveDirectory |
Select-Object PSComputername, TrustedDomain, TrustAttributes, TrustDirection, TrustType |
Export-Csv ".\DomainTrusts.csv" -NoTypeInformation
```

---

### Alternate Tools (Command-line and Other Methods)

#### NLTEST (Built-in Windows Command):

```cmd
nltest /domain_trusts
```

> **Operator Note:** `nltest` provides trust relationships including flags that indicate transitive and direct/indirect relationships.

#### Get-DomainTrust (PowerView - Optional/Advanced use)

```powershell
Import-Module .\PowerView.ps1
Get-DomainTrust
```

> **Operator Note:** PowerView provides advanced trust relationship analysis but should only be used if authorized.

---

## Running Script

Example script (PowerShell):

```powershell
Get-WmiObject -Class Microsoft_DomainTrustStatus -Namespace ROOT\MicrosoftActiveDirectory |
Export-Csv .\DomainTrusts.csv -NoTypeInformation
```

Save the exported file to a secure incident response working directory.

---

## Dependencies

* PowerShell and RSAT tools installed
* Domain Admin or delegated read access to trust relationships
* Optional: PowerView (if authorized for advanced domain enumeration)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell (WMI) | Windows | Primary trusted relationship export |
| nltest | Windows | Quick command-line trust verification |
| PowerView | Windows | Advanced trust and domain relationship analysis |
| Active Directory Domains and Trusts GUI | Windows | Visual review of trusts (no export feature) |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate access permissions and PowerShell module availability.
- [ ] Export domain trust relationships using WMI + PowerShell.
- [ ] Verify exported data contains all relevant attributes.
- [ ] Optionally use nltest for verification.
- [ ] Securely store exported trust relationship information.

### Best Practices

- Always use PowerShell or nltest for verifiable exports.
- Validate trust directions and attributes (watch for two-way trusts).
- Look for unexpected external domains or forest trusts.
- Cross-check modification dates for recent or unauthorized changes.

---

## References

[AD DS Cmdlets](https://technet.microsoft.com/en-us/library/hh852315(v=wps.630).aspx)  
[Get-DomainTrusts (PowerView)](https://github.com/WiredPulse/PowerShell/blob/master/Active_Directory/Get-DomainTrusts.ps1)  
[NLTEST Trusts Documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/nltest)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Full original + enriched PowerShell, alternate tooling, operator checklist and best practices | Leo |
