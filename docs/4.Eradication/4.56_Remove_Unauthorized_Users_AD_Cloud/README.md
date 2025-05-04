# 4.56 Remove Unauthorized Users (Local/AD/Cloud)

## Task

Identify and remove unauthorized or suspicious user accounts across local systems, Active Directory (AD), and Cloud Identity Providers (Azure AD, AWS IAM, Google Workspace).

---

## Conditions

Given access to local systems, enterprise identity directories, and cloud tenant management interfaces.

---

## Standards

* Identify all accounts and validate against authorized user lists.
* Detect unauthorized, dormant, or suspicious accounts.
* Remove or disable unauthorized accounts.
* Validate removal and document actions taken.

---

## End State

All unauthorized user accounts are identified, removed, and documented across local, enterprise, and cloud environments.

---

## Notes

- User accounts are a primary attack vector for persistence and privilege escalation.
- Dormant and unauthorized accounts should be treated as serious threats.
- Proper coordination with HR, IT, and system owners is required before removal.

---

## Manual Steps

### Step 1: Identify All Active User Accounts

#### Local Accounts (Windows)

```powershell
Get-LocalUser
```

Look for:

- Disabled = False
- LastLogonDate = stale or never

#### Local Accounts (Linux/macOS)

```bash
cat /etc/passwd
```

Look for users with UID >= 1000 (normal users).

```bash
lastlog
```

Look for users that have never logged in or have not logged in for long periods.

#### Active Directory (Windows Server)

```powershell
Get-ADUser -Filter * -Properties LastLogonDate | Select-Object Name, Enabled, LastLogonDate
```

Look for:

- Enabled = True
- LastLogonDate = Never or stale

---

#### Azure AD (Microsoft 365 Cloud)

```powershell
Connect-AzureAD
Get-AzureADUser -All $true | Select-Object DisplayName, UserPrincipalName, AccountEnabled
```

Look for:

- AccountEnabled = True
- UsageLocation / Last Login missing or stale

---

#### AWS IAM (Cloud)

```bash
aws iam list-users
aws iam get-user --user-name <username>
```

Look for:

- Users with no usage/activity
- Users with Console login enabled but no login history

> **Operator Note:** AWS IAM → `lastUsed` attribute can indicate if dormant.

---

#### Google Workspace (Cloud)

Admin Console → Directory → Users → Filter by login activity

Look for:

- Never logged in
- Last login over 90 days

> **Operator Note:** Dormant accounts should be reviewed with account owners.

---

### Step 2: Validate Account Authorization

- Cross-check users with authorized user list / HR roster.
- Check temporary / service accounts → validate ownership.
- Check accounts with elevated privileges → verify authorization.

> **Operator Note:** Accounts created outside of normal onboarding process are HIGH RISK.

---

### Step 3: Disable or Remove Unauthorized Accounts

#### Local Users (Windows)

```powershell
Disable-LocalUser -Name "username"
Remove-LocalUser -Name "username"
```

#### Local Users (Linux/macOS)

```bash
sudo usermod -L username
sudo userdel username
```

#### Active Directory

```powershell
Disable-ADAccount -Identity "username"
Remove-ADUser -Identity "username"
```

#### Azure AD

```powershell
Set-AzureADUser -ObjectId <UserId> -AccountEnabled $false
Remove-AzureADUser -ObjectId <UserId>
```

#### AWS IAM

```bash
aws iam delete-user --user-name <username>
```

#### Google Workspace

- Admin Console → Directory → Users → Select User → Suspend or Delete

> **Operator Note:** Suspend (disable) first unless immediate removal is required → preserves data for audit.

---

### Step 4: Validate Removal and Audit

- Re-run account queries → confirm removal.
- Check audit logs → validate action was recorded.
- Document justification, method, and responsible approver.

---

## Dependencies

* Access to local systems, AD, cloud consoles
* Approved list of authorized users
* Coordination with HR and IT security

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell AD Modules | Windows | Built-in / RSAT | AD queries and management |
| AWS CLI | Cross-platform | Package manager | IAM management |
| AzureAD Module | PowerShell | Install-Module | Azure AD management |
| Google Admin Console | Web | N/A | Google Workspace user management |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain authorized user lists for local, AD, and cloud environments.
- [ ] Collect all active user accounts across local, AD, Azure AD, AWS IAM, Google Workspace.
- [ ] Compare against authorized list → identify unauthorized or suspicious users.
- [ ] Validate elevated privilege accounts → confirm need and authorization.
- [ ] Disable or remove unauthorized accounts.
- [ ] Validate removal and log actions.
- [ ] Document all actions taken and coordinate with system owners.

### Best Practices

- Disable accounts first before deletion to prevent unintended impact.
- Review service accounts carefully → validate ownership and justification.
- Implement alerting for new accounts created outside of normal processes.
- Establish periodic review cadence for cloud and AD environments.

---

## References

- [Microsoft Active Directory User Management](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/user-management)
- [Azure AD User Management](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/add-users-azure-active-directory)
- [AWS IAM User Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_manage.html)
- [Google Workspace User Management](https://support.google.com/a/answer/33314?hl=en)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator guidance and multi-platform examples | Leo |
