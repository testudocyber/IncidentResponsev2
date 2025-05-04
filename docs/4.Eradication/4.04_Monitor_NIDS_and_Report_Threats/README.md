# 4.04 Monitor NIDS and Report Threats

## Task

Monitor Network Intrusion Detection System (NIDS) and Report Threats

## Conditions

Given a fully installed NIDS, e.g. Security Onion, connected to a span port or tap that monitors key terrain, an account for the NIDS, a known network topology and list of computers and provided services, and access to a second computer with Internet access for investigation.

---

## Standards

* Scan anomalies and irregularities within the interface to find possible malicious activity.
* Investigate suspicious anomalies to make a reasonable determination the NIDS information represents malicious activity.
* Use other tools (e.g. Zeek, Wireshark, Snort/Suricata) to validate the incident and rule out false positives.
* Report the incident to the intelligence team and the team leader.
* Collect all information relevant to the incident for a detailed report.

---

## End State

After investigating NIDS reports and malicious activity is determined, the intelligence team and team leader are informed. The team leader then informs the network owner of the incident and begins the response process.

---

## Notes

- Continuous NIDS monitoring is essential during recovery and eradication phases.
- Focus on anomalies and patterns — single alerts are often benign, but combinations may be serious.

---

## Manual Steps

### Step 1: Login to the NIDS Interface

- Access Security Onion or other NIDS solution.
- Use secure login credentials.

#### Example (Security Onion web UI)
```
https://[security-onion-IP]/app/dashboards
```

- Verify that dashboards, alerts, and data are updating properly.

---

### Step 2: Scan for Suspicious Activity

- Review dashboards for spikes or anomalies (connection attempts, data exfiltration, lateral movement).
- Investigate alerts for:
  - Known malware signatures
  - Excessive failed logins
  - Connections to rare or unexpected destinations
  - Abnormal ports and protocols

#### Security Onion (Suricata Alert View)
```
cat /nsm/suricata/logs/fast.log
```

#### Zeek Example
```
cat /nsm/bro/logs/current/conn.log | grep -i "malware"
```

---

### Step 3: Investigate Suspicious Events

- Cross-check with other tools:
  - Zeek → Connection logs, DNS queries
  - Wireshark → PCAP analysis
  - EDR → Host behavior context
  - Threat Intelligence → IP/domain reputation

> **Operator Note:** Determine if anomalous behavior is a false positive, benign anomaly, or true malicious event.

---

### Step 4: Report Confirmed or Likely Malicious Activity

- Alert:
  - Intelligence Team
  - Incident Response Team Leader
  - Network Owner (via chain of command)

#### Recommended Reporting Channels
- Create ticket in incident management platform.
- Email + phone escalation.
- Log entry into IR event tracker.

> **Operator Note:** Use tactical language in alerts: who, what, when, where, why (suspected).

---

### Step 5: Collect and Document Incident Information

- Save NIDS logs (Suricata, Zeek) related to the incident.
- Capture screenshots of alert dashboards.
- Record investigation steps taken and outcome.
- Store in Incident Response shared location for handoff.

---

## Running Script

```bash
# Example: Search Zeek for all connections to known bad IP
grep "x.x.x.x" /nsm/bro/logs/current/conn.log
```

> **Operator Note:** Replace "x.x.x.x" with IOC or suspicious IP found.

---

## Dependencies

* Security Onion (or equivalent)
  * Zeek (formerly Bro)
  * Suricata / Snort
  * SGUIL / Kibana
* Network Topology and Key Terrain identification
* IR Procedures and Escalation Paths

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Splunk | Cross-platform | Enterprise or local install | Log aggregation and IOC correlation |
| Wireshark | Cross-platform | Install via package manager | Deep packet inspection |
| EDR Platform (CrowdStrike, SentinelOne) | Enterprise | Installed agent | Host behavior and IOC correlation |

> **Operator Note:** Use layered detection and investigation to improve confidence before escalating.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Log into NIDS platform and verify alerting is operational.
- [ ] Review dashboards and alerts for suspicious behavior.
- [ ] Investigate each suspicious alert using Zeek, Suricata, Wireshark.
- [ ] Determine whether alerts are false positives or true positives.
- [ ] Report true positive incidents to Intel and Team Lead.
- [ ] Collect artifacts and document investigation steps.
- [ ] Prepare handoff documentation for follow-on IR actions.

### Best Practices

- Set threshold for reporting → balance between signal and noise.
- Ensure all IR team members can access NIDS and supporting tools.
- Regularly review and tune alert thresholds and rulesets.
- Monitor for evolving threats and new attack techniques.

---

## References

- [Security Onion Solutions](https://securityonionsolutions.com/)
- [Zeek (formerly Bro)](https://zeek.org/)
- [Suricata IDS](https://suricata.io/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Corrected and expanded operator version incorporating original standards and detailed investigation steps | Leo |
