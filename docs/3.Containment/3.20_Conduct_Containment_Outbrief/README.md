# Conduct Containment Outbrief

## Task Conduct a Formal Containment Outbrief to Transition to Eradication

## Conditions

Given completed containment activities and coordination with IR stakeholders, the operator will lead or participate in an outbrief meeting to summarize actions, validate containment success, and enable transition to eradication and recovery phases.

> **Operator Note:** The containment outbrief ensures clarity and alignment between incident response team, IT operations, business stakeholders, and enclave/system owners. This step officially closes containment and prevents premature movement to next phases.

## Standards

* Team member prepares containment summary including actions, affected systems, and containment status.  
* Team member schedules and invites IR stakeholders to outbrief meeting.  
* Team member presents containment summary and validates containment success with participants.  
* Team member records meeting decisions and any action items.  
* Team member distributes outbrief notes and updates IR documentation.

## End State

Containment actions and their effectiveness have been validated and communicated to all stakeholders, and agreement has been reached to move to eradication phase.

---

## Notes

- The containment outbrief is both technical and operational → audience may include IT, business, and security.  
- The outbrief must confirm containment success to avoid premature progression.  
- All containment-related risks, exceptions, or challenges should be transparently discussed.

---

## Manual Steps

### Step 1: Prepare Containment Summary Document

Recommended format:

| Action | System(s) | Date/Time | Operator | Status | Notes |
|--------|-----------|-----------|----------|--------|-------|
| Disabled malicious service | Workstation-004 | 2025-05-02 09:00 | L. Jones | Completed | No recurrence observed |
| Blocked C2 traffic via firewall ACL | Network Firewall | 2025-05-02 09:30 | K. Smith | Completed | No outbound traffic seen in past 24h |

Also include:

- Overall number of impacted systems
- Description of containment methods used (network, host, user)
- Residual risk or known limitations
- Summary of validation steps completed

---

### Step 2: Schedule and Conduct Outbrief Meeting

**Recommended attendees:**

- Incident Response Lead
- Incident Responders
- Enclave or System Owner
- IT Operations Lead
- Business Stakeholders (if needed)

**Recommended agenda:**

1. Overview of incident and containment objectives
2. Summary of containment actions
3. Validation of containment success
4. Known gaps or residual risks
5. Transition plan to eradication and recovery
6. Open discussion and Q&A
7. Approval to proceed

---

### Step 3: Record Outbrief Decisions

Capture key points and decisions:

```plaintext
Containment Summary: Approved
Eradication Phase Start: Approved to begin 2025-05-02 14:00
Risks Noted: Some user accounts remain disabled pending investigation
Follow-Up Actions: IT Operations to monitor overnight and report anomalies
```

> **Operator Note:** Distribute notes to all participants after the outbrief.

---

## Running Script (PowerShell Example for Containment Summary Generation)

```powershell
$containmentSummary = @()

$containmentSummary += [PSCustomObject]@{
    Action = "Disabled malicious service"
    System = "Workstation-004"
    DateTime = Get-Date
    Operator = "L. Jones"
    Status = "Completed"
    Notes = "No recurrence observed"
}

$containmentSummary | Export-Csv -Path .\ContainmentOutbrief.csv -NoTypeInformation
```

> **Operator Note:** Use PowerShell to generate containment summary table quickly from logs or notes.

---

## Dependencies

* Incident Response documentation  
* IR team leader and decision makers available  
* Collaboration and scheduling tools (email, calendar, Teams, Zoom, etc.)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| Markdown, Word, or PowerPoint | Cross-platform | Prepare containment summary document |
| Ticketing system or incident management platform | Enterprise | Record and distribute outbrief notes |
| Email or Collaboration Platform | Cross-platform | Send invites, notes, and decisions |
| PowerShell | Windows | Generate containment action summary from logs |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Prepare containment action summary document.
- [ ] Schedule outbrief meeting with key stakeholders.
- [ ] Present containment actions, validation, and residual risks.
- [ ] Record meeting notes and decisions.
- [ ] Distribute notes and update incident response documentation.

### Best Practices

- Be transparent about containment success and limitations.
- Gain formal approval before proceeding to eradication.
- Use plain language where necessary for non-technical stakeholders.
- Align containment completion with overall IR plan milestones.

---

## References

[NIST SP 800-61 Rev. 2 - Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
[Incident Response Playbook Templates - CISA](https://www.cisa.gov/resources-tools/resources/incident-response-playbooks)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for conducting containment outbrief, agenda, checklist, and best practices | Leo |
