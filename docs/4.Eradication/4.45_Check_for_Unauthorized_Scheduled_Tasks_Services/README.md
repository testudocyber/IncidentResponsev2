# 4.45 Check for Unauthorized Scheduled Tasks and Services

## Task Identify and Remove Unauthorized Scheduled Tasks and Services

## Conditions

Given access to impacted systems and elevated permissions, the operator will enumerate, review, and remove attacker-created or unauthorized scheduled tasks and services designed to maintain persistence or execute malicious code.

> **Operator Note:** Scheduled tasks and unauthorized services are often used by attackers for persistence. Thorough review and cleanup of these artifacts is critical during eradication.

## Standards

* Team member enumerates all scheduled tasks and services across impacted systems.
* Team member validates each task and service against known good baselines.
* Team member disables and removes unauthorized tasks and services.
* Team member documents all removals and retains records for post-incident review.
* Team member validates removal and confirms no residual unauthorized entries remain.

## End State

All unauthorized and attacker-created scheduled tasks and services have been identified, validated, removed, and documented. No malicious persistence mechanisms remain.

---

## Notes

- Unauthorized tasks and services may have misleading or disguised names.
- Removal should be coordinated with system owners to prevent accidental removal of legitimate services.
- Retain forensic copies and documentation of malicious tasks and services removed.

---

## Manual Steps

### Step 1: Enumerate Scheduled Tasks

#### Windows

```powershell
Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"} | Format-Table TaskName, State, LastRunTime
schtasks /query /fo LIST /v
```

#### Linux (cron jobs)

```bash
crontab -l
ls /etc/cron.*
cat /etc/crontab
systemctl list-timers
```

#### macOS (launchd and cron)

```bash
launchctl list
crontab -l
```

> **Operator Note:** Pay attention to tasks with non-standard user names, random file names, or unusual execution paths (AppData, Temp folders).

---

### Step 2: Enumerate Services

#### Windows

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"} | Format-Table Name, DisplayName, Status
Get-WmiObject win32_service | Select-Object Name, StartMode, State, PathName
```

#### Linux

```bash
systemctl list-units --type=service --state=running
chkconfig --list
```

#### macOS

```bash
launchctl list
sudo launchctl print system
```

---

### Step 3: Investigate Suspicious Tasks and Services

Criteria for suspicious tasks/services:

- Created recently (within incident timeframe)
- Random, non-descriptive names (e.g., "svc-host", "update123")
- Execution path pointing to temp or user folders
- Run as SYSTEM or root
- No corresponding legitimate application or vendor

> **Operator Note:** Coordinate findings with IR and system/application owners.

---

### Step 4: Disable and Remove Unauthorized Entries

#### Windows (Disable and Remove Scheduled Task)

```powershell
Disable-ScheduledTask -TaskName "MaliciousTask"
Unregister-ScheduledTask -TaskName "MaliciousTask" -Confirm:$false
```

#### Windows (Disable and Remove Service)

```powershell
Stop-Service -Name "MaliciousService"
sc.exe delete "MaliciousService"
```

#### Linux (Remove Cron Jobs)

```bash
crontab -r -u attackeruser
sudo rm /etc/cron.d/maliciouscron
sudo systemctl disable malicious.service
sudo systemctl stop malicious.service
sudo rm /etc/systemd/system/malicious.service
```

#### macOS (Remove Launch Agent/Daemon)

```bash
sudo launchctl bootout system /Library/LaunchDaemons/malicious.plist
sudo rm /Library/LaunchDaemons/malicious.plist
```

---

### Step 5: Validate Removal

- Re-enumerate tasks/services to confirm removal
- Review logs to ensure no restarts/re-creations
- Monitor system for signs of persistence attempts

---

## Running Script (Windows - Find Non-Microsoft Running Services)

```powershell
Get-WmiObject win32_service | Where-Object {$_.PathName -notlike "*Microsoft*"} | Format-Table Name, State, PathName
```

> **Operator Note:** Focus on non-standard service paths and names during eradication validation.

---

## Dependencies

* Administrative/root access on hosts
* Baseline of authorized services/tasks (if available)
* Coordination with system/application owners
* IR team approval for disabling/removal

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Autoruns (Sysinternals) | Windows | Download from Sysinternals | View all scheduled tasks, services, and persistence |
| PowerShell | Windows | Built-in | View, disable, remove scheduled tasks/services |
| systemctl / crontab | Linux | Built-in | Manage services and scheduled tasks |
| launchctl | macOS | Built-in | Manage launch daemons and agents |

> **Operator Note:** Autoruns is highly recommended for full Windows persistence review during eradication.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate all scheduled tasks and services.
- [ ] Validate each task/service → identify unauthorized entries.
- [ ] Disable and remove unauthorized or suspicious tasks and services.
- [ ] Validate removal and absence of re-creation.
- [ ] Document findings and actions.

### Best Practices

- Prefer disabling before deletion for rollback and evidence preservation.
- Use multiple detection methods (built-in commands + tools like Autoruns).
- Retain forensic copies of malicious task/service configuration where possible.
- Validate all systems post-eradication to confirm no persistence mechanisms remain.

---

## References

[Microsoft Sysinternals Autoruns](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns)  
[systemd - Linux Services](https://www.freedesktop.org/wiki/Software/systemd/)  
[Apple macOS launchctl Reference](https://www.manpagez.com/man/1/launchctl/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for checking/removing unauthorized scheduled tasks and services with platform guidance | Leo |
