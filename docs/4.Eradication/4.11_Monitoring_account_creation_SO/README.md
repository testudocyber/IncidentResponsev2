# 4.11 Monitoring Account Creation (SO + Local + Cloud)

## Task

Monitor for the creation of new user or service accounts across enterprise environments including Security Onion (SO), local systems, and cloud platforms to identify potentially unauthorized access and persistence mechanisms.

---

## Conditions

Given:

* A Security Onion server and admin account to query account creations
* Access to incident response workstation
* Access to local and cloud platforms for account creation monitoring

---

## Standards

* Team member queries the Security Onion Server to obtain the following minimum information:
  * Account Name
  * Account Domain
  * Machine the account was created on (if the account was created locally)
  * SID of new account
* Team member outputs the account creation data to a comma-separated values (CSV) file and compares the entries to existing user accounts.
* Team member checks information on accounts used to create new accounts against known IOCs or system owner activities to determine if accounts are legitimate.

---

## End State

All new user or service account creation events are discovered, logged, validated, and confirmed to be legitimate or escalated as suspicious.

---

## Notes

Security Onion is able to detect local and domain account creations.

---

## Manual Steps

### Security Onion (Primary Method)

#### Use the following Security Onion search query:

```
winlog.event_id:"4720" | groupby event.module event.dataset
```

This query will identify new user account creation events on Windows systems.

#### Export to CSV:

1. Select query results.
2. Use export feature to save as CSV.
3. Compare against known user list.

#### Validate

- Cross-reference new accounts with authorized account request forms or owner approval.
- Investigate accounts without approval or context.

---

### Windows Event Viewer (Alternative / Local)

#### Using Windows Event Viewer:

Event ID to search:

```
Event ID: 4720 (A user account was created)
```

Steps:

1. Open Event Viewer → Windows Logs → Security
2. Use filter or search for `4720`
3. Validate account creation events manually

> **Operator Note:** Use PowerShell for automation where needed.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4720}
```

---

### Linux and macOS (Local Account Monitoring)

#### Audit new users from `/etc/passwd` (Linux/macOS)

```bash
awk -F':' '{ print $1}' /etc/passwd
```

#### Monitor user addition with auditd (Linux)

```bash
ausearch -m ADD_USER
```

#### macOS Unified Log (New Accounts)

```bash
log show --predicate 'eventMessage CONTAINS "Added user account"' --info
```

> **Operator Note:** Configure OSSEC or Auditbeat where applicable for automated account monitoring.

---

### Cloud Monitoring

#### AWS (CloudTrail)

```bash
aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser
```

Look for:

- IAM user creation → Cross-reference who performed action
- Automated scripts vs human interaction

##### AWS GuardDuty

- Will also alert on suspicious account creations (optional cross-check)

---

#### Azure (Azure AD Audit Logs)

```powershell
Search-UnifiedAuditLog -StartDate (Get-Date).AddDays(-1) -Operations "Add user"
```

or Azure Portal → Azure AD → Audit Logs

Look for:

- New user additions
- Role assignments

---

#### GCP (Cloud Audit Logs)

```bash
gcloud logging read "protoPayload.methodName=\"google.iam.admin.v1.CreateServiceAccount\""
```

or

```bash
gcloud logging read "protoPayload.methodName=\"google.admin.directory.user.insert\""
```

Look for:

- Service account creations
- User account insertions

> **Operator Note:** API-based or externally sourced account additions should be reviewed closely.

---

### Validate and Investigate

For all platforms:

- Cross-check against authorized change requests or HR/user management systems.
- Investigate automated creations without owner.
- Validate service accounts → what apps/infra are using them?

---

## Running Script

TBD → Automated script examples may be generated for PowerShell, Bash, and Cloud CLIs to consolidate across platforms.

---

## Dependencies

* Security Onion access and Winlogbeat or Sysmon integration.
* Access to local OS logs (Windows Event Viewer, Linux auditd, macOS Unified Logs).
* Cloud provider admin/reader role to audit IAM user creations.
* CSV export capability for analysis.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Security Onion | Network | Native | Windows Account Creation Events |
| OSSEC / Wazuh | Cross-platform | Package Manager / VM | Local User Account Monitoring |
| Auditbeat | Linux/macOS | Package Manager | Real-time user event monitoring |
| AWS CloudTrail + GuardDuty | AWS | Native | IAM user creation detection |
| Azure AD Audit Logs + Sentinel | Azure | Native | Account creation monitoring |
| GCP Cloud Logging + SCC | GCP | Native | Account creation and IAM service account monitoring |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Run Security Onion query and export CSV
- [ ] Compare new accounts to known user lists
- [ ] Investigate accounts created outside normal processes
- [ ] Validate local Windows Event Log (4720) entries
- [ ] Validate Linux/macOS for user additions
- [ ] Validate Cloud platforms (AWS, Azure, GCP) for IAM/User/Service Account creations
- [ ] Investigate unknown or automated-created accounts
- [ ] Confirm approved ownership for all new accounts
- [ ] Document all findings and close legitimate cases

### Best Practices

- Run queries daily or with automated alerting to detect account creation quickly.
- Ensure cloud-native alerting is integrated to SIEM/SOAR.
- Service accounts should have owner tags/annotations in cloud IAM.
- Perform weekly audit and reconciliation for shadow accounts.

---

## References

* [EventID 4720](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4720)
* [AWS IAM CloudTrail Events](https://docs.aws.amazon.com/IAM/latest/UserGuide/cloudtrail-integration.html)
* [Azure AD Audit Logs](https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-audit-logs)
* [GCP Cloud Audit Logs](https://cloud.google.com/logging/docs/audit)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.1 | Enhanced with operator checklist, recommendations, and multi-platform account monitoring workflow | Leo |
