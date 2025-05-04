# AD Configuration Scan with PowerShell

## Task Evaluate Active Directory (AD) Configurations using PowerShell

## Conditions

Given an incident response workstation with PowerShell configured, Domain Administrator-level credentials, and a target Domain.

> **Operator Note:** Authorization from the network owner is required before accessing and scanning AD structures.

## Standards

* Team member verifies connectivity to the Domain Controller with provided credentials.  
* Team member ensures the Active Directory module has been imported into the PowerShell console prior to running scan.  
* Team member runs a PowerShell script utilizing AD cmdlets to capture the following AD-related information to an output file:
    * Domain Controllers (DCs)
    * Domain Groups
    * Domain Organizational Units (OUs)
    * Domain Computers
    * Domain Users
    * Domain Service Accounts
    * Group Membership
    * OU Membership
* Team member saves the output file for comparison to later evaluations.

> **Operator Note:** Captured data should be stored securely and compared to baselines to identify unauthorized changes.

## End State

AD configuration information for the target domain is output to a text or XML file for later comparison.

> **Operator Note:** Reports should be retained for post-incident analysis and forensic review.

## Manual Steps

Using a team laptop that is NOT a member of the domain:

1. Launch `2.15_AD_Enumeration_FromExternal.ps1` PowerShell script.  
2. Input required information when prompted:

```
Domain credentials: DOMAIN\username (i.e. team02\Administrator)
Domain Controller name: (i.e. team02-dc01)
Domain name: (i.e. team02.tgt)
```

3. Allow script to run (may take 5–10 minutes).
4. Upon completion, review exported results:

```plaintext
C:\Temp\export.txt
```

5. To compare results later:

- Save previous export to different directory.
- Use Notepad++ with "Compare" plugin or similar diff tool to identify changes.

> **Operator Note:** Comparing AD structure over time helps detect persistence mechanisms such as rogue service accounts or group modifications.

## Running Script

* The following script should be executed with PowerShell:

```powershell
.\2.15_AD_Enumeration_FromExternal.ps1
```

* Follow on-screen prompts to input domain and credential information.

* Output file is saved to:

```plaintext
C:\Temp\export.txt
```

## Dependencies

* PowerShell (recommended: v5+ or PowerShell 7)
* Active Directory PowerShell Module (installed via RSAT on Windows)

```powershell
Import-Module ActiveDirectory
```

## Other available tools

* Windows Command Line (dsquery, net group /domain)
* ManageEngine ADManager Plus and ADAudit Plus (optional enterprise tools)
* PowerView (for advanced operator enumeration - use only when authorized)

```powershell
Import-Module PowerView
Get-NetDomain
Get-NetOU
Get-NetUser
```

> **Operator Note:** PowerView is an advanced operator tool useful for deeper relationship enumeration (offensive/defensive). Use cautiously.

## References

[Microsoft PowerShell Overview](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7)  
[RSAT: Active Directory Module](https://learn.microsoft.com/en-us/powershell/active-directory/install/overview)  
[PowerView GitHub](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm Domain connectivity and required credentials.
- [ ] Run PowerShell AD Enumeration script and collect export.
- [ ] Store and secure exported AD configuration files.
- [ ] Use Notepad++, diff tools or automated solutions to compare AD state over time.
- [ ] Investigate any unauthorized changes immediately with AD administrators.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | PowerShell + AD Module | Primary method for AD enumeration |
| Windows | ADManager Plus, ADAudit Plus | GUI-based enterprise tools |
| Windows/Linux | PowerView (optional, advanced use) | Advanced AD enumeration for operators |

### Alternate Commands

#### Enumerate Domain Controllers (quick check):

```powershell
Get-ADDomainController -Filter *
```

#### Enumerate all AD Users:

```powershell
Get-ADUser -Filter * -Properties *
```

#### Legacy (CMD):

```cmd
net group "Domain Admins" /domain
```

### Best Practices

- Run during off-peak hours if targeting large domains.
- Validate script and module versions to avoid compatibility issues.
- Archive all export and comparison outputs for audit and forensics.
- Conduct delta analysis post-scan for unusual changes.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched PowerShell AD configuration scan procedures and tooling | Leo |
