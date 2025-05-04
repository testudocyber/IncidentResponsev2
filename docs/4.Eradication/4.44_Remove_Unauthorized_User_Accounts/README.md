# 4.44 Remove Unauthorized User Accounts

## Task Identify and Remove Unauthorized or Attacker-Created User Accounts

## Conditions

Given access to impacted systems and domain infrastructure, the operator will enumerate user accounts across hosts and domain, validate account legitimacy, and remove any unauthorized, malicious, or attacker-created accounts.

> **Operator Note:** Attackers often create new user accounts, escalate privileges of existing accounts, or hide in unused accounts to maintain access. Detection and removal of unauthorized accounts is critical for successful eradication.

## Standards

* Team member enumerates all user accounts across impacted systems and the domain.
* Team member validates accounts against approved and known user/account lists.
* Team member disables and removes unauthorized or attacker-created accounts.
* Team member records actions and retains audit logs of removed accounts.
* Team member coordinates with system owners to validate no business accounts are accidentally removed.

## End State

All attacker-created or unauthorized accounts have been identified, validated, removed, and documented. Access control integrity has been restored.

---

## Notes

- Always disable accounts before deleting → this prevents accidental loss of evidence or impacting system services.
- Work with system owners and HR/management to validate questionable accounts.
- Retain logs of removed accounts and actions for lessons learned and forensic evidence.

---

## Manual Steps

### Step 1: Enumerate User Accounts on Hosts

#### Windows (Local Accounts)

```powershell
Get-LocalUser
net user
```

#### Domain Accounts (Active Directory)

```powershell
Get-ADUser -Filter * | Select-Object Name, SamAccountName, Enabled, LastLogonDate
```

#### Linux (Local Accounts)

```bash
cat /etc/passwd
getent passwd
```

#### macOS (Local Accounts)

```bash
dscl . list /Users
```

> **Operator Note:** Pay close attention to accounts with UID < 500 on macOS (system accounts) and accounts without login history or descriptions.

---

### Step 2: Identify Unauthorized or Suspicious Accounts

- Look for:
  - Accounts created recently during attack timeframe.
  - Accounts with administrative privileges.
  - Accounts with non-standard naming schemes (e.g., `svc-updates`, `test123`, `adm1n`).
  - Accounts that never logged in or have no owner.

- Validate with:
  - HR records (employee/contractor list).
  - System/application owners.
  - IR Team and enclave owner.

---

### Step 3: Disable Suspicious or Unauthorized Accounts

#### Windows (Local)

```powershell
Disable-LocalUser -Name "suspiciousaccount"
```

#### Windows (Domain)

```powershell
Disable-ADAccount -Identity "suspiciousaccount"
```

#### Linux

```bash
sudo usermod --lock suspiciousaccount
```

#### macOS

```bash
sudo pwpolicy -u suspiciousaccount disableuser
```

---

### Step 4: Remove Unauthorized Accounts (Post Validation)

#### Windows (Local)

```powershell
Remove-LocalUser -Name "suspiciousaccount"
```

#### Windows (Domain)

```powershell
Remove-ADUser -Identity "suspiciousaccount"
```

#### Linux

```bash
sudo userdel suspiciousaccount
```

#### macOS

```bash
sudo dscl . -delete /Users/suspiciousaccount
```

---

### Step 5: Document Actions

- Log account name, system, actions taken, and operator performing the action.
- Retain before/after snapshots of account lists.
- Note justification for account removal.

```plaintext
[2025-05-02] Removed unauthorized user "svc-update" from HOST123. Account confirmed attacker-created. Approved by IR Lead. Operator: Leo
```

---

## Running Script (Windows - List All Users and Last Login)

```powershell
Get-LocalUser | Select-Object Name, Enabled, LastLogon
```

#### Linux (Identify Users With No Logins)

```bash
lastlog | grep "**Never logged in**"
```

> **Operator Note:** Accounts with no logins and unusual names are often used as backdoors.

---

## Dependencies

* Administrative/root access on all hosts and domain controllers
* Coordination with system owners and HR for validation
* Audit logs or historical account data (if available)
* IR team approval for account removal actions

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell | Windows | Built-in | Enumerate and remove local/domain users |
| Active Directory Users & Computers | Windows (Domain) | Built-in MMC snap-in | Visual account validation and removal |
| dscl | macOS | Built-in | Manage local users and groups |
| userdel / passwd | Linux | Built-in | Remove and lock local users |

> **Operator Note:** Always lock/disable first before deleting to prevent accidental service impact.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate all user accounts (host + domain).
- [ ] Validate all accounts against approved lists.
- [ ] Disable unauthorized accounts prior to removal.
- [ ] Remove unauthorized accounts after validation.
- [ ] Document removal actions and update incident records.
- [ ] Retain logs and audit trails for future reference.

### Best Practices

- Coordinate with HR and system owners to prevent false positives.
- Disable accounts first to allow rollback if removal impacts critical services.
- Use native OS tools whenever possible for auditability.
- Document extensively → account removal is often reviewed in post-incident reporting.

---

## References

[Microsoft PowerShell Local Accounts Cmdlets](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.localaccounts/?view=powershell-7.2)  
[Linux User Management](https://linuxize.com/post/how-to-add-and-delete-users-on-ubuntu-18-04/)  
[Apple macOS dscl Command](https://developer.apple.com/library/archive/documentation/OpenDirectory/Conceptual/DirectoryServices/Tasks/EditingDSRecords.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for removing unauthorized accounts with platform guidance, tools, checklist | Leo |
