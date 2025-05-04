# 4.51 Perform Recovery Monitoring and Validation

## Task Conduct Active Monitoring and Validation During and After Recovery to Ensure Threats Have Been Eradicated

## Conditions

Once systems are being restored and users begin returning to normal operations, the operator will execute recovery monitoring and validation procedures to confirm there are no remaining threats or signs of compromise in the environment.

> **Operator Note:** Recovery is the period where risks remain high → attacker tools missed during eradication or latent persistence mechanisms may attempt to resume. Active monitoring during this phase is critical.

## Standards

* Team member establishes active monitoring for restored systems.
* Team member validates restored systems for security and performance baselines.
* Team member monitors network, endpoints, and enterprise resources for abnormal activity.
* Team member documents monitoring actions, observations, and any anomalies detected.
* Team member escalates immediately if indicators of compromise (IOCs) or suspicious behavior are observed.

## End State

Recovery period completes without detection of malicious activity, and validation demonstrates that systems and users have returned to normal, secure operations.

---

## Notes

- Monitoring and validation should continue through the initial weeks after recovery.
- Recovery validation includes performance, functionality, AND security verification.
- Any detected anomalies must be treated as potential indicators of ongoing compromise.

---

## Manual Steps

### Step 1: Establish Monitoring Coverage

#### Ensure all restored systems are:

- Online and enrolled in EDR/XDR solution
- Actively reporting telemetry (no gaps in coverage)
- Under antivirus and log collection coverage

> **Operator Note:** Any "silent" systems not reporting must be investigated immediately.

---

### Step 2: Conduct Host-Level Validation

#### Windows/Linux/macOS

- Verify no unexpected processes running
- Verify no unauthorized startup entries or tasks
- Validate user accounts and permissions

#### Tools

- EDR/XDR → Crowdstrike, SentinelOne, Defender ATP
- Sysinternals (Windows) → Process Explorer, Autoruns
- systemctl / launchctl (Linux/macOS) → Active service enumeration

---

### Step 3: Conduct Network-Level Monitoring

- Validate all restored hosts communicate only on expected ports and protocols.
- Detect unusual traffic patterns (C2, port scanning, brute forcing).
- Monitor DNS requests for suspicious domains.

#### Tools

- Zeek (Security Onion) → Network flow analysis
- IDS/IPS → Snort, Suricata
- Firewall/Proxy Logs → Unexpected destinations or high volumes

---

### Step 4: Conduct User and Enterprise Monitoring

- Confirm no abnormal user authentication or access attempts.
- Monitor email, file shares, and privileged accounts.
- Validate business processes resume securely.

#### Tools

- SIEM → Splunk, Sentinel, ELK
- Email Security Gateway → Proofpoint, M365 Defender
- AD Logs → Authentication and authorization monitoring

---

### Step 5: Validate System Performance and Stability

- Verify systems run without performance degradation (possible malware or crypto mining remnants).
- Validate system/app logs show no errors related to recovery process.
- Confirm backups resume as normal and are not impacted.

> **Operator Note:** Some malware reactivates after restart — post-recovery performance issues can be an IOC.

---

### Step 6: Document Findings and Validate Completion

- Record monitoring coverage, checks performed, and validation results.
- If no malicious activity is detected after monitoring period (typically 1-2 weeks post recovery), document and mark recovery validation as complete.

> **Operator Note:** Always retain recovery monitoring documentation as part of incident record for Lessons Learned.

---

## Running Script (Windows - Verify EDR and AV Status)

```powershell
Get-MpComputerStatus
```

#### Linux/macOS (Check Running Services)

```bash
ps aux
systemctl list-units --type=service
```

#### Network (Zeek - Check Connections)

```bash
cat conn.log | grep -v "normal_ports"
```

---

## Dependencies

* Full enrollment of recovered systems into security monitoring (EDR/AV/SIEM)
* Coordination with system owners and IT ops
* Predefined IOC watchlists from IR team
* On-call IR and security staff for escalation

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| EDR/XDR Platforms (Crowdstrike, SentinelOne, Defender ATP) | Cross-platform | Enterprise deployment | Endpoint and host monitoring |
| SIEM (Splunk, Sentinel, ELK) | Cross-platform | Enterprise deployment | Correlate enterprise security events |
| Security Onion (Zeek, Suricata) | Network | Sensor deployment | Network traffic analysis |
| Sysinternals (Process Explorer, Autoruns) | Windows | Download | Validate host process integrity and startup persistence |

> **Operator Note:** Automated and manual validation combined gives the best coverage.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm all restored systems are enrolled in security monitoring.
- [ ] Validate hosts for clean running state and performance.
- [ ] Monitor network activity for anomalies.
- [ ] Monitor enterprise authentication and privileged accounts.
- [ ] Document validation activities and monitoring coverage.
- [ ] Escalate any suspicious detections immediately.
- [ ] Finalize recovery validation after monitoring period concludes.

### Best Practices

- Start monitoring immediately → do not wait for users to return.
- Monitor for at least 1-2 weeks post-recovery for latent threats.
- Correlate across host, network, and user activity for anomaly detection.
- Retain all records for compliance and post-incident review.

---

## References

[Security Onion - Network Security Monitoring](https://securityonionsolutions.com/)  
[MITRE ATT&CK - Persistence](https://attack.mitre.org/tactics/TA0003/)  
[Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for recovery monitoring and validation with technical procedures and checklist | Leo |

