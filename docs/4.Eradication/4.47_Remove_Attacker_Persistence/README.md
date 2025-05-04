# 4.47 Remove Attacker Persistence Mechanisms

## Task Identify and Remove All Attacker Persistence Mechanisms

## Conditions

Given administrative access to impacted systems, the operator will enumerate and remove all attacker-installed persistence mechanisms. These include scheduled tasks, services, startup items, registry entries, scripts, user profiles, and other footholds left to maintain access.

> **Operator Note:** Persistence can be stealthy and layered. Operators must carefully search, validate, and remove ALL unauthorized persistence mechanisms to prevent re-compromise.

## Standards

* Team member conducts full review of known persistence vectors on each impacted system.
* Team member uses validated tools and commands to enumerate and detect persistence artifacts.
* Team member removes or disables identified unauthorized persistence mechanisms.
* Team member documents all findings and actions taken, including validation of removal.
* Team member ensures post-removal verification is performed.

## End State

All attacker persistence mechanisms have been removed and validated. Systems are free of unauthorized tasks, services, registry keys, startup items, and other persistence artifacts.

---

## Notes

- Use layered detection methods (manual inspection, automated tools, EDR telemetry).
- Always document persistence artifacts before removal (evidence + lessons learned).
- Validate system functionality after removal → some attackers use hijacked legitimate mechanisms.

---

## Manual Steps

### Step 1: Enumerate Common Persistence Mechanisms

#### Scheduled Tasks and Cron Jobs

##### Windows

```powershell
Get-ScheduledTask | Select-Object TaskName, State, Actions
schtasks /query /fo LIST /v
```

##### Linux

```bash
crontab -l
ls -alh /etc/cron.*
systemctl list-timers
```

##### macOS

```bash
launchctl list
crontab -l
```

---

#### Services and Daemons

##### Windows

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"} | Format-Table Name, DisplayName, PathName
```

##### Linux

```bash
systemctl list-units --type=service
chkconfig --list
```

##### macOS

```bash
launchctl list
```

---

#### Registry and Startup Items (Windows)

```powershell
Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"
Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```

> **Operator Note:** Also review `HKLM\SYSTEM\CurrentControlSet\Services` for driver-level persistence.

---

#### WMI Event Subscriptions (Windows)

```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter
```

> **Operator Note:** WMI persistence is common for fileless malware and advanced actors.

---

#### User Profiles and Login Items

##### Windows

```powershell
Get-ChildItem "C:\Users" -Force
```

##### Linux/macOS

```bash
ls -la /home
ls -la /Users
```

> **Operator Note:** Review `.bash_profile`, `.bashrc`, `.zshrc`, `.login` files for script-based persistence.

---

### Step 2: Investigate and Validate Suspicious Entries

Indicators of malicious persistence:

- Non-standard names (random strings, benign looking names)
- File paths in user directories or temp folders
- Unknown publishers or unsigned executables
- Recently created tasks, services, or registry entries

Coordinate with IR lead and system owner for validation prior to removal.

---

### Step 3: Remove Unauthorized Persistence Mechanisms

#### Scheduled Tasks

```powershell
Unregister-ScheduledTask -TaskName "MaliciousTask" -Confirm:$false
```

#### Services

```powershell
Stop-Service -Name "MaliciousService"
sc.exe delete "MaliciousService"
```

#### Registry Startup Keys

```powershell
Remove-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "BadStartupEntry"
```

#### WMI Subscriptions

```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter | Remove-WmiObject
```

#### Linux/macOS Cron Jobs and Startup Files

```bash
crontab -r -u malicioususer
rm /etc/cron.d/maliciouscron
rm ~/.bash_profile
rm /Library/LaunchDaemons/malicious.plist
launchctl bootout system /Library/LaunchDaemons/malicious.plist
```

---

### Step 4: Validate Removal and Monitor

- Re-enumerate persistence vectors to ensure no entries remain.
- Monitor system logs for unauthorized task/service creation attempts.
- Validate with EDR/XDR or audit log collection.

---

## Running Script (Windows - Quick Scheduled Task Audit)

```powershell
Get-ScheduledTask | Select-Object TaskName, Description, State, Actions | Export-Csv ScheduledTasks.csv -NoTypeInformation
```

> **Operator Note:** Retain before/after audit records for incident documentation.

---

## Dependencies

* Administrative/root access on target systems
* IR toolkit including PowerShell, Autoruns, systemctl, launchctl, and WMI modules
* Approval from IR lead for removal actions
* Coordination with system owners for validation

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Autoruns (Sysinternals) | Windows | Download from Microsoft Sysinternals | Visualize and remove autorun and startup entries |
| PowerShell | Windows | Built-in | Enumerate and remove tasks, services, WMI |
| EDR/XDR Platforms (e.g., Crowdstrike, SentinelOne) | Cross-platform | Enterprise deployment | Detect and alert on unauthorized persistence |
| systemctl + crontab | Linux | Built-in | Manage services and scheduled tasks |
| KnockKnock | macOS | Download from Objective-See | GUI analysis of persistent login items |

> **Operator Note:** Always prefer using trusted enterprise tooling (EDR/XDR) to supplement manual detection and removal.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate all common persistence vectors (tasks, services, registry, WMI, startup).
- [ ] Validate suspicious entries → determine authorized vs unauthorized.
- [ ] Disable and remove unauthorized persistence.
- [ ] Validate removal and monitor for re-creation attempts.
- [ ] Document all actions and preserve artifacts where possible.

### Best Practices

- Prefer disabling over deletion where possible to preserve evidence.
- Validate with system/application owners before removing borderline entries.
- Use multiple discovery methods (manual + automated).
- Retain forensic images or audit logs of persistence mechanisms before removal.

---

## References

[Sysinternals Autoruns](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns)  
[WMI Persistence (MITRE ATT&CK T1546.003)](https://attack.mitre.org/techniques/T1546/003/)  
[Launchd and launchctl on macOS](https://www.launchd.info/)  

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for attacker persistence removal with platform techniques and tools | Leo |
