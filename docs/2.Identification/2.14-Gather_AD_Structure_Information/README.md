# Gather AD Structure Information

## Task Gather AD Structure Information

## Conditions

Given a suspected compromised network segment(s), access to a system that can gather the structure of the suspected domain.

> **Operator Note:** Enumerating Active Directory structure is essential during incident response to detect unexpected organizational units, rogue accounts, or misconfigured objects.

## Standards

* The team member identifies possibly compromised network segment(s).  
* The team member accesses a system that can enumerate the domain.  
* The team member utilizes various PowerShell cmdlets to enumerate the domain structure and/or users.  
  * Task 2.14 – Gather AD structure information.  
* The resulting enumerated data is compared to a known good AD configuration file to determine any anomalies present in the domain.

> **Operator Note:** Use baseline domain structure documentation when available to detect unauthorized additions or changes.

## End State

The structure of the domain is enumerated based on the specific sub-task accomplished and any anomalies on the domain will be identified.

> **Operator Note:** Findings should be shared with the incident lead and mission partner domain administrators.

## Manual Steps

### Gather Current Domain Information

```powershell
$adddomain = Get-ADDomain
$adddomain
```

Example output:

```plaintext
AllowedDNSSuffixes    : {}
ChildDomains          : {}
ComputersContainer    : CN=Computers,DC=team01,DC=tgt
--snipped--
```

### Gather Domain Organizational Units (OUs)

```powershell
Get-ADObject -Filter { ObjectClass -eq 'organizationalunit' } -Properties CanonicalName | Select-Object -Property CanonicalName
```

Example output:

```plaintext
CanonicalName
-------------
team01.tgt/Domain Controllers
team01.tgt/Microsoft Exchange Security Groups
team01.tgt/Groups
team01.tgt/Chula Vista
--snipped--
```

> **Operator Note:** This command lists the hierarchy of Organizational Units. Unexpected or newly created OUs should be reviewed carefully.

### Output

Output should follow naming convention format:

```
[mm/dd/yyyy_hh:mm:ss_AD_Structure_(xx.xx.xx.xx/x)]
```

Notify mission element lead and intelligence analyst of completion.

## Running Script

* Download script from 2.14_Gather_AD_Structure_Information.

* Run script:

```powershell
.\2.14_GatherADStructureInfo.ps1
```

Example script output:

```plaintext
[+] Writing Active Directory structure to C:\Users\btadmin\Desktop\CyberSurfers\CyberSurfers_ACL_1593261366.22937.txt...
[+] Done!
```

* The logfile will be saved to the `CyberSurfers` directory for review.

```powershell
cat .\CyberSurfers\CyberSurfers_ACL_1593261366.txt
```

Example contents:

```plaintext
[+] Domains infrastructure master: Team01-DC01.team01.tgt
[+] The Active Directory structure as follows:
DC=team01,DC=tgt
--> OU=Alameda
----> OU=Computers
----> OU=Groups
----> OU=Servers
----> OU=Users
--snipped--
```

## Dependencies

* PowerShell (Windows systems with RSAT installed)  
* Active Directory module for PowerShell (`RSAT: Active Directory`)

```powershell
Import-Module ActiveDirectory
```

> **Operator Note:** RSAT tools must be installed on the analyst workstation or jump box. Windows Server and domain-joined systems typically include them.

## Other available tools

* [ADExplorer (Sysinternals)](https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer)  
  * GUI tool for offline and online AD enumeration and snapshots.

* [BloodHound (Optional Advanced)](https://bloodhound.readthedocs.io/en/latest/)  
  * Graph-based AD relationship mapping. Used for advanced attack path discovery. (Use only when approved.)

* `dsquery` (legacy command-line):

```cmd
dsquery ou -limit 0
```

## References

[Microsoft PowerShell AD Module Cmdlets](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)  
[ADExplorer by Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer)  
[BloodHound AD Mapping](https://bloodhound.readthedocs.io/en/latest/)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate domain connectivity and appropriate credentials.
- [ ] Use PowerShell AD module for quick OU and domain enumeration.
- [ ] Run and save output of `2.14_GatherADStructureInfo.ps1` script.
- [ ] Review output for unauthorized or suspicious OUs.
- [ ] Notify lead and preserve reports in incident repository.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | PowerShell + AD Module | Primary enumeration tool |
| Windows | ADExplorer (Sysinternals) | Offline and advanced AD viewer |
| Universal | BloodHound (optional) | Advanced AD relationship and path analysis |

### Alternate Commands

#### Enumerate Domain Controllers:

```powershell
Get-ADDomainController -Filter *
```

#### Enumerate AD Sites:

```powershell
Get-ADReplicationSite -Filter *
```

#### Legacy dsquery:

```cmd
dsquery ou -limit 0
```

### Best Practices

- Use domain-joined workstations with RSAT for enumeration.
- Run with least privilege necessary, but admin rights may be required.
- Compare results with baseline domain documentation.
- Preserve outputs securely for incident timeline reconstruction.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched AD enumeration tooling, procedures, and operator notes | Leo |
