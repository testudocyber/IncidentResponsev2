# Document Containment Actions

## Task Document All Containment Actions Taken During Incident Response

## Conditions

Given access to incident response records, the operator will ensure all containment actions are documented in detail. Documentation should reflect who performed the action, when it occurred, what was done, and why it was necessary.

> **Operator Note:** Accurate documentation of containment activities is critical for post-incident analysis, lessons learned, recovery coordination, and legal or regulatory reporting requirements.

## Standards

* Team member records every containment action taken, linked to impacted hosts or systems.  
* Team member records the justification and approval (if required) for each action.  
* Team member ensures timestamped records of who performed each action.  
* Team member stores documentation in approved IR management platform or file repository.  
* Team member ensures documentation is reviewed and available for post-incident activities.

## End State

All containment actions are fully and accurately documented and accessible to authorized personnel for follow-up actions.

---

## Notes

- Document actions as soon as possible after execution to avoid omissions.  
- Use a standard format to enable easy review and analysis.  
- Documentation should be detailed enough for another operator to understand what was done and why.

---

## Manual Steps

### Step 1: Define the Documentation Format (Preferred Template)

| Date/Time | Operator | Action Taken | Host/System | Justification | Approval (If Required) |
|-----------|----------|--------------|-------------|---------------|------------------------|
| 2025-05-02 09:13 | L. Jones | Disabled malicious service "strsvc1" | Workstation-014 | Service identified running from AppData, persistence attempt | Approved by IR Lead |

> **Operator Note:** Use this format to track every action taken during containment.

---

### Step 2: Record Host-Based Actions

Examples of host-level containment actions to document:

- Disabled local administrator account on HostX  
- Renamed and disabled scheduled task on HostY  
- Removed unauthorized software (e.g., adfind.exe) from HostZ  
- Blocked external traffic from HostW via firewall

---

### Step 3: Record Network-Level Actions

Examples of network containment actions:

- Applied ACL on FirewallX to block external comms from Subnet 10.10.10.0/24  
- Sinkholed DNS queries for malicious domain `abcxyz.bad`  
- Disabled VPN access for user “suspiciousUser”

---

### Step 4: Record Account and Identity Actions

Examples of identity containment actions:

- Disabled user account `jsmith` due to confirmed credential compromise  
- Removed admin rights from `hrapp` service account temporarily

---

### Step 5: Record Validation and Review

- Validated service disablement on all targeted hosts  
- Confirmed ACL applied and no external comms observed  
- Conducted team review and documented containment completion

---

## Running Script (Windows Example to Export Containment Log)

```powershell
$containmentLog = @()

$containmentLog += [PSCustomObject]@{
    DateTime = Get-Date
    Operator = "Leo"
    Action = "Disabled suspicious service strsvc1"
    Host = "Workstation-014"
    Justification = "AppData execution path - possible persistence"
    Approval = "IR Lead"
}

$containmentLog | Export-Csv -Path .\ContainmentLog.csv -NoTypeInformation
```

> **Operator Note:** Customize and use PowerShell to automate log capture if preferred.

---

## Dependencies

* Approved documentation location (SharePoint, ticketing system, file share, etc.)  
* Access to IR actions and relevant logs  
* Team coordination (approval and review steps)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| Markdown (.md) or Text files | Cross-platform | Quick and portable log format |
| Ticketing Systems (Jira, ServiceNow) | Enterprise | Official incident recordkeeping |
| SharePoint / OneDrive | Enterprise | Shared team documentation |
| OneNote / Word | Cross-platform | IR team notes and logbooks |
| PowerShell Export / Scripts | Windows | Automated log creation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Record all containment actions as soon as possible.
- [ ] Use consistent format with date/time, operator, action, host, justification, and approval.
- [ ] Store logs in secure and approved location.
- [ ] Conduct review with IR lead or incident commander.
- [ ] Ensure documentation is complete before moving to eradication phase.

### Best Practices

- Assign a scribe or dedicated recorder during large-scale incidents.
- Use timestamps and operator initials for traceability.
- Validate logs against actions recorded in IR ticketing system or command logs.
- Keep documentation professional and objective (avoid subjective language).

---

## References

[NIST SP 800-61 Rev. 2 - Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
[US-CERT Incident Handling Guidelines](https://www.cisa.gov/resources-tools/resources/incident-handling-overview)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for documenting containment actions with format, checklist, and best practices | Leo |
