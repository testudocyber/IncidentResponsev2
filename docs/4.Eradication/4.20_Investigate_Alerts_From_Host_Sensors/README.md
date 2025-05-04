# 4.20 Investigate Alerts From Host Sensors

## Task

Investigate and analyze alerts generated from host-based intrusion detection systems (HIDS) and endpoint detection agents to confirm malicious activity, identify related artifacts, and escalate findings for containment and eradication actions.

---

## Conditions

Given:

* HIDS agents installed on all relevant hosts (e.g. Wazuh, Sysmon, OSSEC).
* Fully configured SIEM receiving host sensor telemetry (e.g. Security Onion, Elasticsearch, Splunk).
* Notification of a host sensor alert.
* Access to the impacted host(s) with administrative credentials.
* Approval from network or system owner to conduct analysis on host(s).

---

## Standards

### Network Analyst Responsibilities

* Recognize and triage host sensor alerts in SIEM.
* Rule out false positives using available context (e.g. threat intel, whitelisting).
* Correlate with network telemetry → connections, suspicious destinations, etc.
* Notify team leader and hand off findings to Host Analyst team.
* Continue monitoring host and related infrastructure for additional indicators or lateral movement.

### Host Analyst Responsibilities

* Determine host Operating System (Windows, Linux, MacOS).
* Log into host using authorized credentials.
* Use **native OS tools** to validate suspicious activity:
  * **Windows**:
    * Network → `netstat`, `Get-NetTCPConnection`, `TCPView`
    * Processes → `tasklist`, `Get-Process`, `Process Explorer`
    * Accounts → `net user`, `Get-LocalUser`
    * Persistence → `autoruns`, `reg query`
    * Event Logs → `eventvwr.msc`, `Get-WinEvent`
  * **Linux**:
    * Network → `netstat -an`, `ss -tuln`
    * Processes → `ps aux`, `top`, `htop`
    * Accounts → `cat /etc/passwd`, `last`, `who`
    * Logs → `/var/log/auth.log`, `journalctl`
  * **macOS**:
    * Network → `lsof -i`, `netstat -an`
    * Processes → `top`, `ps aux`
    * Accounts → `dscl . list /Users`
    * Logs → `Console.app`, `log show`

* Use **third-party tools** if available:
  * **Sysinternals Suite (Windows)** → `Procmon`, `Process Explorer`, `TCPView`
  * **Lsof (Linux/macOS)** → Review file and network activity
  * **Security Agents** → Wazuh/OSSEC local agent logs

* Investigate potential compromised accounts:
  * Login anomalies
  * Recent password changes
  * Privilege escalations
  * Suspicious service account activity

* Investigate lateral movement:
  * SMB sessions → `net session` (Windows)
  * Remote logins → `/var/log/secure`, `last` (Linux)
  * Remote desktop activity → Event Logs (Windows)
  * MacOS screen sharing → `log show`

* Gather IOC artifacts:
  * Suspicious process hashes
  * Malicious command line executions
  * Dropped malware files
  * Registry persistence keys (Windows)
  * Crontabs/systemd units (Linux)
  * Launch agents (macOS)

* Document all findings and prepare detailed report for escalation to IR leadership.

---

## End State

* All host-based alerts are validated, triaged, and investigated.
* Any confirmed or suspected malicious activity is documented with relevant artifacts.
* Findings are escalated to incident commander / team lead for containment and eradication decisions.

---

## Notes

* Host sensor alerts are critical early warnings. They must be reviewed in detail — ignore at your peril.
* False positives (e.g. legitimate admin tools) must be ruled out carefully.
* Always preserve volatile artifacts (memory, running processes) before taking remediation actions (such as kill process or reboot).

---

## Manual Steps

### From SIEM (Security Onion / Kibana / Splunk)

* Login to SIEM
* Query alerts → Filter by host sensor detections
* Review correlated network activity (connections, IPs, downloads)
* Review host telemetry:
  * Process activity
  * Parent-child process relationships
  * Network connections
  * Persistence attempts

### Validate on Host

* RDP/SSH or physically access system (if authorized)
* Run OS native commands to confirm process and network state
* Dump running processes
* Dump network connections
* Collect persistence artifacts (startup, autoruns)
* Review local security and system logs

### Correlate and Report

* Confirm or refute malicious behavior
* Summarize findings → IOC presence, process ancestry, privilege levels
* Report to team lead → Include screenshots, logs, commands used

---

## Running Script

* From previously referenced Sysmon deployment in `4.Eradication/4.08_Deploy_or_evaluate_host_sensors/scripts/Sysmon_Wazuh_push.ps1`

```powershell
# Example to pull all events from Sysmon for review
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" | Export-Csv SysmonLogs.csv
```

---

## Dependencies

* Sysmon (Windows)
* Wazuh / OSSEC agent
* Security Onion or Splunk
* Native OS tools (cmd, PowerShell, Bash, Console)
* Admin credentials to access hosts
* Network connectivity to hosts or console access

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Sysinternals Suite | Windows | Portable | Process, network, autorun analysis |
| Lsof | Linux/macOS | Native | File and network descriptor analysis |
| Event Viewer / Get-WinEvent | Windows | Native | Event log review |
| Kibana / Elasticsearch | Cross-platform | Pre-installed in Security Onion | Alert review + correlation |
| Splunk | Cross-platform | Installable | Alert review + correlation |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Triage alert in SIEM → Determine severity + impacted host
- [ ] Confirm host OS → Validate correct tooling
- [ ] Access host → Use native and third-party tools to validate process, network, and persistence
- [ ] Investigate user accounts and privilege escalation indicators
- [ ] Determine if IOC is confirmed → Malicious file/process/behavior
- [ ] Correlate lateral movement indicators
- [ ] Document all findings and escalate

### Best Practices

* Use snapshot/backup or memory capture prior to intrusive investigation
* Do not delete/alter suspicious artifacts until analysis complete
* Cross-reference process hashes and command lines with threat intelligence (VirusTotal, Hybrid Analysis)
* Avoid cloud uploads without proper authorization
* Coordinate handoff with containment team immediately upon confirmed compromise

---

## References

* [Security Onion Documentation](https://docs.securityonion.net/en/16.04)
* [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/)
* [Wazuh Documentation](https://documentation.wazuh.com/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded with operator workflow, validation procedures, and escalation guidance | Leo |
