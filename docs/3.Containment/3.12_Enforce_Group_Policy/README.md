# Enforce Group Policy Compliance

## Task Enforce Group Policy Compliance Across Workstations and Servers

## Conditions

Given a target Domain, domain credentials with appropriate permissions, and systems joined to Active Directory, the operator will validate and enforce Group Policy Objects (GPO) to ensure security baselines and containment policies are in effect.

> **Operator Note:** During an incident, it is critical that GPOs apply immediately and accurately to reduce attacker freedom of movement and ensure systems are properly hardened.

## Standards

* Team member determines appropriate scope (Workstations, Servers, or entire Domain).  
* Team member verifies that Group Policy Objects are correctly configured and linked.  
* Team member uses approved methods to force GPO updates to target systems.  
* Team member validates GPO application via system logs or resultant policy reporting.  
* Team member records actions and confirms impact with enclave or system owner.

## End State

Group Policy is confirmed to be applied to all in-scope systems and verified as compliant with incident containment requirements.

---

## Notes

- GPO is enforced automatically at defined intervals, but IR situations require manual enforcement.  
- Use Group Policy Management Console (GPMC) and `gpupdate` for immediate application.  
- PowerShell and enterprise tools can automate mass enforcement.

---

## Manual Steps

### Method 1: Group Policy Management Console (GPMC)

#### Open GPMC

```plaintext
gpmc.msc
```

#### Validate GPO Link and Enforcement

- Ensure correct GPOs are linked to appropriate OUs.
- Check "Enforced" flag is enabled where applicable.
- Use "Group Policy Results Wizard" to verify resultant set of policies on targets.

---

### Method 2: Force GPO Refresh (gpupdate)

#### On local or remote systems:

```cmd
gpupdate /force
```

> **Operator Note:** `/force` reapplies all policies, not just those that have changed.

#### Remote with PowerShell (single system):

```powershell
Invoke-GPUpdate -Computer "Workstation01" -Force
```

#### Remote with PowerShell (bulk systems):

```powershell
Get-ADComputer -Filter * -SearchBase "OU=Workstations,DC=example,DC=com" |
ForEach-Object {
    Invoke-GPUpdate -Computer $_.Name -Force
}
```

> **Operator Note:** Ensure WinRM is enabled for remote PowerShell invocation.

---

### Method 3: Validate GPO Application

#### Use Resultant Set of Policy (RSOP)

```cmd
rsop.msc
```

Or via PowerShell:

```powershell
Get-GPResultantSetOfPolicy -ReportType Html -Path ".\GPOReport.html"
```

> **Operator Note:** This produces a full report of applied GPOs for validation.

---

### Method 4: Local Group Policy Object (Optional for non-domain systems)

```powershell
LGPO.exe /b backup_folder
LGPO.exe /g
```

> **Operator Note:** `LGPO.exe` is useful for standalone hosts but normally unnecessary in domain environments.

---

## Running Script (Bulk Update Example)

```powershell
$Computers = Get-ADComputer -Filter * -SearchBase "OU=Workstations,DC=example,DC=com"

foreach ($Computer in $Computers) {
    Invoke-GPUpdate -Computer $Computer.Name -Force
}
```

> **Operator Note:** This ensures that all workstations receive updated GPO during containment.

---

## Dependencies

* RSAT (Group Policy Management Console and PowerShell Module)
* Domain Admin or delegated Group Policy permissions
* WinRM enabled for remote enforcement
* Connectivity to Domain Controllers

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| GPMC (gpmc.msc) | Windows | Primary policy management and enforcement |
| gpupdate (cmd) | Windows | Local refresh |
| Invoke-GPUpdate (PowerShell) | Windows | Remote refresh |
| rsop.msc | Windows | Validate resultant set of policies |
| LGPO.exe | Windows | Standalone hosts or forensic application of local policy |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate GPO links and scope in GPMC.
- [ ] Identify target systems that need forced policy application.
- [ ] Use `gpupdate` or `Invoke-GPUpdate` to refresh GPO on target systems.
- [ ] Validate applied policies using RSOP or GPO reports.
- [ ] Document enforcement actions in IR tracking tools.

### Best Practices

- During incident response, enforce GPO only on impacted systems or OUs to minimize disruption.
- Use PowerShell for bulk enforcement actions when speed is required.
- Validate policies have successfully applied using `gpresult` or RSOP.
- Coordinate with enclave or system owners before pushing policy changes.

---

## References

[Group Policy Management Console (GPMC)](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/gpmc)  
[GPUpdate command documentation](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/gpupdate)  
[PowerShell Invoke-GPUpdate](https://learn.microsoft.com/en-us/powershell/module/grouppolicy/invoke-gpupdate)  
[LGPO Utility](https://www.microsoft.com/en-us/download/details.aspx?id=55319)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for enforcing GPO with methods, validation, and best practices | Leo |
