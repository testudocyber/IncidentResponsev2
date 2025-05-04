# 4.05 Investigate Alerts from Network SIEM using IDS and Other Logs

## Task

Investigate Alerts from Network Security Information and Event Management (SIEM) using IDS and other Logs

## Conditions

Given a fully installed SIEM (e.g., Graylog or similar) configured to accept logs from network hosts, a logon account for the SIEM, and a known network topology.

---

## Standards

* Logon to the SIEM interface.
* Scan for potential malicious or abnormal user or system activity, possibly by filtering events by certain category (e.g., logon/logoff of privileged users) to locate abnormal or suspected malicious behavior.
* Using the reported information (user logon; privilege escalation; source/destination IP addresses, ports; file download information), conduct initial investigative steps to determine if activity can be deemed malicious.
* If necessary, coordinate with other team members or system owner to determine if the activity is normal.
* Notify Team Leader of suspected malicious activity and provide detailed information gathered from initial investigative steps.

---

## End State

Suspected abnormal activity reported by SIEM is investigated, determined to be malicious activity and reported to team intel member and Team Lead for further action.

---

## Notes

- Rarely will an alert displayed by a SIEM be sufficient to determine a course of action for the team.
- The SIEM operator must correlate reported activity against normal network and host activity often without the time to establish a proper baseline.
- SIEM activity is only a piece of the puzzle that may lead to establishing the presence of malicious activity on the network.
- The SIEM operator should be aware of the cyber key terrain including servers, administrator accounts, file storage and database locations that will likely be targeted.
- If Host-based Intrusion Detection Systems (HIDS) are not being aggregated into the SIEM, the operator must coordinate with Blue Team members and/or system owners to cross-check logs.

---

## Manual Steps

### Step 1: Log into the SIEM Platform

- Access Graylog, Splunk, Security Onion, or other SIEM via secure connection.

#### Example:
```
https://[SIEM-IP]/login
```

### Step 2: Search for Suspicious Alerts

- Use SIEM query capabilities to filter on:
  - Authentication failures
  - Privilege escalations
  - Unusual outbound connections
  - High-volume data transfers
  - Rare process executions

#### Graylog Example:
```bash
event_type:"authentication_failure" AND user:"admin"
```

#### Splunk Example:
```bash
index=main sourcetype=WinEventLog:Security EventCode=4625
```

> **Operator Tip:** Prioritize high-value assets and privileged accounts.

---

### Step 3: Investigate Event Context

- Gather:
  - Usernames involved
  - Source and destination IP addresses
  - Timestamps
  - Systems involved
  - Actions taken (e.g., login attempts, file downloads)

- Correlate findings with:
  - IDS alerts (Zeek, Suricata)
  - Host logs (Windows Security Logs, Sysmon)

---

### Step 4: Validate with Other Data Sources

- Cross-check suspicious events:
  - Endpoint Detection and Response (EDR)
  - Firewall logs
  - DNS logs
  - Asset Inventory

- Determine if activity was:
  - Authorized
  - Misconfiguration
  - Malicious

---

### Step 5: Report Findings

- If malicious activity is suspected:
  - Notify the Team Leader.
  - Escalate to Intel Team.
  - Document the alert, investigative steps, and evidence gathered.

---

## Running Script Example

#### Quick Graylog Query for Top Source IPs

```bash
source:"*" | count by source_ip
```

#### Splunk Top Talkers Example

```bash
index=main | stats count by src_ip, dest_ip | sort -count
```

---

## Dependencies

* Access to operational SIEM (Graylog, Splunk, Security Onion)
* Access to complementary data sources (IDS, Firewall, Host logs)
* List of privileged accounts and key cyber terrain
* IR escalation path documented

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Graylog | Cross-platform | Docker or VM install | Open-source log aggregation and analysis |
| Splunk | Cross-platform | Enterprise | SIEM with rich query and correlation |
| Security Onion | Linux | ISO install | IDS/IPS/SIEM integration |
| OSQuery | Cross-platform | Agent install | Host-level event correlation and investigation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Log into SIEM platform and verify log flow.
- [ ] Search for high-risk behaviors using targeted queries.
- [ ] Investigate user, host, and network context for suspicious events.
- [ ] Validate against external sources (IDS, EDR, firewall logs).
- [ ] Report and escalate suspicious findings.
- [ ] Document all investigation and findings thoroughly.

### Best Practices

- Know your environment: high-value assets, privileged users, normal behaviors.
- Search broadly first, then narrow based on context.
- Use pivoting — find additional context for every alert.
- Always assume SIEM alerts are a starting point, not a conclusion.

---

## References

- [Security Onion Solutions](https://securityonionsolutions.com/)
- [Splunk Enterprise Security](https://www.splunk.com/en_us/products/premium-solutions/splunk-enterprise-security.html)
- [SIEM Overview](http://www.tomsitpro.com/articles/siem-solutions-guide,2-864.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Corrected and expanded operator version for SIEM investigations and alert correlation | Leo |
