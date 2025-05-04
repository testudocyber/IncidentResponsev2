# 4.59 Remove Unauthorized Scheduled Tasks and Automation (Local and Cloud)

## Task

Identify and remove unauthorized scheduled tasks, automation jobs, and persistent background execution mechanisms across local systems and cloud platforms (AWS, Azure, GCP).

---

## Conditions

Given access to local systems, cloud automation consoles, and administrative tools to enumerate, disable, and remove scheduled tasks.

---

## Standards

* Enumerate and validate all scheduled tasks and automation jobs.
* Cross-check tasks against authorized task and automation inventories.
* Disable and remove unauthorized or suspicious tasks.
* Document and report all removals.

---

## End State

All unauthorized and suspicious scheduled tasks and automation are identified, removed or disabled, and documented across local and cloud environments.

---

## Notes

- Unauthorized scheduled tasks are a known persistence mechanism for attackers.
- Cloud automation (Lambda, Azure Automation, Cloud Functions) can be abused for C2 and post-exploitation.
- Detection and validation should include source, creator, and execution context.

---

## Manual Steps

### Step 1: Enumerate Scheduled Tasks and Automation - Local Systems

#### Windows → Task Scheduler

```powershell
Get-ScheduledTask | Select-Object TaskName, State, Actions
```

Look for:

- Non-standard or unfamiliar task names.
- Tasks running from unusual directories (ex: Temp, AppData).
- Tasks triggered outside normal hours.

> **Operator Note:** Check for hidden tasks and those created by "SYSTEM" or unknown users.

---

#### Linux → Cron and Anacron

```bash
crontab -l
cat /etc/crontab
ls /etc/cron.d/
```

Look for:

- Jobs running scripts from /tmp or /var/tmp
- Jobs running as root not approved
- Jobs running frequently at odd hours

> **Operator Note:** Review `/var/log/syslog` or `/var/log/cron` for execution history.

---

#### macOS → Launch Daemons and Launch Agents

```bash
launchctl list
cat /Library/LaunchDaemons/*
cat /Library/LaunchAgents/*
```

Look for:

- Unknown plist files
- Launch agents in user folders
- Daemons launching shell scripts

---

### Step 2: Enumerate Cloud Scheduled Tasks and Automation

#### AWS → EventBridge (CloudWatch Events), Lambda, Step Functions

```bash
aws events list-rules
aws lambda list-functions
aws stepfunctions list-state-machines
```

Look for:

- Functions or rules with unknown creators
- EventBridge rules running on suspicious schedules
- Lambda functions tied to suspicious triggers (ex: S3, SNS)

> **Operator Note:** Functions without tags or ownership are high priority for review.

---

#### Azure → Automation Accounts, Runbooks, Logic Apps, Functions

```powershell
Get-AzAutomationAccount
Get-AzAutomationRunbook
Get-AzLogicApp
Get-AzFunctionApp
```

Look for:

- Unscheduled or disabled Runbooks re-enabled
- Logic Apps performing unauthorized data transfers
- Functions with unknown owners or lack of documentation

> **Operator Note:** Review Activity Log → filter by resource type and create/update actions.

---

#### GCP → Cloud Scheduler, Cloud Functions

```bash
gcloud scheduler jobs list
gcloud functions list
```

Look for:

- Jobs triggered without business justification
- Functions running without source control
- Triggers like HTTP, Pub/Sub without secure IAM bindings

> **Operator Note:** Cloud Functions used by attackers often have generic names → audit naming and descriptions.

---

### Step 3: Validate Task and Automation Authorization

- Compare against approved inventories and runbooks.
- Validate ownership and purpose with system or service owners.
- Check implementation history (who added and why).

> **Operator Note:** Shadow automation (automation implemented without IT/security approval) should be reviewed carefully.

---

### Step 4: Disable or Remove Unauthorized Tasks

#### Windows

```powershell
Unregister-ScheduledTask -TaskName "TaskName" -Confirm:$false
```

#### Linux

```bash
crontab -r
rm /etc/cron.d/unapprovedjob
```

#### macOS

```bash
sudo launchctl bootout system /Library/LaunchDaemons/com.unauthorized.plist
sudo rm /Library/LaunchDaemons/com.unauthorized.plist
```

#### AWS

```bash
aws events delete-rule --name "RuleName"
aws lambda delete-function --function-name "FunctionName"
```

#### Azure

```powershell
Remove-AzAutomationRunbook -AutomationAccountName "AccountName" -Name "RunbookName"
Remove-AzFunctionApp -Name "FunctionName"
```

#### GCP

```bash
gcloud scheduler jobs delete "job-name"
gcloud functions delete "function-name"
```

> **Operator Note:** Always disable first unless critical → preserves for forensic review.

---

### Step 5: Validate and Document Removals

- Confirm removed tasks no longer exist.
- Validate audit logs reflect removal actions.
- Document findings, owners, and decisions.

---

## Dependencies

* Access to local OS, Cloud Platform APIs and Consoles
* Approved automation and task inventory
* Coordination with application and system owners

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell / Task Scheduler | Windows | Built-in | Task enumeration/removal |
| systemctl / crontab | Linux/macOS | Native | Cron/launchd management |
| AWS CLI | Cross-platform | Package manager | Lambda/EventBridge |
| Azure CLI / PowerShell | Cross-platform | Native | Azure Automation |
| GCloud CLI | Cross-platform | Package manager | Scheduler/Functions |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate all scheduled tasks and automation jobs across local and cloud.
- [ ] Validate each task against approved inventory.
- [ ] Identify and flag unauthorized tasks.
- [ ] Disable and remove unauthorized tasks.
- [ ] Validate and document removal.

### Best Practices

- Quarantine questionable tasks by disabling before deleting.
- Review cloud automation periodically → attackers often leave cloud persistence.
- Implement alerts for creation of new automation tasks.
- Ensure all business automation is documented and reviewed.

---

## References

- [AWS Lambda and EventBridge Management](https://docs.aws.amazon.com/lambda/latest/dg/lambda-scheduled-events.html)
- [Azure Automation and Functions](https://learn.microsoft.com/en-us/azure/automation/automation-intro)
- [GCP Cloud Scheduler and Functions](https://cloud.google.com/scheduler/docs)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator guidance across local, cloud, and SaaS platforms | Leo |
