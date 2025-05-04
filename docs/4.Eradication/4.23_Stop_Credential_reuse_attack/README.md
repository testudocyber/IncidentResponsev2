# 4.23 Stop Credential Reuse Attack

## Task

Detect, investigate, and stop the reuse of compromised credentials on the network to prevent lateral movement, unauthorized access, and escalation of privileges during an incident.

---

## Conditions

Given:

* Active security monitoring (SIEM, EDR, Identity Protection tools).
* User accounts are suspected or confirmed compromised.
* Ability to interact with domain controllers, identity platforms, and endpoints.
* Administrative access to relevant tools and network devices.
* Threat intelligence or incident indicators identifying credential reuse attempts.

---

## Standards

* Investigate and validate the source and scope of credential reuse attempts.
* Identify accounts involved in malicious or unauthorized authentications.
* Prevent further unauthorized access by disabling accounts or enforcing password resets.
* Block identified attacker hosts from the network or authentication mechanisms.
* Document the response and findings thoroughly.

---

## End State

* Credential reuse is stopped.
* Attacker-controlled sessions are terminated.
* Accounts are reset or disabled to prevent re-compromise.
* Blocking measures are enforced at the network, endpoint, and identity layers.

---

## Notes

Credential reuse attacks often follow phishing, password spraying, or prior compromise of privileged credentials. These attacks typically target:

* Domain Controllers
* Cloud Identity Providers (Azure AD, Okta, Google Workspace)
* VPN portals
* Remote Desktop Gateways
* SaaS Applications

Indicators of Credential Reuse:

* Logins from unusual geographies
* Multiple accounts authenticating from same IP
* Service accounts being used interactively
* Logons outside normal working hours
* Accounts accessing systems they normally don't use

---

## Manual Steps

### Step 1 → Validate Alerts and Identify Affected Accounts

#### Tools / Sources

- SIEM (Splunk, Elastic, Security Onion)
- Azure AD Sign-in Logs / M365 Defender / Sentinel
- EDR (CrowdStrike, Defender for Endpoint)
- Firewall/VPN logs
- On-prem AD Security Event Logs

#### Examples

##### Windows Event Logs

```powershell
Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4624} | Select-Object -First 20
```

##### Azure AD Sign-in

* Azure Portal → Azure AD → Sign-ins → Filter for failure and unusual sign-in attempts.

##### EDR

* Review "Account Authentication" or "Lateral Movement" detection alerts.

---

### Step 2 → Confirm Credential Reuse Pattern

* Look for multiple failed logins followed by successful ones.
* Identify source IP addresses → Are they shared across different user accounts?
* Identify repeat logins from non-corporate IPs or foreign locations.
* Validate user behavior with managers if necessary.

---

### Step 3 → Contain and Block the Attack

#### On-Prem AD

* Disable compromised accounts immediately.

```powershell
Disable-ADAccount -Identity username
```

* Force password resets on suspected compromised accounts.

```powershell
Set-ADAccountPassword -Identity username -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewPassword!" -Force)
```

* Check for attacker-created accounts and disable/remove.

#### Cloud Identity (Azure AD / Okta / Google Workspace)

* Disable sign-in for compromised accounts.
* Enforce password reset.
* Revoke sessions and tokens.

```powershell
Revoke-AzureADUserAllRefreshToken -ObjectId "<userObjectId>"
```

* Enable MFA (if not already enabled).
* Apply conditional access to block login from suspicious IPs or locations.

#### Network (Contain attacker infrastructure)

* Add malicious source IP addresses to perimeter block lists (firewall ACLs).

```bash
iptables -A INPUT -s <attacker_ip> -j DROP
```

* Disable rogue VPN accounts or sessions.

#### Endpoints

* Use EDR to isolate endpoints controlled by attackers.

```powershell
# CrowdStrike Example
falconctl -g --isolation
```

---

### Step 4 → Review for Persistence and Lateral Movement

* Check scheduled tasks, new services, and startup items on compromised hosts.
* Review RDP and remote management access.
* Look for shadow admin accounts or group membership changes.
* Conduct memory or disk analysis if malware was used.

---

### Step 5 → Restore and Monitor

* Reset all compromised accounts.
* Rotate privileged account passwords (Domain Admins, Service Accounts).
* Enhance monitoring with temporary alerting rules for affected users.
* Conduct user security awareness refresh → credential hygiene.

---

## Running Script Examples

##### Example → Detect multiple authentication attempts (Windows AD)

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} | Select-Object TimeCreated, Message | Out-GridView
```

##### Example → Block malicious IP on Windows firewall

```powershell
New-NetFirewallRule -DisplayName "Block Attacker IP" -Direction Inbound -RemoteAddress <Attacker IP> -Action Block
```

---

## Dependencies

* Administrative access to Identity Platform (AD / Azure AD / Okta / Google)
* SIEM / Log Aggregator
* Endpoint Detection and Response (EDR)
* Network Firewall or ACL Management
* User Notification and Password Reset Process

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Active Directory PowerShell Module | Windows | Installed | Manage AD users and accounts |
| AzureAD / MSGraph PowerShell Modules | Windows / Cloud | Installed | Cloud account management |
| CrowdStrike / SentinelOne / Defender EDR | Cross-platform | Installed | Contain endpoints, detect behavior |
| Splunk / Security Onion | Cross-platform | Installed | Detect authentication anomalies |
| VPN Gateway | Cross-platform | Installed | Review/terminate rogue sessions |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Validate alert and identify affected user accounts
- [ ] Confirm pattern of credential reuse or lateral movement
- [ ] Disable or restrict affected accounts immediately
- [ ] Block malicious IPs / isolate attacker-controlled endpoints
- [ ] Rotate passwords for compromised or high-risk accounts
- [ ] Investigate lateral movement and persistence
- [ ] Enable MFA where applicable and review access policies
- [ ] Document entire process and escalate for post-incident review

### Best Practices

* Always validate and isolate before removal actions to avoid tipping off attackers.
* Use MFA to reduce impact of stolen credentials.
* Rotate all shared / privileged accounts even if only one appears compromised.
* Work with IT + Helpdesk to coordinate password resets smoothly.
* Monitor affected users and accounts post containment.

---

## References

* [Azure AD Sign-in Logs](https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-sign-ins)
* [Active Directory PowerShell Guide](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)
* [CrowdStrike Containment Documentation](https://www.crowdstrike.com/endpoint-security-products/falcon-prevent/)
* [Splunk Authentication Monitoring](https://docs.splunk.com/Documentation/Splunk)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Generated detailed operator workflow with containment, investigation, and recovery process | Leo |
