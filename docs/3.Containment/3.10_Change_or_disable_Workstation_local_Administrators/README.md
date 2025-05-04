# Change or Disable Workstation Local Administrator Account

## Task Change or Disable Workstation Local Administrator Account

## Conditions

Given domain credentials with the appropriate permissions, and an incident response workstation with RSAT or local admin tools available.

> **Operator Note:** Local Administrator accounts on workstations are a well-known target for attackers. These accounts should be disabled or removed when possible to reduce lateral movement and persistence.

## Standards

* Team member coordinates the requirement to disable or modify the Local Workstation Administrator Account.  
* Team member uses Group Policy to enforce the change across the workstation fleet (preferred method).  
* Team member uses local commands (Net User, PowerShell) as needed during rapid response or targeted actions.  
* Team member documents all accounts changed or disabled.

## End State

Workstation Local Administrator account is either disabled or has been renamed and removed from all local privileged groups, reducing risk of abuse.

---

## Notes

- Disabling or modifying local Administrator accounts helps prevent credential reuse and Pass-the-Hash attacks.
- Use Group Policy Objects (GPOs) for centralized and scalable enforcement.
- Use PowerShell or Net User for one-off, manual workstation actions.
- Where possible, replace static Local Administrator accounts with managed solutions (e.g. LAPS).

---

## Manual Steps

### Method 1: Group Policy (Preferred)

#### Open Group Policy Management Console (GPMC)

```plaintext
gpmc.msc
```

#### Create a new Group Policy Object (GPO):

- Right-click Domain → Create a GPO → name it “Disable Local Admin Account”
- Right-click → Edit → go to:

```plaintext
Computer Configuration → Windows Settings → Security Settings → Local Policies → Security Options
```

#### Set the following policy:

```plaintext
Accounts: Administrator account status → Disabled
```

#### Link GPO to Workstation OU:

- Drag and drop or Link existing GPO to workstation OU.
- Force Group Policy update on workstations:

```cmd
gpupdate /force
```

> **Operator Note:** Only link to Workstation OU, not Servers or Domain Controllers.

---

### Method 2: Command Line (Net User)

For individual machines or small groups:

```cmd
net user administrator /active:no
```

> **Operator Note:** This disables the local administrator account immediately.

---

### Method 3: PowerShell (Alternate)

```powershell
Disable-LocalUser -Name "Administrator"
```

> **Operator Note:** PowerShell method requires PowerShell 5.1+ and is useful for scripting.

---

## Running Script

```powershell
# Disable Local Administrator on a remote workstation
Invoke-Command -ComputerName Workstation01 -ScriptBlock {
    Disable-LocalUser -Name "Administrator"
}
```

> **Operator Note:** Use with caution. Use GPO for fleet-wide, consistent deployment.

---

## Dependencies

* RSAT with Group Policy Management Console (for domain GPO)  
* Domain Admin or delegated permissions  
* Local administrator rights (if running manually on workstation)  
* PowerShell 5.1+ for `Disable-LocalUser` command (optional method)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| Group Policy Management Console (GPMC) | Windows | Preferred method to disable local admin accounts across all workstations |
| Net User (cmd) | Windows | Simple and quick disable |
| PowerShell (Disable-LocalUser) | Windows | Scripted and remote disable |
| LAPS (Local Administrator Password Solution) | Windows Enterprise | Recommended to manage local admin passwords and eliminate shared password usage |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Coordinate with enclave owner or business owner before disabling accounts.
- [ ] Use Group Policy for scalable and managed disablement.
- [ ] For manual work, use Net User or PowerShell.
- [ ] Validate disablement with test logon attempts or `net user administrator`.
- [ ] Document changes in incident records.
- [ ] Plan for re-enabling or removing GPO after IR activity.

### Best Practices

- Always prefer GPO for scalability and accountability.
- Disable, do not delete, Administrator accounts unless required.
- Use LAPS wherever possible to manage local admin credentials securely.
- Retain ability for emergency local logon where required (e.g. single use recovery account).

---

## References

[Windows 7 Security Technical Implementation Guide](https://iase.disa.mil/stigs/)  
[Enable / Disable Administrator account](https://technet.microsoft.com/en-us/library/dd744293(v=ws.10).aspx)  
[Creating Group Policy Object](https://technet.microsoft.com/en-us/library/cc754740(v=ws.11).aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 2.0 | Full original + expanded operator methods (GPO, CLI, PowerShell), best practices, alternate tooling and checklist | Leo |
