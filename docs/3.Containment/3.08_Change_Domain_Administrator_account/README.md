# Change Domain Administrator Account

## Task Change Local Administrator Account Password on Domain Controller (DC)

## Conditions

Given a target Domain, a Domain Controller (DC), a user account with appropriate rights and privileges to modify user accounts, and a workstation with the Windows Remote Server Administration Tools (RSAT) installed.

> **Operator Note:** Changing the domain Administrator account password should be performed carefully. In some environments, this account may be tied to services and automated processes. Coordinate with enclave owners before proceeding.

## Standards

* Team member coordinates the requirement to modify the Administrator account on the DC.  
* Team member locates the built-in Administrator account using Active Directory Users and Computers (ADUC) or PowerShell.  
* Team member resets the password using GUI or PowerShell and confirms the change.  
* Team member documents the change, including the approval, new password storage location, and time of modification.

## End State

The target domain Administrator account password has been changed successfully and securely documented.

---

## Notes

Mission Element lead ought to request this capability from the business or enclave owner in order to secure privileged accounts. Password changes can disrupt services and should be closely coordinated.

---

## Manual Steps

### Caution

Changing the domain Administrator password should be coordinated with the system owner. The account may be tied to critical services or applications.

### PowerShell Method (Preferred)

#### Using CNAME (Distinguished Name)

```powershell
Set-ADAccountPassword 'CN=Administrator,CN=Users,DC=example,DC=com' -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rd!" -Force)
```

#### Using SAM Account Name

```powershell
Set-ADAccountPassword -Identity Administrator -OldPassword (ConvertTo-SecureString -AsPlainText "OldP@ssw0rd!" -Force) -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rd!" -Force)
```

> **Operator Note:** Use `-Reset` when old password is not known (privileged reset) or `-OldPassword` when performing a routine password change.

### GUI Method (ADUC - Alternative)

1. Open **Active Directory Users and Computers (dsa.msc)**  
2. Navigate to **Users → Administrator account**  
3. Right-click → **Reset Password**  
4. Enter new password and confirm  
5. Ensure password complexity requirements are met

> **Operator Note:** GUI method does not allow for old password verification and uses a privileged reset.

---

## Running Script

```powershell
Set-ADAccountPassword -Identity Administrator -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rd!" -Force)
```

Store the password securely in an authorized password vault or encryption-protected incident notes.

---

## Dependencies

* RSAT with Active Directory Module installed
* Domain Admin privileges
* Coordination with System Owner / Enclave Owner
* Secure password vault or protected storage for credentials

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + AD Module | Windows | Primary method (automated and logged) |
| ADUC (dsa.msc) | Windows | GUI method (manual password reset) |
| LAPS (Local Admin Password Solution) | Enterprise | Password rotation and secure storage (if deployed) |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate approval from system owner/enclave owner.
- [ ] Verify account dependencies (services, scripts) before changing password.
- [ ] Use PowerShell `Set-ADAccountPassword` or ADUC to change password.
- [ ] Record change (time, operator, method).
- [ ] Store new password in encrypted and authorized location.
- [ ] Validate functionality after change (logon test, service checks).

### Best Practices

- Perform changes during maintenance windows or coordinated response.
- Avoid using common passwords → use randomly generated complex passwords.
- Document who approved and performed the change.
- Remove or rotate temporary passwords post-incident.

---

## References

[PowerShell Set-ADAccountPassword documentation](https://technet.microsoft.com/en-us/library/ee617261)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Original + enriched with PowerShell + GUI methods, operator checklist, alternate tools, best practices | Leo |
