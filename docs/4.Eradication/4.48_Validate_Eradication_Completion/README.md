# 4.48 Validate Eradication Completion

## Task Validate That All Malicious Artifacts Have Been Removed and Environment is Ready for Recovery

## Conditions

After completing eradication activities (malware removal, persistence elimination, unauthorized accounts removal), the operator will execute validation steps to confirm that the environment is clean and no attacker footholds or residual risks remain.

> **Operator Note:** This is a critical control gate before transition to recovery. No recovery action should begin until eradication validation is complete and documented.

## Standards

* Team member performs validation activities across all impacted and at-risk systems.
* Team member uses tools and manual review to confirm eradication success.
* Team member coordinates validation efforts with stakeholders (IT, IR lead, system owners).
* Team member documents all validation actions and results.
* Team member escalates any residual detections for further eradication before recovery begins.

## End State

Validation confirms that all malicious artifacts have been removed, systems are clean, and no persistence or unauthorized access points remain. The environment is approved for transition to recovery.

---

## Notes

- Validation is a multi-layered process → host, network, and enterprise levels.
- Thoroughness is critical to avoid re-infection or re-compromise.
- Engage system owners during validation to ensure all business-critical components are reviewed.

---

## Manual Steps

### Step 1: Validate Host-Level Cleanliness

#### Malware Scan

- Re-scan all hosts with antivirus and anti-malware tools.
- Tools: Windows Defender, Malwarebytes, ClamAV, ESET

#### Persistence Review

- Confirm no unauthorized scheduled tasks, services, registry keys, startup items.
- Tools: Autoruns (Windows), systemctl + crontab (Linux), KnockKnock (macOS)

#### User Accounts

- Verify no unauthorized accounts exist.
- Validate account permissions and group memberships.
- Tools: PowerShell, getent passwd, dscl

#### Logs Review

- Review system, security, and application logs for signs of malware or attacker activity.
- Tools: Event Viewer (Windows), /var/log/ (Linux), Console.app (macOS)

---

### Step 2: Validate Network-Level Cleanliness

#### Network Connections

- Verify no unexpected open ports or persistent connections.
- Tools: netstat, ss, TCPView (Windows)

#### Firewall and IDS/IPS Logs

- Review logs for any ongoing malicious traffic or indicators of compromise.
- Tools: pfSense, Suricata, Palo Alto Panorama, Security Onion

#### DNS and Proxy

- Validate that all DNS queries are legitimate and no C2 domains are being accessed.
- Tools: Zeek (Security Onion), DNS server logs

---

### Step 3: Validate Enterprise and Domain Integrity

#### Active Directory

- Review Group Policy Objects, user rights assignments, delegated permissions.
- Verify no rogue domain controllers or replication issues.
- Tools: PowerShell, ADUC, BloodHound (for advanced validation)

#### Email and Collaboration Tools

- Review mail flow rules, external forwarding, unauthorized accounts.
- Tools: Exchange Admin Center, M365 Defender, Google Workspace Admin

#### Cloud and SaaS

- Confirm that no unauthorized access tokens or integrations exist.
- Tools: AWS GuardDuty, Azure Security Center, Okta logs

---

### Step 4: Validate Backups and Business Systems

- Confirm backups are intact and tested (ransomware defense).
- Validate business-critical applications are clean and operational.
- Tools: Backup software console, application/system owners coordination

---

### Step 5: Document and Approve

- Create eradication validation report.
- Include findings, validation methods, results, and any follow-up actions.
- Submit report to IR Lead and System Owner for approval to transition to recovery.

---

## Running Script (Windows - Recheck for Suspicious Scheduled Tasks)

```powershell
Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"} | Select-Object TaskName, Actions
```

## Running Script (Linux - Check for Suspicious Services and Cron Jobs)

```bash
systemctl list-units --type=service
crontab -l
```

---

## Dependencies

* Access to all eradicated systems and supporting infrastructure
* Antivirus, EDR, and IR toolkit available for validation scans
* Coordination with IT/system owners
* IR Lead oversight and approval to proceed to recovery

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Antivirus (Defender, Malwarebytes, ClamAV) | Cross-platform | Pre-installed or download | Re-scan hosts |
| Autoruns (Sysinternals) | Windows | Download from Microsoft | Validate autoruns and persistence |
| EDR/XDR Platform (Crowdstrike, SentinelOne) | Enterprise | Deployed agent | Confirm eradication |
| Security Onion / Zeek | Network | Deployed sensor | Confirm network activity is clean |
| PowerShell / bash | Cross-platform | Built-in | Enumerate accounts, services, tasks |

> **Operator Note:** Use layered validation → no single tool will catch everything.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate host-level eradication (malware, persistence, accounts, logs).
- [ ] Validate network-level eradication (traffic, DNS, firewall logs).
- [ ] Validate enterprise-level eradication (AD, email, SaaS/cloud).
- [ ] Validate backups and critical applications are clean.
- [ ] Document validation efforts and findings.
- [ ] Escalate and eradicate if anything is found.
- [ ] Obtain IR Lead approval before recovery begins.

### Best Practices

- Perform validation in a structured and systematic manner.
- Engage cross-functional teams (network, system owners, IT).
- Use clean baseline systems or recovery media to verify suspicious systems.
- Never skip validation → missed eradication steps often result in repeat incidents.

---

## References

[Sysinternals Autoruns](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns)  
[MITRE ATT&CK Persistence Techniques](https://attack.mitre.org/tactics/TA0003/)  
[Zeek Network Security Monitor](https://zeek.org/)  
[Windows Defender Antivirus](https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-in-windows-10)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for validating eradication completion with detailed checklist and tools | Leo |
