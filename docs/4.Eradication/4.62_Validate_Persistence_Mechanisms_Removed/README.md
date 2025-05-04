# 4.62 Validate No Persistence Mechanisms Remain (Local, AD, Cloud - Backdoors, Accounts, Automation)

## Task

Detect and remove unauthorized persistence mechanisms including rogue accounts, backdoors, startup tasks, cloud functions, IAM backdoors, and unauthorized automation from local systems, Active Directory (AD), and cloud environments (AWS, Azure, GCP).

---

## Conditions

Given access to local, enterprise, and cloud platform administrative tools and logs.

---

## Standards

* Detect and validate persistence mechanisms across all environments.
* Remove or disable unauthorized persistence mechanisms.
* Confirm removal through validation and auditing.
* Document all actions.

---

## End State

No unauthorized accounts, services, jobs, or automated tasks remain that provide unauthorized or hidden access paths into the environment.

---

## Notes

- Persistence can be implanted across many layers: accounts, services, scheduled tasks, cloud automation, IAM policies.
- Threat actors often use obscure or hidden mechanisms.
- Thorough validation requires enumeration and cross-checking all avenues.

---

## Manual Steps

### Step 1: Enumerate and Review Local Persistence Mechanisms

#### User Accounts (Local)

##### Windows

```powershell
Get-LocalUser
```

##### Linux/macOS

```bash
cat /etc/passwd
```

Look for:

- Unauthorized user accounts
- Privileged accounts (Administrators/root group)

> **Operator Note:** Flag users with unusual usernames or not in authorized list.

---

#### Startup Items / Scheduled Tasks

##### Windows

```powershell
Get-ScheduledTask
```

Check:

- Non-standard startup tasks
- Scripts located in Temp/AppData
- Unknown publisher or user-created

##### Linux (cron)

```bash
crontab -l
cat /etc/cron.*
ls /etc/systemd/system/
```

Check:

- Odd scripts or binaries executing as root
- Newly added @reboot jobs

##### macOS

```bash
launchctl list
```

Check:

- Unknown launch agents/daemons
- User folder agents

> **Operator Note:** Unknown or recently modified startup tasks → high priority for investigation.

---

#### Registry / WMI / Services (Windows Advanced Persistence)

```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter
```

Check:

- WMI persistence → triggers hidden from normal user view

```powershell
Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"
```

> **Operator Note:** Registry autoruns are easy to hide → validate all entries.

---

### Step 2: Enumerate and Review Active Directory Persistence

#### AD Accounts

```powershell
Get-ADUser -Filter * -Properties LastLogonDate, Enabled
```

Look for:

- Stale, disabled, or hidden users
- Unusual account naming conventions

#### AD Delegation / GPO Backdoors

```powershell
Get-GPResultantSetOfPolicy
Get-ADObject -LDAPFilter "(adminCount=1)"
```

Look for:

- Unexpected GPO delegations
- adminCount=1 → privileged accounts

#### AD Computers

```powershell
Get-ADComputer -Filter * -Properties LastLogonDate
```

> **Operator Note:** Unused or "phantom" computer accounts could be attacker-placed.

---

### Step 3: Enumerate and Review Cloud Persistence Mechanisms

#### AWS

```bash
aws iam list-users
aws iam list-roles
aws iam list-policies
```

Look for:

- Inline policies attached directly to users → high risk
- Users/roles created by unknown identities
- Trust policies allowing external AWS accounts (cross-account access)

```bash
aws lambda list-functions
```

Look for:

- Functions without business justification
- Auto-start triggers → SNS, S3

---

#### Azure

```powershell
Get-AzADUser
Get-AzRoleAssignment
Get-AzFunctionApp
```

Look for:

- Unused or high-privileged Azure AD users
- Custom roles and privileged assignments
- Azure Functions → malicious or unknown automated execution

Azure Portal → Logic Apps / Automation Accounts:

- Unknown or newly created logic apps / runbooks

---

#### Google Cloud (GCP)

```bash
gcloud iam roles list
gcloud projects get-iam-policy [PROJECT_ID]
gcloud functions list
```

Look for:

- Custom roles with excessive privileges
- External members granted IAM roles
- Functions with HTTP triggers or frequent invocation

> **Operator Note:** External identities → attacker backdoors.

---

### Step 4: Remove Unauthorized Persistence Mechanisms

#### Local (Windows, Linux, macOS)

- Disable and delete unauthorized users.
- Delete unknown scheduled tasks / launch agents / crons.
- Remove autoruns and services.

#### Active Directory

- Disable and remove unauthorized users.
- Remove GPO links or delegations.
- Clean up computer objects.

#### AWS

```bash
aws iam delete-user --user-name USER
aws iam delete-role --role-name ROLE
aws lambda delete-function --function-name FUNCTION
```

#### Azure

```powershell
Remove-AzADUser -ObjectId USERID
Remove-AzFunctionApp -Name "FunctionName"
```

#### GCP

```bash
gcloud iam roles delete [ROLE_ID] --project=[PROJECT_ID]
gcloud functions delete [FUNCTION_NAME]
```

> **Operator Note:** Disable before deletion where possible to avoid accidental service interruption.

---

### Step 5: Validate and Document

- Confirm no hidden users, roles, policies, or functions remain.
- Validate startup tasks and automation → no unauthorized entries.
- Update onboarding/offboarding and security controls to prevent re-introduction.

---

## Dependencies

* Access to OS-level, AD, and cloud management consoles
* Audit logs and administrative permissions
* Validated inventory of approved users, roles, and automation tasks

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell / Bash | Cross-platform | Native | Local + AD validation |
| AWS CLI / Azure CLI / gcloud | Cross-platform | Package manager | Cloud IAM and automation validation |
| Sysinternals Autoruns | Windows | Download | In-depth autorun review |
| Microsoft Sentinel / Splunk | SIEM | Native | Cross-correlation and detection of abnormal persistence |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate local persistence mechanisms (users, tasks, autoruns).
- [ ] Enumerate AD persistence mechanisms (users, GPOs, delegations).
- [ ] Enumerate cloud IAM, automation, and external trust relationships.
- [ ] Validate and disable/remove unauthorized mechanisms.
- [ ] Document actions and notify affected owners.

### Best Practices

- Disable before deleting unless urgent → preserves evidence.
- Always investigate suspicious automation or IAM roles.
- Review persistence avenues regularly → include in change management.
- Implement detection for persistence reintroduction (SIEM alerting).

---

## References

- [MITRE ATT&CK Persistence Techniques](https://attack.mitre.org/tactics/TA0003/)
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [Azure AD Security Best Practices](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/security-operations)
- [GCP IAM Overview](https://cloud.google.com/iam/docs/overview)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused persistence discovery and removal guidance | Leo |
