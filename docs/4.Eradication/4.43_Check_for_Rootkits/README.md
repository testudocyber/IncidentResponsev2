# 4.33 Check for Rootkits

## Task Check Systems for Rootkits and Hidden Malware Artifacts

## Conditions

Given access to impacted systems and elevated permissions, the operator will utilize rootkit detection tools and procedures to identify hidden malware, kernel-level implants, and unauthorized hooks that could subvert eradication efforts.

> **Operator Note:** Rootkits are designed to hide attacker presence and evade detection. Checking for rootkits is critical during eradication to ensure no hidden persistence mechanisms remain.

## Standards

* Team member selects and deploys appropriate rootkit detection tools for the platform.
* Team member performs thorough scans using automated tools and manual verification methods.
* Team member reviews and validates any detections, escalating as necessary.
* Team member documents rootkit scan results and confirms clean status.
* Team member initiates remediation or reimaging procedures for systems with confirmed rootkits.

## End State

Impacted systems have been scanned for rootkits and hidden malware. Any detections have been remediated, and the environment is validated as clean.

---

## Notes

- Rootkit detection requires elevated privileges (Administrator, root).
- Some rootkits may not be detectable by userland tools → forensic validation or reimaging may be necessary.
- Perform rootkit scanning in safe mode or from trusted boot media when possible.

---

## Manual Steps

### Step 1: Select Rootkit Detection Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| GMER | Windows | [Download](https://www.gmer.net/) | GUI → Scan for hooks, hidden processes |
| Windows Defender Offline Scan | Windows | Built-in → Settings → Virus & Threat Protection → Offline Scan | Scans outside OS environment |
| Rootkit Hunter (rkhunter) | Linux | `apt install rkhunter` or `yum install rkhunter` | `rkhunter --check` |
| chkrootkit | Linux | `apt install chkrootkit` or `yum install chkrootkit` | `chkrootkit` |
| KnockKnock | macOS | Install via Objective-See website | GUI → View persistent items and unsigned code |

> **Operator Note:** Use multiple tools for high assurance. Rootkit detection is prone to false negatives.

---

### Step 2: Prepare System for Scanning

- Ensure antivirus and security software are updated and running.
- Stop unnecessary services to reduce noise.
- Where possible, boot into Safe Mode or from trusted recovery media.

---

### Step 3: Perform Rootkit Scans

#### Windows (GMER)

- Launch GMER
- Select "Scan" → allow full scan to complete
- Review for hidden modules, hooks, and processes

#### Windows Defender Offline Scan

- From Windows Security → Virus & Threat Protection
- Select "Offline Scan" and reboot
- Review results after reboot

#### Linux (Rootkit Hunter)

```bash
sudo rkhunter --update
sudo rkhunter --check
```

#### Linux (chkrootkit)

```bash
sudo chkrootkit
```

#### macOS (KnockKnock)

- Run KnockKnock
- Review unsigned, persistent, or hidden launch agents and daemons

---

### Step 4: Review Findings

- Validate results (some detections may be false positives)
- Escalate or initiate reimaging procedures for confirmed rootkit infections
- For suspicious but unconfirmed results → perform secondary scans or forensic analysis

---

### Step 5: Document Results

- Record scan tool(s) used, date/time, results, and actions taken
- Document as clean or infected
- Retain logs as part of IR case file

---

## Running Script (Linux - Rootkit Hunter Scan and Output)

```bash
sudo rkhunter --check --report-warnings-only > rkhunter_scan_results.txt
```

> **Operator Note:** Always redirect output to file for review and inclusion in incident documentation.

---

## Dependencies

* Administrative/root access on systems
* Rootkit detection tools installed and updated
* Network or local access to systems (consider offline boot media for high-risk cases)
* Coordination with enclave/system owners before running intrusive scans

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| GMER | Windows | Manual download | GUI based, advanced rootkit scanning |
| Windows Defender Offline | Windows | Built-in | Scans outside running OS |
| Rootkit Hunter | Linux | `apt/yum install rkhunter` | Command line scan for rootkits and anomalies |
| chkrootkit | Linux | `apt/yum install chkrootkit` | Simple command-line rootkit scanner |
| KnockKnock | macOS | Download from Objective-See | GUI detection of unsigned and persistent items |

> **Operator Note:** If rootkits are detected, consider reimaging the system as part of eradication best practices.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Deploy rootkit detection tools appropriate to platform.
- [ ] Update tools and signatures where applicable.
- [ ] Perform full rootkit scans on impacted hosts.
- [ ] Review scan results and validate findings.
- [ ] Document results and remediation actions.
- [ ] If rootkits are confirmed, escalate to IR lead and initiate system rebuild if necessary.

### Best Practices

- Perform rootkit scans early during eradication → before recovery begins.
- Use multiple detection methods for higher confidence.
- Offline scanning is preferred for highly sensitive or mission critical systems.
- Retain scan logs and evidence for forensic analysis.

---

## References

[GMER Rootkit Scanner](https://www.gmer.net/)  
[Rootkit Hunter (rkhunter)](https://rkhunter.sourceforge.net/)  
[chkrootkit](http://www.chkrootkit.org/)  
[KnockKnock - Objective-See](https://objective-see.org/products/knockknock.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for rootkit scanning with tools, installation, operator recommendations | Leo |
