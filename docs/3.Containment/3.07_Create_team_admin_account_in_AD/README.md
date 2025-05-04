# Create Team Admin Account in Active Directory (AD)

## Task Create Team Admin Account in Active Directory

## Conditions

Given a target Domain, a Domain Controller (DC), a user account with appropriate rights and privileges to modify user accounts, and a workstation with the Windows Remote Server Administration Tools (RSAT) installed.

> **Operator Note:** Creating a temporary or team admin account is often necessary to secure the environment during incident response. This should only be done with proper approvals and logged actions.

## Standards

* Team member coordinates the requirement to modify the Administrator Group on the Domain  
* Team member creates User Account in Active Directory on target Domain  
* Team member opens Active Directory Users and Computers (ADUC) or uses PowerShell to create the user account  
* Team member assigns Domain Admins group membership to the account  
* Team member resets the password and secures the credentials in accordance with incident response SOPs  

> **Operator Note:** Always consult enclave or business owner prior to creating privileged accounts during an incident.

## End State

New user account is created in Active Directory with Domain Admin credentials that the Team can utilize to carry out protection tasks.

---

## Notes

- Team admin accounts should be temporary and removed after incident resolution.
- Strong, complex passwords should be used.
- Account creation and group membership assignment should be logged.

---

## Manual Steps

### Create Active Directory User (GUI Method)

1. Open **Active Directory Users and Computers (ADUC)**:  
   - Click Start → Administrative Tools → Active Directory Users and Computers  
   - Navigate to the domain node (example.com)

2. Right-click the folder or OU where you want to create the user → **New → User**

3. Fill out the user creation form:  
   - First name, Initials, Last name  
   - User logon name (username) → Click **Next**

4. Enter password and select appropriate options:  
   - Recommended: User must change password at next logon (can be unchecked during IR)  
   - Click **Next** → Click **Finish**

---

### Assign Group Membership (GUI Method)

1. Open **Active Directory Users and Computers (ADUC)**  
2. Navigate to **Domain Admins** group  
3. Right-click → **Properties**  
4. Go to **Members tab** → Click **Add**  
5. Enter the new user’s username → Click **OK** → User is now a Domain Admin

---

### PowerShell Method (Preferred for repeatability)

#### Create new user:

```powershell
New-ADUser -Name "IR Team Admin" -SamAccountName "iradmin" -AccountPassword (Read-Host -AsSecureString "Enter Password") -Enabled $true -Path "OU=Incident Response,DC=example,DC=com"
```

#### Add user to Domain Admins:

```powershell
Add-ADGroupMember -Identity "Domain Admins" -Members "iradmin"
```

> **Operator Note:** Replace "OU=Incident Response,DC=example,DC=com" with the correct OU path if needed.

---

## Running Script

```powershell
New-ADUser -Name "IR Team Admin" -SamAccountName "iradmin" -AccountPassword (Read-Host -AsSecureString "Enter Password") -Enabled $true -Path "OU=Incident Response,DC=example,DC=com"

Add-ADGroupMember -Identity "Domain Admins" -Members "iradmin"
```

Store these commands securely and remove or disable the account after incident remediation.

---

## Dependencies

* RSAT with Active Directory Module (PowerShell)
* Domain Admin credentials or delegated permissions
* Organizational approval (business owner or enclave owner authorization)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell AD Module | Windows | Preferred scripted method |
| ADUC (dsa.msc) | Windows | GUI-based user creation |
| dsadd (legacy) | Windows | CLI-based user creation (optional) |
| LAPS / Password Vaulting Tools | Windows/Enterprise | Secure storage of credentials |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain approval to create Domain Admin account.
- [ ] Create user account with unique username (avoid generic names).
- [ ] Assign temporary secure password.
- [ ] Add user to Domain Admins group using PowerShell or GUI.
- [ ] Document creation in incident notes and secure credentials.
- [ ] Remove account after incident unless promoted to permanent.

### Best Practices

- Use PowerShell for speed, repeatability, and logging.
- Document all elevated account creation during incident.
- Disable and remove temporary accounts immediately after use.
- Securely share passwords (LAPS, encrypted vaults).
- Avoid generic names like "admin" or "incident" that may persist post-incident.

---

## References

[Create user in Active Directory](https://technet.microsoft.com/en-us/library/dd894463(v=ws.10).aspx)  
[Assign Group Membership](https://technet.microsoft.com/en-us/library/dd894436(v=ws.10).aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.9 | Full original + enriched PowerShell method, operator checklist, alternate tooling and best practices | Leo |
