# Receive, Plan, and Communicate Mission Intent Task

## Conditions

- IR Team requested to support mission partner’s critical information systems

## Standards

- Use MDMP to develop Course of Action (COA)
- Address non-standard considerations:
  - Duty Status (SAD, Title 10/32)
  - Cyber tool availability/limitations
  - Escalation procedures
  - OPSEC requirements
  - Legal support

## Procedural Steps (Checklist)

### Use MDMP to Develop COA

- [ ] Define mission and objectives.
- [ ] Gather intelligence on environment and threat.
- [ ] Develop multiple COAs considering risk and constraints.
- [ ] Select best COA and document rationale.

### Address Duty Status

- [ ] Verify personnel duty status (SAD, T10/32).
- [ ] Validate legal authorities for each member.
- [ ] Adjust tasking accordingly.

### Review Cyber Tool Availability

- [ ] Identify required IR tools.
- [ ] Validate mission partner approval for tool usage.
- [ ] Prepare alternate tools/scripts if access denied.

#### Example Alternate Tool (if EDR blocked):

```bash
ps -ef | grep suspicious_process
netstat -antup
```

### Define Escalation Procedures

- [ ] Establish thresholds for escalation.
- [ ] Designate escalation contacts.
- [ ] Document and communicate process to team.

### Review OPSEC Requirements

- [ ] Confirm classification level and sensitivity of mission.
- [ ] Define handling requirements for data and reports.
- [ ] Provide OPSEC brief to IR team.

### Maintain Legal Coordination

- [ ] Engage JAG during planning.
- [ ] Validate ROE and jurisdiction.
- [ ] Document legal signoff on mission plan.

## References

- [ADP 5.0](https://armypubs.army.mil/epubs/DR_pubs/DR_a/ARN18126-ADP_5-0-000-WEB-3.pdf)

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Expanded checklist including non-standard considerations | Leo |
