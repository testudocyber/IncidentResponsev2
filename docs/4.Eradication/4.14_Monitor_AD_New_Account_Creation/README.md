# 4.14 Monitor AD New Account Creation (Local + Cloud)

## Task

Monitor Active Directory (AD) and cloud environments for new user or service account creation events, validate for legitimacy, and document findings.

---

## Conditions

Given:

* Access to Domain Controllers (DCs)
* A domain account with permissions to query AD and read event logs
* Incident response workstation with PowerShell
* Access to cloud native logging platforms

---

## Standards

* Team member queries the DC to obtain:
  * Name of user who created new account
  * Security Identifier (SID) of the creator
  * Logon ID (semi-unique tracker of session)
  * Name and SID of new account
  * Domain of new account
* Output to CSV and compare against authorized user list
* Investigate account creator and validate authorization
* Optional: Execute PR.AC-1.1 to obtain all user attributes for advanced comparison

---

## End State

All new account creations (local + cloud) are discovered, validated, and documented.

---

## Notes

* Primary Windows Event IDs:
  * `4720` → User account created (Windows 7+/Windows Server 2008+)
  * `624` → User account created (Windows 2003 and older)
* Other useful Event IDs:
  * `4722` → Account enabled
  * `4725` → Account disabled
  * `4726` → Account deleted

---

## Manual Steps

### PowerShell → Active Directory

List users created in last 5 days:

```powershell
$When = ((Get-Date).AddDays(-5)).Date
Get-ADUser -Filter {whenCreated -ge $When} -Properties whenCreated
```

Alternative AD query:

```powershell
import-module activedirectory
Get-QADUser -CreatedAfter (Get-Date).AddDays(-5)
Get-ADUser -Filter * -Properties whenCreated | Where-Object {$_.whenCreated -ge ((Get-Date).AddDays(-5)).Date}
```

### PowerShell → Security Event Logs (Direct account creation events)

```powershell
Get-EventLog Security -InstanceId 4720 -after ((get-date).AddDays(-1))
```

> **Operator Note:** 4720 is the most important → direct user creation.

---

### Additional SID Queries and Investigations

#### Get SID of AD Group

```powershell
Get-ADGroup -Identity "some_group_name"
```

#### Get Group from SID

```powershell
Get-ADGroup -Identity S-1-5-32-544
```

#### Local User SID

```cmd
wmic useraccount where name='some_username' get sid
```

#### Current User SID

```cmd
whoami /user
```

#### Local Admin SID

```cmd
wmic useraccount where (name='administrator' and domain='%computername%') get name,sid
```

#### Find username from SID

```cmd
wmic useraccount where sid='S-1-3-12-XYZAA-1111' get name
```

---

### Cloud Platform User Creation Monitoring

#### AWS → CloudTrail

```bash
aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=CreateUser
```

- Review IAM user creation events

#### Azure → Azure AD Audit Logs

```powershell
Search-UnifiedAuditLog -StartDate (Get-Date).AddDays(-1) -Operations "Add user"
```

or via Portal → Azure AD → Audit Logs

#### GCP → Cloud Audit Logs

```bash
gcloud logging read "protoPayload.methodName=\"google.admin.directory.user.insert\""
```

> **Operator Note:** Validate if new user/service account was provisioned automatically or manually.

---

## Running Script

`.\\AD_AccountCreationDetection.ps1`

> **Operator Note:** This should be run at intervals (command-directed or daily).

---

## Dependencies

* AD Module for PowerShell
* Event Log Access
* Cloud Admin roles (reader for audit logs)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Security Onion (via Winlogbeat) | Network | Native | SMB + AD event ingest |
| BloodHound | AD | Native | Visualize and audit AD permissions and accounts |
| OSSEC / Wazuh | Cross-platform | Native | Detect user additions |
| AWS CloudTrail | AWS | Native | IAM user creation |
| Azure AD Audit Logs | Azure | Native | User creation tracking |
| GCP Cloud Audit Logs | GCP | Native | Service/User account creation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Run PowerShell commands for local AD → capture new user creations
- [ ] Collect SID and Logon ID of creator and new account
- [ ] Validate if account creation was authorized → request/approval
- [ ] Review Event IDs for suspicious enable/delete/disabling post-creation
- [ ] Review cloud (AWS, Azure, GCP) audit logs for new user/service account additions
- [ ] Investigate accounts not aligned with change control
- [ ] Document findings and close cases or escalate

### Best Practices

- Automate queries into SIEM/SOAR for real-time alerts
- Validate service account creations → often targets for persistence
- Include cloud in daily/weekly reviews
- Tag or annotate all authorized service accounts

---

## References

* [PowerShell Get Recently Created Users](https://github.com/WiredPulse/PowerShell/blob/master/Active_Directory/Get-ADUser_RecentlyCreatedUsers.ps1)
* [AWS CloudTrail CreateUser](https://docs.aws.amazon.com/IAM/latest/UserGuide/cloudtrail-integration.html)
* [Azure AD Audit Logs](https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-audit-logs)
* [GCP Cloud Audit Logs](https://cloud.google.com/logging/docs/audit)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained and expanded for cloud + operator workflow | Leo |
