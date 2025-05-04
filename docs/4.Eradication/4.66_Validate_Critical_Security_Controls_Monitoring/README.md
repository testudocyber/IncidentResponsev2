# 4.66 Validate Critical Security Controls and Monitoring Are Restored and Operational (EDR, SIEM, Cloud Security Tools)

## Task

Ensure that all critical security controls (EDR, SIEM, Cloud Security Tools) are restored and operational following eradication and recovery activities.

---

## Conditions

Given access to endpoint agents, SIEM platforms, and cloud-native security services across all environments.

---

## Standards

* Validate EDR agent deployment, heartbeat, and policy enforcement.
* Validate SIEM log ingestion from all critical sources.
* Validate cloud-native security tooling is enabled and functioning.
* Validate alerting and response workflows.
* Document validation and remediation actions.

---

## End State

All critical security controls and monitoring platforms are fully restored, tested, and confirmed operational.

---

## Notes

- Systems may lose security controls during reimaging or remediation → revalidation is critical.
- Cloud-native tools must be re-enabled on rebuilt workloads.
- Alerting and response workflows should be tested before closure.

---

## Manual Steps

### Step 1: Validate EDR Agent Coverage and Health

#### Validate Agent Deployment

| Platform | EDR Example |
|----------|-------------|
| Windows/Linux/macOS | CrowdStrike, SentinelOne, Defender ATP |

##### CrowdStrike Example

```bash
sudo /opt/CrowdStrike/falconctl -g --aid
```

Look for:

- Agent ID assigned
- Last check-in time

##### Microsoft Defender ATP

Portal → Device Inventory → Filter → Agent Status → Active/Inactive

Look for:

- Devices marked as Active
- No devices reporting stale

> **Operator Note:** Contact IT to redeploy agents on missing systems.

---

#### Validate Policy Enforcement

- Run EICAR or benign test → confirm detection
- Validate protection policies are applied via console

> **Operator Note:** Disabled or outdated policies must be corrected immediately.

---

### Step 2: Validate SIEM Log Ingestion

| SIEM | Method |
|------|--------|
| Splunk | Search `index=* earliest=-5m` |
| Sentinel | Workbook or Log Analytics → Query last 5 min |
| Elastic | Discover → Time range last 5 min |

Look for:

- Recent logs from:
  - Domain Controllers
  - File Servers
  - Security appliances
  - Endpoints

> **Operator Note:** Any major log source not appearing → escalate to IT/logging engineer.

---

#### Validate Alerting

- Generate test events (login failure, fake malware detection)
- Confirm SIEM rules trigger alerts

Example in Splunk:

```spl
index=wineventlog EventCode=4625
```

> **Operator Note:** Coordinate with SOC to validate end-to-end alert pipeline.

---

### Step 3: Validate Cloud Security Tools (AWS, Azure, GCP)

#### AWS → GuardDuty and Security Hub

```bash
aws guardduty get-detector --detector-id DETECTOR_ID
```

Look for:

- Enabled = True

Check Console:

- GuardDuty findings → confirm ingestion
- Security Hub → confirm no disabled controls

> **Operator Note:** If disabled → enable GuardDuty and Security Hub.

---

#### Azure → Defender for Cloud

```powershell
Get-AzSecurityPricing
```

Look for:

- Pricing tier = Standard (Defender enabled)

Check Portal:

- Defender recommendations → confirm telemetry
- Sentinel → confirm ingestion

> **Operator Note:** Ensure Defender for Servers and Defender for Identity are enabled.

---

#### GCP → Security Command Center + Cloud Logging

```bash
gcloud scc findings list
gcloud logging read "timestamp >= \"2025-05-01T00:00:00Z\""
```

Look for:

- Recent findings → active security scanning
- Logs from compute/storage/network

> **Operator Note:** Review SCC → ensure not in "Unconfigured" state.

---

### Step 4: Validate Cross-System Alert and Response Workflow

- Simulate security event across endpoint or cloud (benign).
- Validate alert flows:
  - Detected → SIEM → SOC notification
  - Detected → Cloud security platform → Alerting channel (email, Slack, PagerDuty)

> **Operator Note:** Validate who gets notified → SOC, IR team, management.

---

### Step 5: Validate and Document

- Validate no missing agents, log gaps, or disabled controls.
- Capture screenshots or log exports of healthy status.
- Document validation actions, results, and any remediations performed.

---

## Dependencies

* Access to EDR management consoles
* Access to SIEM search consoles
* Cloud security platform administrative access
* Ability to trigger test/benign security events

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| EDR Consoles | Cross-platform | Native | Validate agent status |
| SIEM (Splunk, Sentinel, Elastic) | Cross-platform | Native | Validate log ingestion |
| AWS CLI + GuardDuty + Security Hub | AWS | Package manager | Validate cloud security |
| Azure CLI + Defender for Cloud + Sentinel | Azure | Package manager | Validate cloud security |
| GCP CLI + Security Command Center | GCP | Package manager | Validate cloud security |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate endpoint EDR agent deployment and policy enforcement.
- [ ] Validate SIEM log ingestion for critical log sources.
- [ ] Validate cloud-native security tool status (AWS, Azure, GCP).
- [ ] Validate alerting pipeline → simulate detection and confirm notification.
- [ ] Document validation results and escalate gaps.

### Best Practices

- Enable heartbeat monitoring on EDR to catch dropped agents.
- Integrate cloud security tool alerts into SIEM.
- Maintain source-to-SIEM mapping → identify ingestion gaps fast.
- Perform quarterly security control validation exercises.

---

## References

- [AWS GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
- [Azure Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction)
- [GCP Security Command Center](https://cloud.google.com/security-command-center)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with operator-focused validation of EDR, SIEM, and cloud-native security controls | Leo |
