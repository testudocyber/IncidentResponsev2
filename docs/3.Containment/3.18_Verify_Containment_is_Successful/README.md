# Verify Containment is Successful

## Task Verify That Containment Actions Were Fully Successful and No Adversary Presence Remains

## Conditions

Given containment actions (account disablement, network isolation, service disabling, removal of malicious tools) have been completed, the operator will validate that impacted systems and networks no longer exhibit signs of compromise or unauthorized access.

> **Operator Note:** Verification of containment is critical before proceeding to eradication and recovery. Failure to validate containment may result in reinfection or attacker re-entry.

## Standards

* Team member confirms previously compromised hosts are isolated or secured.  
* Team member validates that malicious services, scheduled tasks, unauthorized accounts and software have been removed or disabled.  
* Team member reviews network traffic to confirm C2 and exfiltration have ceased.  
* Team member ensures no new suspicious indicators are present post-containment.  
* Team member coordinates containment verification sign-off with IR lead or enclave owner.

## End State

Containment is validated through host, network, and user behavior review. Adversary access has been removed or blocked, and the environment is stabilized for eradication and recovery phases.

---

## Notes

- Verification must be multi-layered → do not trust single data source validation.  
- Use endpoint, network, and centralized logging to confirm environment state.  
- Coordination with SOC/IR team is critical for holistic validation.

---

## Manual Steps

### Step 1: Validate Host-Based Controls

#### Review for active attacker tools and malware

```powershell
Get-Process
Get-ScheduledTask
Get-Service
```

> **Operator Note:** Verify no malicious processes, tasks, or services remain enabled or running.

#### Review unauthorized accounts

```powershell
net user
Get-LocalUser
```

#### Check for open network connections

```powershell
netstat -ano
```

#### Confirm firewall and policy enforcement

```powershell
Get-NetFirewallProfile | Format-Table Name, Enabled
```

---

### Step 2: Validate Network-Level Controls

#### IDS/IPS / Firewall

- Confirm rules blocking external communications are still active.
- Validate no C2 traffic or anomalies are present.

#### DNS Logging

- Review for queries to known malicious domains or external IPs.

#### Network Segmentation

- Validate isolation of impacted systems remains in place.

---

### Step 3: Validate Enterprise Controls

#### Central Logging/SIEM

- Search for recurring alerts related to:
  - Unauthorized logons
  - Malware detections
  - Command and control signatures

#### Endpoint Detection and Response (EDR)

- Verify hosts are clean and not running malicious processes.
- Validate alerts have stopped post-containment.

#### Review Active Directory

- Validate that GPOs have applied successfully.
- Confirm no new admin accounts exist.

---

### Step 4: Validate User Behavior and Access

- Ensure only IR personnel are accessing impacted systems.
- Confirm no unusual logon patterns are detected.
- Review shared drives and email for signs of data theft or new phishing attempts.

---

## Running Script (Windows Bulk Containment Verification)

```powershell
$computers = Get-ADComputer -Filter * -SearchBase "OU=Workstations,DC=example,DC=com"

foreach ($computer in $computers) {
    Invoke-Command -ComputerName $computer.Name -ScriptBlock {
        Get-Service | Where-Object { $_.Status -eq "Running" }
        Get-ScheduledTask | Where-Object { $_.State -eq "Running" }
        Get-LocalUser
    }
}
```

> **Operator Note:** Useful for verifying across multiple endpoints quickly.

---

## Dependencies

* Administrator access (Windows, Linux, AD)  
* Access to central SIEM, EDR, or logging tools  
* Network traffic monitoring tools (Firewall, IDS/IPS)  
* Endpoint host access (RDP, SSH, or EDR agent)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell / Task Manager | Windows | Validate local host state |
| Netstat, Get-NetTCPConnection | Windows/Linux | Network connections |
| EDR Tools (Defender ATP, CrowdStrike, SentinelOne) | Cross-platform | Validate endpoint state |
| SIEM (Splunk, Sentinel, Elastic) | Cross-platform | Validate no new alerts |
| IDS/IPS, Firewall Logs | Network | Validate no malicious activity |
| DNS Logs | Network | Validate no malicious outbound communication |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate host-level controls → no malicious processes, accounts, services, tasks.
- [ ] Validate network controls → no external communication or beaconing.
- [ ] Validate enterprise controls → no SIEM alerts or EDR detections.
- [ ] Validate user access → only authorized IR personnel accessing.
- [ ] Coordinate with IR team lead and enclave owner for formal containment validation.
- [ ] Document containment verification in IR record.

### Best Practices

- Perform multi-layer validation → host, network, enterprise, user.
- Use automated scripts for broad validation across environment.
- Maintain containment state until eradication and recovery preparation is complete.
- Validate over time → monitor for delayed attacker activity or timers.

---

## References

[Microsoft Defender ATP Hunting Queries](https://learn.microsoft.com/en-us/microsoft-365/security/defender/advanced-hunting-overview)  
[NIST SP 800-61 Rev. 2 - Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for verifying containment success at host, network, and enterprise levels | Leo |
