# 4.13 Detect SMB using Security Onion (SO)

## Task

Identify SMB traffic and monitor SMB-based activity using Security Onion to detect unauthorized SMB file transfers or lateral movement techniques (such as Cobalt Strike SMB P2P).

---

## Conditions

While using Security Onion Alerts and Hunt dashboards, identify SMB traffic.

---

## Standards

* Login into Security Onion.
* Select **Alerts** on the left panel.
* Ensure Alerts are properly configured:
  * Group: `rule.name`
  * Group: `event.module`
  * Group: `event.severity_label`
* Set timeframe for your scan period:
  * Click the underlined area to the left of the refresh button.
  * If listed as calendar and hours → click the calendar icon and select desired timeframe (e.g., Last 1 Day or Last 6 hours).
* Click on column title `event.severity_label` to sort alerts by severity (High → Low).
* Search for SMB alert signatures (i.e. `ET POLICY SMB Executable File Transfer`)
* Left click on the SMB alert → scroll down → **Actions** → **Hunt**
* Click the **Hunt** tab on the left side of the dashboard.
* Ensure timeframe matches the alert.
* In the search bar:
  * Delete everything in quotations → leave only `SMB`
  * Confirm `Group: event.module` and `Group: event.dataset` are still active
  * If not, click down arrow next to search bar → select `| groupby event.module event.dataset`
* Click on **Hunt**.
* Scroll down to **Events** → sort by `rule.name` and/or `event.severity_label`.

You may see results like:

```
ET TROJAN CobaltStrike SMB P2P Default Msagent Named Pipe Interaction
A Network Trojan was detected → High
```

---

## End State

* SMB traffic is identified and monitored for unauthorized file transfers or suspicious lateral movement activities.

---

## Notes

Security Onion is able to detect SMB-based threats using Suricata rules and Hunt investigations. Typical examples include:

- Unauthorized file transfer attempts
- Named pipe interactions
- Cobalt Strike SMB beacon activity

---

## Manual Steps

* See above Security Onion Alerts and Hunt steps.

---

## Expanded Detection Methods (Operator Enhancement)

### Zeek SMB Logs (Optional)

Zeek (formerly Bro) monitors SMB traffic and logs into SMB log files.

```bash
cat /nsm/bro/logs/current/smb_files.log
```

Look for:

- File transfer events
- Access attempts
- Unexpected usernames

Example fields:

```
ts | uid | id.orig_h | id.resp_h | command | path | name
```

#### Search for executables

```bash
grep ".exe" /nsm/bro/logs/current/smb_files.log
```

---

### Cloud and Hybrid Detection (Optional)

If network extends into VPN or Cloud-connected environments:

#### AWS

- VPC Flow Logs will show port 445 connections → suspicious if not expected

```bash
aws logs filter-log-events --log-group-name VPCFlowLogs --filter-pattern "port 445"
```

#### Azure

- NSG Flow Logs + Azure Defender → SMB protocol detection (Unusual Inbound SMB Traffic alert)

#### GCP

- VPC Flow Logs + Security Command Center → Look for TCP/445 traffic alerts

> **Operator Note:** SMB traffic between cloud environments is often unexpected and should be reviewed immediately.

---

## Running Script

N/A → Can be scripted if frequent Zeek log review is required.

---

## Dependencies

* Security Onion running and properly configured
* Suricata / Zeek enabled in Security Onion
* Alerts and Hunt dashboard access

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Security Onion (Alerts + Hunt + Zeek SMB logs) | Network | Native | SMB traffic detection and analysis |
| OSSEC / Wazuh | Cross-platform | Package Manager | Detect SMB-based file access or login attempts |
| Cloud native flow logs + alerts (AWS, Azure, GCP) | Cloud | Native | Detect SMB traffic across hybrid cloud |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate Alerts view and hunt for SMB activity
- [ ] Detect and investigate SMB alerts (ET POLICY, ET TROJAN CobaltStrike SMB)
- [ ] Use Hunt to drill down and analyze SMB patterns
- [ ] Review Zeek SMB logs → look for file transfers and executable sharing
- [ ] Validate cloud environments → review VPC/NSG Flow Logs for unexpected SMB traffic
- [ ] Confirm with asset owners if detected SMB activity is authorized
- [ ] Document all findings and escalate unauthorized or suspicious activity

### Best Practices

- Monitor both alerts and Hunt for SMB — attackers may generate low/no alerts but Hunt will show patterns.
- Zeek SMB logs provide file-level insights → critical for lateral movement detection.
- Block SMB traffic at perimeter or cloud security groups unless explicitly required.
- Enable slow scan and anomaly-based SMB detection in Suricata / SO rules.

---

## References

* [Security Onion Alerts](https://docs.securityonion.net/en/16.04/alerts.html)
* [Security Onion Hunt](https://github.com/Security-Onion-Solutions/securityonion-docs/blob/2.2/hunt.rst)
* [Zeek SMB Protocol Analyzer](https://docs.zeek.org/en/current/script-reference/protocols/smb.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained and expanded with multi-method SMB detection + operator checklist | Leo |
