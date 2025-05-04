# 4.09.5 Evaluate Host Activity Baseline

## Task

Evaluate and establish a baseline of host activity to identify abnormal processes, services, user behavior, and persistence mechanisms on potentially compromised systems.

---

## Conditions

Given access to hosts across Windows, Linux, and macOS platforms and appropriate local or remote administrative privileges.

---

## Standards

* Collect representative data of running processes, services, network connections, and user activity.
* Document "normal" host behavior.
* Identify deviations or anomalies that may indicate malware, persistence, lateral movement, or exfiltration.
* Validate findings with system owners and mission requirements.

---

## End State

A documented host activity baseline exists, allowing rapid detection of anomalies and informing eradication and recovery plans.

---

## Notes

- Host baselining is critical when visibility is limited or during targeted attacks.
- "Normal" may vary based on host role → understand the mission and owner expectations.
- Establishing this early allows faster triage later.

---

## Manual Steps

### Step 1: Determine Host Role and Critical Functions

* Consult asset inventories, system owners, or prior assessments to determine:
  - Is this a server, workstation, or appliance?
  - What is its normal function (ex: Domain Controller, File Server, User Workstation)?

> **Operator Note:** Expectations differ greatly → A DC running RDP is normal, a user workstation running RDP server is suspicious.

---

### Step 2: Collect Running Processes

#### Windows

```powershell
Get-Process | Sort-Object ProcessName | Format-Table -AutoSize
```

Save output:

```powershell
Get-Process | Out-File C:\IR\baseline_process_list.txt
```

#### Linux / macOS

```bash
ps aux --sort=pid
```

Save output:

```bash
ps aux --sort=pid > /tmp/baseline_process_list.txt
```

> **Operator Note:** Look for unsigned, unusual named processes, or processes running from temp directories.

---

### Step 3: Collect Network Connections

#### Windows

```powershell
Get-NetTCPConnection | Format-Table -AutoSize
```

#### Linux / macOS

```bash
ss -tulnp
```

> **Operator Note:** Investigate processes listening on unexpected ports (ex: Powershell hosting a web server).

---

### Step 4: Collect Scheduled Tasks and Autoruns

#### Windows

```powershell
Get-ScheduledTask | Format-Table -AutoSize
```

```powershell
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

#### Linux

```bash
crontab -l
ls /etc/cron* /var/spool/cron
```

#### macOS

```bash
launchctl list
```

> **Operator Note:** Persistence mechanisms often hide here. Flag anything unfamiliar.

---

### Step 5: Collect User Activity

#### Windows

```powershell
quser
Get-EventLog -LogName Security -InstanceId 4624 | Select-Object -First 20
```

#### Linux/macOS

```bash
who
last
```

> **Operator Note:** Unexpected logins (especially at odd hours) → red flag.

---

### Step 6: Collect Installed Applications / Packages

#### Windows

```powershell
Get-WmiObject -Class Win32_Product | Select-Object Name
```

#### Linux

```bash
dpkg -l
```

#### macOS

```bash
system_profiler SPApplicationsDataType
```

> **Operator Note:** Look for unauthorized or suspicious software.

---

### Step 7: Document and Establish Baseline

Create a report documenting:

| Hostname | Normal Processes | Normal Ports | Installed Software | Users/Logins | Notes |
|----------|------------------|--------------|--------------------|--------------|-------|
| WIN-DC01 | lsass.exe, svchost.exe | 88, 135, 445 | AV, Adobe Reader | admin, svc-account | DC - normal baseline |
| USER-PC | chrome.exe, outlook.exe | none | Chrome, Office | user | normal user profile |

---

### Step 8: Identify and Investigate Anomalies

- Flag anything outside of expected processes/ports
- Correlate suspicious processes with hashes and VirusTotal
- Investigate abnormal user accounts or logins
- Identify unscheduled persistence mechanisms

> **Operator Note:** Not everything unknown is bad → escalate suspicious findings to Intel or IR Lead.

---

## Dependencies

* Access to target hosts (local or remote).
* Administrative privileges.
* Ability to run system commands or scripts.
* Logging and recording capability.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Sysinternals Suite (Autoruns, PsExec, Procmon) | Windows | Download | Deep process and autorun analysis |
| Velociraptor | Cross-platform | Deploy agent | Enterprise-scale endpoint telemetry |
| OSQuery | Cross-platform | Install agent | SQL-based host visibility |
| PowerShell / Bash | Native | Built-in | Collect process and system info |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Determine host role and expected behavior.
- [ ] Collect and document running processes.
- [ ] Collect and document network connections.
- [ ] Collect and document autoruns and scheduled tasks.
- [ ] Collect and document logged-in users and historical login data.
- [ ] Collect and document installed applications.
- [ ] Review and analyze for anomalies.
- [ ] Validate findings with asset owner or IR lead.

### Best Practices

- Compare similar host roles against each other to spot outliers.
- Use hash validation and threat intelligence for unknown binaries.
- Automate collection across multiple systems when possible.
- Preserve all raw outputs → useful for later incident review.

---

## References

- [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/)
- [Velociraptor](https://velociraptor.app/)
- [OSQuery](https://osquery.io/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused guidance and examples | Leo |
