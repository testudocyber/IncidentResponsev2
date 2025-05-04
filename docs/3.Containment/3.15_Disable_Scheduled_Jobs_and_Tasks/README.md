# Disable Scheduled Tasks and Jobs

## Task Disable Scheduled Tasks and Jobs on Compromised or At-Risk Hosts

## Conditions

Given access to Windows or Linux hosts with local administrator or root privileges, the operator will identify, disable, and document scheduled tasks or jobs that may be abused by attackers for persistence or lateral movement.

> **Operator Note:** Threat actors frequently leverage scheduled tasks or cron jobs to maintain persistence or execute malicious payloads after reboots or on a timer. During incident response, disabling (not deleting) them preserves forensic evidence while disrupting adversary operations.

## Standards

* Team member identifies all scheduled tasks and jobs on impacted systems.  
* Team member documents and reviews all tasks for legitimacy and IR team coordination.  
* Team member disables suspicious or all non-critical tasks.  
* Team member confirms that disabled tasks no longer execute.  
* Team member preserves evidence (do not delete unless approved).

## End State

All malicious or unnecessary scheduled tasks are disabled on impacted systems. A record of actions is maintained for forensic review.

---

## Notes

- Disabling is preferred over deleting during active incidents to preserve forensic data.
- Changes should be documented carefully and validated through test execution.
- Tasks that may impact critical business operations should be reviewed before disabling.

---

## Manual Steps

### Windows Systems

#### View All Scheduled Tasks

##### PowerShell (Preferred)

```powershell
Get-ScheduledTask | Select-Object TaskName, State
```

##### schtasks (Command Line)

```cmd
schtasks /query /fo LIST /v
```

#### Disable Specific Scheduled Task

##### PowerShell

```powershell
Disable-ScheduledTask -TaskName "TaskName" -TaskPath "\"
```

##### schtasks

```cmd
schtasks /Change /TN "\TaskName" /Disable
```

> **Operator Note:** Disable suspicious or non-essential tasks only after reviewing.

#### Export Scheduled Tasks (For backup / forensic review)

```powershell
Get-ScheduledTask | Export-Clixml -Path .\ScheduledTasksBackup.xml
```

---

### Linux Systems

#### View Cron Jobs (user level)

```bash
crontab -l
```

#### Remove or comment out suspicious entries

```bash
crontab -e
```

> Comment suspicious tasks with `#` to disable but preserve evidence.

---

#### System-wide Cron Jobs (root or services)

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

> **Operator Note:** Review and comment out any suspicious jobs.

---

#### Disable systemd timers

```bash
systemctl list-timers
systemctl disable <timer name>
```

---

#### View and Remove "at" Jobs

```bash
atq
atrm <job number>
```

> **Operator Note:** Attackers often use `at` for one-time malicious execution.

---

## Running Script (Windows → Bulk Disable All Scheduled Tasks)

```powershell
Get-ScheduledTask | ForEach-Object {
    Disable-ScheduledTask -TaskName $_.TaskName -TaskPath $_.TaskPath
}
```

> **Operator Note:** Use with caution. Recommended only for systems where complete lock-down is required.

---

## Dependencies

* Local Administrator or Domain Admin rights (Windows)  
* Root or sudo rights (Linux)  
* PowerShell (Windows)  
* Cron/systemctl/at utilities (Linux)  
* Coordination with system owners

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell ScheduledTasks module | Windows | View/export/disable tasks |
| schtasks.exe | Windows | CLI alternative |
| Task Scheduler GUI (taskschd.msc) | Windows | Visual inspection |
| crontab / systemctl / at | Linux | Cron, system timers, and at jobs management |
| SIEM (Splunk, Sentinel) | Cross-platform | Detection of scheduled job execution |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Identify all scheduled tasks on impacted hosts.
- [ ] Document tasks prior to disabling.
- [ ] Disable malicious or non-essential scheduled tasks.
- [ ] Validate disabled tasks no longer execute.
- [ ] Leave tasks in place for forensic review unless removal is approved.
- [ ] Track all changes in IR documentation.

### Best Practices

- Disable rather than delete to preserve forensic evidence.
- Use PowerShell or scheduled task export for easy rollback.
- Comment cron jobs on Linux instead of deleting.
- Use systemd timers and "at" jobs review for advanced persistence detection.
- Validate tasks using IR or IT change records to avoid accidental disruption.

---

## References

[Microsoft Scheduled Task Cmdlets](https://learn.microsoft.com/en-us/powershell/module/scheduledtasks/)  
[Linux Crontab Manual](https://man7.org/linux/man-pages/man5/crontab.5.html)  
[Systemd Timers Documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd.timer.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide covering detection, disabling, and validation of scheduled tasks and jobs | Leo |
