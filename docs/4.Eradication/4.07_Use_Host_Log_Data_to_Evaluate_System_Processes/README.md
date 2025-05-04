# 4.07 Use Host Log Data to Evaluate System Processes

## Task

Use Host Log Data to Evaluate System Processes for Signs of Malicious Activity

## Conditions

Given a suspected compromised Windows desktop computer system, local administrator account credentials, one or more Indicators of Compromise (IOCs), and incident response software.

---

## Standards

* The team member annotates the system name, serial number, manufacturer, and time of action in the Incident Log.
* The team member logs into or connects remotely using administrator privileges.
* The team member obtains an image of the target computer's physical memory.
* The team member calculates cryptographic hashes (MD5 or SHA1) of the memory image.
* The team member gathers file hashes of all files on the system.
* The team member uses incident response software to collect the following:

  - System date/time and timezone
  - Operating system version and service pack
  - RAM size
  - Disk and filesystem details
  - USB connection history
  - Autorun software
  - Services and status
  - Installed software
  - Scheduled tasks
  - User accounts and groups
  - Network interfaces, routing table, ARP cache, DNS cache
  - Network connections (with associated processes)
  - Loaded drivers
  - Open files and handles
  - Running processes and PID info
  - Windows event logs
  - Antivirus/HIDS/Firewall logs

* The team member reviews logs for matches to IOCs.

---

## End State

Log and text file entries are found and correlated to potential malicious behavior or known IOCs.

---

## Notes

- Host log and process data is essential for detecting rootkits, malware persistence, and lateral movement.
- Cross-reference logs with threat intelligence and previously discovered IOCs.
- Coordinate with system owners and IR lead before aggressive forensic analysis on production systems.

---

## Manual Steps

### Step 1: Prepare Collection Tools

- Preferred IR Tools (Windows):
  - Sysinternals Suite (Autoruns, PsList, Handle, TCPView)
  - Velociraptor
  - PowerShell

- Preferred IR Tools (Linux/macOS):
  - lsof
  - netstat / ss
  - ps aux
  - OSQuery

---

### Step 2: Collect System Process and Host Information

#### Windows (PowerShell examples)

```powershell
# List running processes with PID, username and command line
Get-WmiObject Win32_Process | Select-Object Name, ProcessId, CommandLine

# List network connections with process info
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, State, OwningProcess

# List auto-start locations
Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"

# List scheduled tasks
Get-ScheduledTask | Select-Object TaskName, State
```

#### Linux/macOS

```bash
# List running processes
ps aux

# List network connections
ss -tulnp

# List open files and network
lsof -i

# List autoruns (Linux example: cron jobs)
crontab -l
```

---

### Step 3: Collect and Analyze Windows Event Logs

```powershell
# Export event logs
wevtutil epl Security Security.evtx
wevtutil epl System System.evtx
wevtutil epl Application Application.evtx

# Display failed logons
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4625} | Format-List
```

---

### Step 4: Collect Additional Metadata

- USB history → Registry or forensic tool (USBDeview or Velociraptor)
- Installed software → WMIC or PowerShell
- Drivers and loaded kernel modules → PowerShell or Autoruns

---

### Step 5: Cross-reference Collected Data with IOCs

- Use IOC list provided (hashes, domain names, IPs, filenames)
- Search logs and data for presence of any matches
- Flag any suspicious entries for escalation

---

### Step 6: Save and Export Findings

- Export collected data and analysis to secure storage (USB, IR share).
- Document findings in IR Case Management or Incident Log.

---

## Running Script Example (Automated Collection)

```powershell
# Simple PowerShell collection script
Get-Process | Out-File C:\IR\process_list.txt
Get-NetTCPConnection | Out-File C:\IR\network_connections.txt
Get-ScheduledTask | Out-File C:\IR\scheduled_tasks.txt
Get-WinEvent -LogName Security -MaxEvents 1000 | Out-File C:\IR\security_events.txt
```

> **Operator Note:** Use IR Collection Frameworks like Velociraptor for automated and scalable host data acquisition when available.

---

## Dependencies

* Local admin or elevated access on host
* Access to IOC lists
* Storage location for data export
* IR data collection toolkit

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Sysinternals Suite | Windows | Download | Comprehensive host analysis |
| Velociraptor | Cross-platform | Deploy agent | Enterprise-scale host forensics |
| OSQuery | Cross-platform | Install agent | SQL-based query of host data |
| PowerShell | Windows | Built-in | Scripting and host interrogation |
| lsof, ps, netstat | Linux/macOS | Built-in | Process and network analysis |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Record system details and annotate in incident log.
- [ ] Collect process and network information.
- [ ] Collect autoruns, scheduled tasks, and startup programs.
- [ ] Collect event logs and scan for security-relevant entries.
- [ ] Cross-reference findings against IOCs.
- [ ] Document findings and export artifacts securely.

### Best Practices

- Automate collection where possible to reduce human error.
- Validate findings with threat intelligence team.
- Maintain chain of custody for collected forensic artifacts.
- Use standardized naming and file structure when exporting logs and analysis.

---

## References

- [Sysinternals Tools](https://docs.microsoft.com/en-us/sysinternals/)
- [Velociraptor](https://velociraptor.app/)
- [PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)
- [OSQuery](https://osquery.io/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Corrected and expanded operator version with deep host process analysis guidance | Leo |
