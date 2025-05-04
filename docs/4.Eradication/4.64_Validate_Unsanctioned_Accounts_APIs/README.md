# 4.64 Validate No Unauthorized or Unsanctioned Accounts or API Keys Remain (Local, Cloud - IAM, Service Accounts, API Tokens)

## Task

Identify and remove unauthorized, stale, or over-permissive user accounts, service accounts, and API credentials across local systems and cloud environments (AWS, Azure, GCP).

---

## Conditions

Given access to local system account lists, AD accounts, cloud IAM and API credential stores.

---

## Standards

* Enumerate and validate all accounts and API keys.
* Identify unauthorized, inactive, or stale accounts.
* Remove or disable unauthorized or high-risk accounts and credentials.
* Document all actions.

---

## End State

All unauthorized accounts and API credentials are identified, disabled/removed, and fully documented.

---

## Notes

- API keys and service accounts often get overlooked → attackers use them for persistence and data exfiltration.
- Orphaned and stale accounts create privilege escalation opportunities.
- OAuth tokens and personal access tokens (PATs) can bypass traditional controls.

---

## Manual Steps

### Step 1: Enumerate Local System and Active Directory Accounts

#### Windows Local

```powershell
Get-LocalUser
```

Look for:

- Disabled = False
- LastLogonDate is old or never logged in
- Unknown users

#### Linux/macOS Local

```bash
cat /etc/passwd
```

Look for:

- UID >= 1000
- Home directory anomalies (empty, /dev/null)
- Shell = /bin/bash or other active shells (exclude /sbin/nologin)

#### Active Directory

```powershell
Get-ADUser -Filter * -Properties LastLogonDate, Enabled | Select-Object Name, Enabled, LastLogonDate
```

Look for:

- Users with no logon in 90+ days
- Disabled users that still exist
- Service accounts with interactive login enabled

> **Operator Note:** Use AD group memberships to help identify high privilege accounts → validate necessity.

---

### Step 2: Enumerate API Keys and Service Accounts - AWS

#### List IAM Users and Access Keys

```bash
aws iam list-users
aws iam list-access-keys --user-name USERNAME
```

Look for:

- Access keys with last used = N/A or old
- Users not tied to workload or people (ghost users)

#### Identify Long-Lived API Keys

```bash
aws iam get-access-key-last-used --access-key-id KEY_ID
```

Look for:

- Keys not used in last 90+ days
- Non-rotated keys (older than policy allows)

#### Identify IAM Roles and Attached Policies

```bash
aws iam list-roles
aws iam list-role-policies --role-name ROLENAME
```

Look for:

- Roles with "*" or wildcard permissions
- Roles not used recently or unknown use case

---

### Step 3: Enumerate API Keys and Service Accounts - Azure

#### Azure AD Users and Applications

```powershell
Get-AzADUser
Get-AzADServicePrincipal
```

Look for:

- Accounts with "Owner" or "Global Admin"
- Service Principals not used or unknown origin

#### Application Secrets and Certificates (API Tokens)

```powershell
Get-AzADAppCredential -ObjectId OBJECT_ID
```

Look for:

- Expired, non-rotating credentials
- Apps with risky delegated permissions

#### Azure Automation RunAs Accounts

```powershell
Get-AzAutomationAccount
```

Look for:

- Automation accounts with unnecessary privileged access
- Stale Automation credentials

---

### Step 4: Enumerate API Keys and Service Accounts - GCP

#### IAM Service Accounts and Roles

```bash
gcloud iam service-accounts list
gcloud projects get-iam-policy PROJECT_ID
```

Look for:

- Service accounts with "Owner" role
- External members granted access

#### API Keys

```bash
gcloud alpha services api-keys list
```

Look for:

- API keys without restrictions
- API keys not tied to workload

#### OAuth Client Secrets

```bash
gcloud iam service-accounts keys list --iam-account SERVICE_ACCOUNT_EMAIL
```

Look for:

- Service account keys older than rotation policy
- Unknown owners

> **Operator Note:** API keys and service accounts are frequently exploited for lateral movement in cloud.

---

### Step 5: Validate and Investigate Accounts / Keys

- Validate account and key ownership → contact owners
- Cross-reference with asset and workload inventories
- Review usage logs → CloudTrail (AWS), Activity Logs (Azure), Admin Activity (GCP)

> **Operator Note:** Absence of recent use + unknown owner = High Risk → escalate for removal.

---

### Step 6: Remove or Disable Unauthorized or Unused Accounts / Keys

#### Local

##### Windows

```powershell
Disable-LocalUser -Name "username"
Remove-LocalUser -Name "username"
```

##### Linux/macOS

```bash
sudo usermod -L username
sudo userdel username
```

#### Active Directory

```powershell
Disable-ADAccount -Identity "username"
Remove-ADUser -Identity "username"
```

---

#### AWS

```bash
aws iam delete-user --user-name USERNAME
aws iam delete-access-key --user-name USERNAME --access-key-id KEYID
aws iam delete-role --role-name ROLENAME
```

---

#### Azure

```powershell
Remove-AzADUser -ObjectId USERID
Remove-AzADServicePrincipal -ObjectId OBJECTID
```

---

#### GCP

```bash
gcloud iam service-accounts delete SERVICE_ACCOUNT_EMAIL
gcloud alpha services api-keys delete KEY_ID
```

---

### Step 7: Validate Removal and Document

- Validate in consoles and CLI → confirm removal.
- Review audit logs for confirmation.
- Document removed accounts, owner, justification, and approver.

---

## Dependencies

* Access to local OS user administration
* Active Directory management privileges
* Cloud IAM admin roles (AWS, Azure, GCP)
* Approved user and API credential inventory

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell | Windows | Built-in | AD + local account validation |
| AWS CLI | Cross-platform | Package manager | IAM + access keys |
| Azure CLI / PowerShell | Cross-platform | Package manager | Azure AD + app credentials |
| GCloud CLI | Cross-platform | Package manager | IAM + API keys |
| CloudTrail / Sentinel / Security Command Center | Cloud | Native | API and credential usage audit |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate local, AD, and cloud accounts + credentials.
- [ ] Validate owner, usage, and need.
- [ ] Identify and flag unauthorized or inactive accounts + credentials.
- [ ] Disable and remove unauthorized credentials.
- [ ] Validate removal and document actions.

### Best Practices

- Enforce strong rotation policies → API keys, secrets.
- Disable before deleting where possible.
- Require ownership metadata on all IAM objects.
- Implement automated stale credential detection (SIEM, CSPM).

---

## References

- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [Azure Identity Security](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/security-operations)
- [GCP IAM Overview](https://cloud.google.com/iam/docs/overview)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator guidance across local, AD, cloud IAM + API credentials | Leo |
