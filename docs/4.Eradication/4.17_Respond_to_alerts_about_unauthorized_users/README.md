# 4.17 Respond to Alerts About Unauthorized Users

## Task

Respond to alerts about unauthorized users or unsuccessful logins. Investigate, triage, and escalate as necessary using established incident response processes.

---

## Conditions

* Proper controls and security practices applied to Active Directory or other directory platform
* Effective use of log management and SIEM (Security Information and Event Management)
* Working in concert with Team Leader’s priorities and interactions with mission partner

---

## Standards

* Ensure Active Directory logs are integrated into log management tool and test SIEM alerting
* Responding party should have knowledge of environment and understand the incident response process
* Incident response process should detail:
  * Escalation criteria
  * Remediation plan
  * Ability to investigate or collect evidence
* Abnormal activity should be logged and remediated until resolved
* Accounts should be suspended, terminated, or monitored with advanced logging if necessary

---

## End State

Unauthorized user alerts are identified, investigated, and resolved utilizing the proper incident response procedure and documentation.

---

## Notes

- Unauthorized user activities can indicate compromised credentials, rogue accounts, or misconfiguration.
- Immediate investigation, correlation, and escalation is required.

---

## Manual Steps

### Security Onion / SIEM

* Login to SIEM interface (e.g. Kibana)
* Select **Dashboard** → Choose **Sysmon → Host Hunting**
* Validate Active Directory logs are populating properly
* Look for relevant events:
  * Event ID 4624 (Logon Success)
  * Event ID 4625 (Logon Failed)
  * Event ID 4720 (User Account Created)
  * Event ID 4726 (User Account Deleted)
  * Event ID 4728/4732 (User Added to Group)

#### Advanced Filtering (Kibana Example):

```
event.code:4625 AND user.name:* AND NOT user.name:knownadmin
```

- Detect login failures not from known admins

---

### Active Directory / PowerShell

#### Search for recently created accounts:

```powershell
Get-ADUser -Filter * -Properties whenCreated | Where-Object {$_.whenCreated -gt (Get-Date).AddDays(-1)}
```

#### Search for last login time for new/suspicious users:

```powershell
Get-ADUser -Filter * -Properties LastLogonTimestamp | Select-Object Name,LastLogonTimestamp
```

#### Review Group Membership (Privileged Group Check)

```powershell
Get-ADGroupMember -Identity "Domain Admins"
```

---

### Linux / macOS Validation

#### Check for new local accounts:

```bash
cat /etc/passwd | grep -v "/nologin" | grep -v "false"
```

#### Check failed logins:

```bash
cat /var/log/auth.log | grep "Failed password"
```

or

```bash
lastb
```

---

### Cloud IAM Monitoring

#### AWS → CloudTrail + GuardDuty

```bash
aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser
```

- Look for unauthorized IAM users

GuardDuty Findings:

- UnauthorizedAccess:IAMUser/AnomalousBehavior
- Recon:IAMUser/PasswordBruteForce

---

#### Azure → Azure AD Audit Logs

```powershell
Search-UnifiedAuditLog -StartDate (Get-Date).AddDays(-1) -Operations "Add user","Update user","Add member to group"
```

or via Portal → Azure AD → Audit Logs → Filter for user creation and role changes

---

#### GCP → Cloud Audit Logs

```bash
gcloud logging read "protoPayload.methodName=google.admin.directory.user.insert"
```

- Detect unauthorized or unexpected user account creation

---

## Dependencies

* Sysmon
* Wazuh
* Wazuh configuration file (ossec.conf)
* Security Onion
* Elasticsearch
* Active Directory PowerShell module
* Cloud CLI tools (AWS CLI, Azure CLI, gcloud)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Security Onion / Wazuh | Network | Native | SIEM/Log aggregation |
| PowerShell | Windows | Native | AD investigation |
| OSSEC / Wazuh | Cross-platform | Native | Local log detection |
| AWS CloudTrail + GuardDuty | AWS | Native | IAM unauthorized detection |
| Azure AD Audit Logs + Sentinel | Azure | Native | User and login anomalies |
| GCP Cloud Audit Logs | GCP | Native | User creation and login anomalies |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Review and validate SIEM alert for unauthorized user event
- [ ] Correlate → Determine AD, Local, Cloud origin
- [ ] Investigate → Determine WHO created user, WHERE they logged in, and WHAT they accessed
- [ ] Validate account owner and intended usage
- [ ] If unauthorized → Disable / remove user account immediately
- [ ] Review group membership and permissions of unauthorized account
- [ ] Search for follow-on activity (logon, RDP, file access)
- [ ] Document all findings, preserve evidence
- [ ] Escalate per IR plan if threat actor confirmed or lateral movement suspected

### Best Practices

- Implement alerting for user creation + privilege escalation (automated detection)
- Perform daily review of newly created accounts
- Establish quarantine OU for suspicious accounts
- Integrate cloud and on-prem user creation into centralized SIEM
- Disable interactive logon rights for service accounts (limit exposure)

---

## References

* [Microsoft Unauthorized User](https://support.microsoft.com/en-us/kb/871179)
* [Microsoft Login Failed](https://technet.microsoft.com/en-us/library/cc671957(v=ws.10).aspx)
* [AWS GuardDuty IAM Findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html)
* [Azure AD Audit Logs](https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-audit-logs)
* [GCP Cloud Audit Logs](https://cloud.google.com/logging/docs/audit)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded for IR workflow, multi-platform unauthorized user detection | Leo |
