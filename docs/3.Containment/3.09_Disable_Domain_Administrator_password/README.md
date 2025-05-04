# Disable Domain Administrator Password

## Task Protect Domain Administrator Account

## Conditions

Given a target Domain, a Domain Controller (DC), a user account with appropriate rights and privileges to modify user accounts, and a workstation with the Windows Remote Server Administration Tools (RSAT) installed.

> **Operator Note:** Disabling, renaming, and clearing the built-in Domain Administrator account is a defense-in-depth control that is strongly recommended by CIS Benchmarks and DISA STIG, but must be performed with caution to avoid domain lockout.

## Standards

* Team member coordinates with the business or enclave owner prior to modifying the Domain Administrator account.  
* Team member renames the built-in Domain Administrator account.  
* Team member creates a dummy "Administrator" account with no privileges.  
* Team member disables the built-in Administrator account.  
* Team member clears any descriptive attributes to avoid revealing account details.  

## End State

The built-in Domain Administrator account has been renamed and disabled. A dummy "Administrator" account with no rights has been created.

---

## Notes

Mission Element lead ought to request this capability of the business owner prior to execution.

Disabling or renaming the Domain Administrator account prevents attackers from easily identifying or abusing the account during lateral movement.

Microsoft does not recommend full deletion or disablement of the original Administrator account unless carefully coordinated.

---

## Manual Steps

### Open Active Directory Users and Computers (GUI)

```bat
dsa.msc
```

- Navigate to **Users**
- Locate the **Administrator** account

---

### Rename the built-in Administrator account (WMIC)

```bat
wmic useraccount where name='Administrator' call rename name='NewAdminName'
```

> **Operator Note:** Choose a non-obvious name to avoid easy detection (e.g. "SysOps-Backup").

---

### Create a dummy Administrator account with no permissions

```bat
net user Administrator C0mplex_P@ssword /ADD /PASSWORDCHG:NO
```

> **Operator Note:** Use a complex, randomly generated password.

---

### Disable the dummy Administrator account

```bat
net user Administrator /active:no
```

---

### Remove description and attributes (Optional but recommended)

```powershell
Set-ADUser Administrator -Description ""

Set-ADUser -Identity Administrator -City "" -Clear "" -Company "" -Country "" -Department "" -Description "" -DisplayName "" -Division "" -EmailAddress "" -EmployeeID "" -EmployeeNumber "" -Fax "" -GivenName "" -HomeDirectory "" -HomeDrive "" -HomePage "" -HomePhone "" -Initials "" -LogonWorkstations "" -MobilePhone "" -Office "" -OfficePhone "" -Organization "" -OtherName "" -POBox "" -PostalCode "" -ProfilePath "" -SamAccountName "" -ScriptPath "" -State "" -StreetAddress "" -Surname "" -Title "" -UserPrincipalName "" -Partition "" -Server ""
```

> **Operator Note:** This minimizes information disclosure for dummy accounts.

---

## Running Script (Example automated flow)

```powershell
# Rename default administrator account
wmic useraccount where name='Administrator' call rename name='IRAdminBackup'

# Create dummy Administrator account
net user Administrator ComplexPass123! /ADD /PASSWORDCHG:NO

# Disable dummy Administrator account
net user Administrator /active:no

# Clear AD attributes (optional step)
Set-ADUser Administrator -Description ""
```

---

## Dependencies

* RSAT with Active Directory Module installed
* Domain Admin or equivalent privileges
* Approval from System/Enclave owner
* Strong random password generator (optional)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + AD Module | Windows | AD attribute clearing, disable account |
| WMIC | Windows | Rename local Administrator |
| net user | Windows | Create dummy accounts, disable |
| ADUC (dsa.msc) | Windows | Visual/manual verification |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain approval before proceeding.
- [ ] Rename the built-in Administrator account.
- [ ] Create a dummy "Administrator" account.
- [ ] Disable the dummy account immediately after creation.
- [ ] Clear dummy account AD attributes.
- [ ] Document all changes and secure new credentials.

### Best Practices

- Rename built-in Administrator account to a unique name.
- Use randomly generated passwords for dummy accounts.
- Clear all descriptive fields to prevent enumeration.
- Document approvals and execution steps in IR log.

---

## References

[DISA STIGS](https://iase.disa.mil/stigs)  
[CIS Benchmark downloads](http://learn.cisecurity.org/benchmarks)  
[Securing AD Administrative Groups and Accounts](https://technet.microsoft.com/en-us/library/cc700835.aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Original + enhanced operator flow, PowerShell automation, alternate tooling and recommendations | Leo |
