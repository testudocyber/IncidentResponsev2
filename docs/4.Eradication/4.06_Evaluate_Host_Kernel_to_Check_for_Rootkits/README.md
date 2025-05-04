# 4.06 Evaluate Host Kernel to Check for Rootkits

## Task

Evaluate Host Kernel to Check for Rootkits

## Conditions

Given a suspected compromised Windows desktop computer system, a local administrator account, incident response tools, and one or more Indicators of Compromise (IOCs).

---

## Standards

* The team member annotates the system name, serial number, manufacturer, and time of action in the Incident Log.
* The team member inserts media that contains the rootkit detection application.
* The team member runs the rootkit detection application with the correct parameters and exports the data to the local drive, external USB media, or network share.
  * Note: Removal of the rootkit is not part of this evaluation, only identification.
* Review screen or tool log file for indicators of the presence of a rootkit against provided IOCs.

---

## End State

Rootkit-related elements are identified.

---

## Notes

When a rootkit is suspected, there are various considerations for completing this task:

- Some rootkits may target removable storage or detection tools themselves.
- Use caution when introducing tools via USB → network transfer or remote execution (e.g. PSExec, WinRM) is preferable.
- Some tools may need to be renamed to avoid detection/blocking by malware.

---

## Manual Steps

### For Linux

#### Chkrootkit

* Install chkrootkit

```bash
sudo apt-get install chkrootkit
```

* Run chkrootkit

```bash
chkrootkit
```

* Investigate any findings

```bash
chkrootkit -h
```

---

#### Rkhunter

* Install rkhunter

```bash
sudo apt-get install rkhunter
```

* Run rkhunter with check mode

```bash
rkhunter -c
```

* Review log file

```bash
cat /var/log/rkhunter.log
```

* Investigate any findings.

---

### For Windows

#### GMER

* Download GMER (http://www.gmer.net)
* Run as Administrator.
* Allow scan to complete.
* Review rootkit and hidden process detection results.
* Save log for incident documentation.

#### TDSSKiller

* Download TDSSKiller (https://usa.kaspersky.com/downloads/tdsskiller)
* Run TDSSKiller as Administrator.
* Select "Change parameters" and enable deep scan options.
* Run scan and review results.
* Save scan log for records.

#### McAfee Rootkit Remover

* Download (https://www.mcafee.com/us/downloads/free-tools/rootkitremover.aspx)
* Run executable and scan system.
* Review findings and export log.

> **Operator Note:** These tools should be used in Safe Mode or offline when possible to avoid interference from active rootkits.

---

### For macOS

#### KnockKnock (Objective-See)

* Download from https://objective-see.org/products/knockknock.html
* Run application.
* Review persistent programs and rootkit indicators.

---

## Dependencies

* Administrative access to system (local admin account or remote access).
* Incident Response tools (Chkrootkit, Rkhunter, GMER, TDSSKiller, McAfee Rootkit Remover).
* Secure location for log export and retention.
* Access to IOC lists for comparison.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Chkrootkit | Linux | apt or source | Command line scanning |
| Rkhunter | Linux | apt or source | Command line rootkit checks |
| GMER | Windows | Download | Hidden process and rootkit detection |
| TDSSKiller | Windows | Download | Kernel rootkit detection |
| McAfee Rootkit Remover | Windows | Download | Known rootkit removal tool |
| KnockKnock | macOS | Download | Persistent and hidden programs inspection |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Record host information and start time in incident log.
- [ ] Prepare and safely transfer detection tools to target system.
- [ ] Run detection tool based on OS type and capture findings.
- [ ] Investigate and interpret results → compare against IOCs.
- [ ] Export results to secure location.
- [ ] Report findings to Incident Lead and document results.

### Best Practices

- Avoid USB use on compromised hosts when possible.
- Perform scans in offline or safe boot environments to avoid interference.
- Cross-check detection tool findings with forensic images when possible.
- Retain logs and screenshots for post-incident analysis.

---

## References

[chkrootkit - Linux](http://www.chkrootkit.org/)  
[rkhunter - Linux](http://rkhunter.sourceforge.net/)  
[GMER](http://www.gmer.net)  
[Windows Defender Offline](https://www.microsoft.com/en-us/windows/windows-defender-offline)  
[TDSSKiller - Kaspersky](http://usa.kaspersky.com/downloads/TDSSKiller)  
[McAfee Rootkit Remover](http://www.mcafee.com/us/downloads/free-tools/rootkitremover.aspx)  
[KnockKnock (macOS)](https://objective-see.org/products/knockknock.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Corrected and expanded operator version incorporating Linux, Windows, macOS and advanced techniques | Leo |
