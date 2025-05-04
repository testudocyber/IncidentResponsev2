# 4.61 Validate All Endpoints Are Removed from Monitoring Gaps (SIEM, EDR, Logging)

## Task

Ensure that all endpoints (workstations, servers, cloud VMs, and mobile endpoints) are properly onboarded into logging and monitoring platforms (SIEM, EDR, and Cloud-native services). Identify and remediate monitoring gaps where endpoints are missing or inactive.

---

## Conditions

Given access to SIEM, EDR, and Cloud-native logging platforms.

---

## Standards

* Enumerate all endpoints across environments.
* Validate each endpoint has active EDR and SIEM/logging agent coverage.
* Identify and close monitoring gaps.
* Document all actions taken.

---

## End State

All endpoints are monitored by EDR and/or SIEM and any monitoring gaps are resolved or escalated.

---

## Notes

- Endpoints missing from SIEM and EDR create serious blind spots during incident response.
- Newly provisioned or legacy systems are often overlooked.
- Cloud workloads (VMs, containers) may spin up/down → monitoring must be dynamic.

---

## Manual Steps

### Step 1: Enumerate Active Endpoints from Source Systems

#### Active Directory (for on-prem endpoints)

```powershell
Get-ADComputer -Filter * | Select-Object Name, LastLogonDate
```

Look for:

- Enabled computer accounts
- Stale systems still active

#### Cloud Platforms (AWS, Azure, GCP)

| Cloud | Command/Console | Look For |
|-------|-----------------|----------|
| AWS EC2 | `aws ec2 describe-instances` | Running instances |
| Azure VM | `Get-AzVM` | Powered on VMs |
| GCP Compute Engine | `gcloud compute instances list` | Running instances |

> **Operator Note:** Ensure cloud VMs are compared against monitoring and EDR coverage.

---

### Step 2: Enumerate Endpoints from EDR Console

#### Common EDR platforms:

| EDR | Query/View | What to Look For |
|-----|------------|------------------|
| CrowdStrike | Hosts → Host Management | Hosts missing heartbeat or never checked in |
| Microsoft Defender ATP | Devices → Active/Inactive | Inactive or unmanaged devices |
| SentinelOne | Devices → All | Devices offline or with stale check-in |
| Others | Equivalent views | Check for offline/stale hosts |

> **Operator Note:** Stale endpoints (30+ days) may be compromised or improperly offboarded.

---

### Step 3: Enumerate Endpoints from SIEM / Log Collection

#### Examples:

| SIEM | Method | What to Look For |
|------|--------|------------------|
| Splunk | `| metadata type=hosts` | Missing known hosts |
| Microsoft Sentinel | Workbooks/Logs → Heartbeat | Inactive/never reported hosts |
| Elastic | Discover → Agent ID check | Missing expected agents |

> **Operator Note:** Cross-reference SIEM asset list against AD and EDR inventories.

---

### Step 4: Compare and Identify Monitoring Gaps

- Merge all collected lists → AD, EDR, SIEM, Cloud platforms.
- Identify systems not appearing in EDR or SIEM.
- Investigate reasons → offline, decommissioned, not onboarded, rogue.

> **Operator Note:** Onboard missing endpoints or escalate rogue/unaccounted endpoints immediately.

---

### Step 5: Onboard Missing Endpoints

#### Local Onboarding (Windows/Linux/macOS)

- Install EDR agent (CrowdStrike, Defender ATP, etc)
- Install SIEM log collection agent (Splunk Universal Forwarder, FluentD, NXLog)

#### Cloud Onboarding

| Platform | Native Logging |
|----------|----------------|
| AWS | Enable GuardDuty, CloudWatch Agent |
| Azure | Azure Monitor/Defender Extension |
| GCP | Enable Cloud Logging Agent |

> **Operator Note:** Automation (e.g. Terraform, CloudFormation) should enforce automatic onboarding.

---

### Step 6: Validate and Document

- Validate endpoint appears in EDR and SIEM.
- Validate cloud-native logs are ingested.
- Document actions, updates to onboarding procedures, and owner sign-offs.

---

## Dependencies

* Access to SIEM, EDR, Cloud consoles
* Accurate endpoint inventory
* Ability to deploy and validate agent installation

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell | Windows | Built-in | AD and agent validation |
| AWS CLI | Cross-platform | Package manager | EC2 inventory |
| Azure CLI / PowerShell | Cross-platform | Package manager | VM inventory |
| GCloud CLI | Cross-platform | Package manager | Compute inventory |
| Splunk / Sentinel / Elastic | Web/Console | Native | SIEM coverage validation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate endpoints from AD, cloud, and local infrastructure.
- [ ] Enumerate endpoints from EDR platforms.
- [ ] Enumerate endpoints from SIEM platforms.
- [ ] Compare → identify gaps.
- [ ] Onboard missing endpoints and remove stale/decommissioned entries.
- [ ] Validate and document coverage.

### Best Practices

- Review onboarding automation regularly → avoid drift.
- Flag stale/inactive hosts → validate decommission or compromise.
- Enforce agent health monitoring → alerts for missing heartbeat.
- Incorporate cloud auto-scaling logic → ensure dynamic assets are auto-onboarded.

---

## References

- [CrowdStrike Falcon Host Management](https://www.crowdstrike.com)
- [Microsoft Sentinel Agent Monitoring](https://learn.microsoft.com/en-us/azure/sentinel/)
- [AWS GuardDuty](https://aws.amazon.com/guardduty/)
- [Azure Defender for Servers](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-servers-introduction)
- [GCP Cloud Logging](https://cloud.google.com/logging)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with operator-focused SIEM/EDR/Cloud endpoint validation workflow | Leo |
