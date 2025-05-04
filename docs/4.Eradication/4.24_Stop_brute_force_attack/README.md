# 4.24 Stop Brute Force Attack

## Task

Detect and stop active brute force attacks against local, domain, cloud, and application accounts in order to prevent unauthorized access, credential compromise, and potential lateral movement.

---

## Conditions

Given:

* Security alerts indicating multiple authentication failures or login attempts.
* Access to authentication logs (Windows, Linux, Cloud, VPN, SaaS).
* Ability to interact with network appliances, domain controllers, endpoint security, and IAM platforms.
* Administrative privileges to take containment actions (account lockout, firewall rules, etc.).

---

## Standards

* Detection of brute force behavior through log review, SIEM alerts, or identity protection systems.
* Identification of targeted accounts and source IP addresses.
* Containment and blocking of attack sources at firewall, endpoint, or identity platform level.
* Disruption of brute force attack in progress and prevention of continued attempts.
* Documentation of incident and response actions.

---

## End State

* Brute force attack is halted.
* Attacker source IP addresses are blocked or isolated.
* Compromised or targeted accounts are secured.
* System configurations or policies are adjusted to prevent recurrence.

---

## Notes

Brute force attacks can occur in many locations:

* Domain Controllers (Windows AD authentication)
* Linux/Unix SSH servers
* VPN gateways and remote access portals
* Cloud identity platforms (Azure AD, Okta, Google Workspace)
* SaaS applications with public login portals

Brute force attacks may use:

* Password spraying (single password across many accounts)
* Dictionary or credential stuffing attacks
* Targeted password guessing

---

## Manual Steps

### Step 1 → Detect Brute Force Activity

#### Indicators

* Multiple login failures from a single IP
* Login attempts from unusual geographies
* Same account targeted repeatedly across time
* High volume login attempts across many accounts

#### Tools/Commands

##### Windows (Domain Controller Event Logs)

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} | Select-Object TimeCreated, Message
```

##### Linux (SSH Brute Force)

```bash
cat /var/log/auth.log | grep "Failed password"
grep "Invalid user" /var/log/auth.log
```

##### Azure AD

* Azure Portal → Azure AD → Sign-ins → Filter for failures
* Look for IPs with many failures across accounts

##### VPN Logs

* Check RADIUS / VPN concentrator logs for repeated failures
* Look for same IP and/or usernames repeated

##### SIEM / EDR

* Review alerts tagged as "Brute Force", "Account Lockout", or "Password Spraying"

---

### Step 2 → Contain Attack Sources

#### Block Source IP

##### Windows Firewall

```powershell
New-NetFirewallRule -DisplayName "Block Brute Force Attacker" -Direction Inbound -RemoteAddress <IP> -Action Block
```

##### Linux Firewall (iptables)

```bash
iptables -A INPUT -s <attacker_ip> -j DROP
```

##### Cloud (Azure)

* Add attacker IP to Conditional Access policy → Block.
* Apply Azure Firewall rule.

##### Cloud (AWS)

* Use AWS WAF or Security Group to block IP.

##### Cloud (GCP)

* Add firewall rule to block external IP.

---

### Step 3 → Secure Targeted Accounts

* Force password reset for accounts that may be compromised.

```powershell
Set-ADAccountPassword -Identity username -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewPassword!" -Force)
```

* Enable MFA (if not already enabled).
* Check for any lockouts or suspicious account behavior.

##### Azure AD

* Block sign-in for account if necessary.
* Require password change on next sign-in.
* Enable Conditional Access for stronger authentication requirements.

##### Linux

```bash
passwd -l username  # Lock the account
passwd username     # Reset password
```

---

### Step 4 → Monitor and Validate Block

* Continue monitoring authentication logs for recurring attempts.
* Use SIEM to create custom rules or dashboards to visualize attack metrics.
* Confirm IP blocks are working → no further authentication attempts logged.

---

### Step 5 → Implement Long-Term Mitigation

* Enable Account Lockout Policies (Windows / Linux)

##### Windows (GPO)

* Computer Config → Windows Settings → Security Settings → Account Policies → Account Lockout Policy

##### Linux (pam_tally2 / faillock)

```bash
faillock --user username
```

* Enable MFA where possible.
* Enforce strong passwords.
* Implement geo-blocking and Conditional Access in cloud environments.
* Deploy network intrusion prevention systems (NIDS/IPS) for brute force pattern detection.

---

## Running Script Examples

##### Example → Linux Block IP After Detection

```bash
iptables -A INPUT -s 192.168.1.200 -j DROP
```

##### Example → Windows GPO Lockout Configuration

```powershell
secedit /export /cfg C:\secpol.cfg
notepad C:\secpol.cfg
# Edit Lockout settings manually and apply using:
secedit /configure /db secedit.sdb /cfg C:\secpol.cfg /areas SECURITYPOLICY
```

##### Example → Azure Block Sign-in Immediately

* Azure AD → User → Block Sign-in → Save

---

## Dependencies

* SIEM or central log aggregator
* Administrative access to firewalls, endpoints, and cloud IAM
* Active Directory / Azure AD / Okta / IAM management access
* Network security tooling (IDS/IPS, VPN Logs)
* EDR tooling (for process and connection containment)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Splunk, Security Onion, ELK | Cross-platform | Installed | Authentication and brute force detection |
| Fail2Ban | Linux | Installed | Auto-ban brute force attempts |
| Azure Conditional Access | Cloud | Configured | Block based on IP or login failure patterns |
| AWS WAF | Cloud | Configured | Block IPs at edge |
| CrowdStrike / SentinelOne | Cross-platform | Installed | Detect + isolate brute force tools running |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Confirm alert and identify brute force attack patterns
- [ ] Identify targeted accounts and source IP
- [ ] Block IP addresses and isolate attacker hosts
- [ ] Disable / reset passwords for targeted accounts
- [ ] Enable MFA for vulnerable accounts
- [ ] Monitor for continued attempts and validate blocks
- [ ] Review policies and strengthen protections post-incident

### Best Practices

* Always review blocking policies to avoid collateral damage (VPN providers, shared IPs).
* Coordinate with IT on password resets and lockouts → avoid impacting end users unnecessarily.
* Ensure temporary IP blocks are reviewed regularly → avoid long-term deny lists without validation.
* Implement automated blocking (Fail2Ban, cloud IP blocklists) for repeat attackers.

---

## References

* [Windows Account Lockout Policy](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/account-lockout-policy)
* [Azure AD Sign-in Monitoring](https://learn.microsoft.com/en-us/azure/active-directory/reports-monitoring/concept-sign-ins)
* [Fail2Ban](https://www.fail2ban.org/wiki/index.php/Main_Page)
* [AWS WAF IP Block](https://docs.aws.amazon.com/waf/latest/developerguide/waf-ip-set-rule.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Generated detailed operator workflow with containment, blocking, and recovery process | Leo |
