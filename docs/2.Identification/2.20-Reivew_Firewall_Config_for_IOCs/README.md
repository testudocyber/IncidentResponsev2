# Review Firewall Configuration for Indicators of Compromise (IOCs)

## Task Review Firewall Configuration for Indicators of Compromise

## Conditions

Given access to a firewall appliance or host-based firewall, incident responders must review firewall rules and logs to detect unauthorized, suspicious, or misconfigured entries that may indicate compromise.

> **Operator Note:** Reviewing firewall rules and logs helps detect persistence mechanisms (e.g., backdoor ports), lateral movement, or data exfiltration paths.

## Standards

* The team member accesses the firewall or firewall management platform.  
* The team member exports and/or reviews active rulesets.  
* The team member reviews logs for abnormal entries, including:
    * Unusual inbound rules
    * Unusual outbound destinations
    * Temporary or recent rule additions
    * Disabled security policies
* The team member documents findings and correlates them with incident IOCs.

> **Operator Note:** Collaborate with firewall administrators to ensure rule and log completeness.

## End State

Firewall configuration and logs are reviewed. Any suspicious entries are documented and shared with incident commander for triage and remediation.

---

## Manual Steps

### Windows Defender Firewall

#### Export firewall configuration:

```powershell
netsh advfirewall export "C:\firewall-config.wfw"
```

#### List current rules:

```powershell
Get-NetFirewallRule | Format-Table Name, DisplayName, Enabled, Direction, Action
```

#### Check allowed inbound ports:

```powershell
Get-NetFirewallRule -Direction Inbound -Action Allow
```

> **Operator Note:** Pay special attention to ports 20-23, 3389, and high ephemeral ranges.

#### Review firewall logs:

```plaintext
C:\Windows\System32\LogFiles\Firewall\pfirewall.log
```

---

### Linux iptables / firewalld

#### List current rules (iptables):

```bash
sudo iptables -L -v -n
```

#### List active zones (firewalld):

```bash
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all
```

#### Export rules (iptables):

```bash
sudo iptables-save > /tmp/iptables-backup.txt
```

> **Operator Note:** Look for rules allowing unusual ports, IPs, or entire IP ranges.

---

### Network Firewalls (Palo Alto, Cisco ASA, Fortinet)

#### Export running config (Cisco ASA):

```bash
show running-config
```

#### Export security policy (Palo Alto via Web UI or CLI):

```bash
show running security-policy
```

> **Operator Note:** Work with Firewall Engineers/Admins to pull exports. Look for "permit any any", disabled zones, or suspicious policy changes.

---

## Running Script

* Not applicable.  
* Command-line and native firewall tools are used for this task.

## Dependencies

* Appropriate firewall access credentials (local admin or firewall admin role)
* Export or read access to firewall rule sets and logs
* Knowledge of normal enterprise firewall policies and IOCs

## Other available tools

* **Firewall Policy Viewer** (for Windows export files)  
* **Splunk / SIEM** (to correlate firewall logs with IOCs)  
* **Firewall Management Platforms** (Panorama, FMC, etc.)

> **Operator Note:** Firewall management consoles provide easier rule review. Use CLI when console access is unavailable.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain approval and access to target firewall or host.
- [ ] Export current rules and logging configurations.
- [ ] Review for risky rules (ANY/ANY, unexpected ports).
- [ ] Review logs for abnormal traffic patterns or denied attempts.
- [ ] Document and report findings.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | netsh, PowerShell | Enumerate and export firewall rules |
| Linux | iptables, firewalld | List and review local firewall rules |
| Network Firewalls | CLI/GUI exports | Review security policy and NAT rules |
| Universal | SIEM, Splunk | Correlate firewall logs with IOCs |

### Alternate Commands

#### Linux iptables - check rules allowing outbound:

```bash
sudo iptables -L OUTPUT -v -n
```

#### Windows - export log entries for parsing:

```powershell
Get-Content "C:\Windows\System32\LogFiles\Firewall\pfirewall.log" | Out-File firewall-log-review.txt
```

#### Cisco ASA - show recent config changes:

```bash
show archive config differences
```

### Best Practices

- Focus on changes during incident timeframe.
- Look for temporary or unusual "permit all" type rules.
- Review allowed outbound rules (common exfil path).
- Validate findings with Firewall/Network Administrators.

---

## References

[Microsoft NetSecurity Module](https://docs.microsoft.com/en-us/powershell/module/netsecurity/)  
[Linux Iptables Docs](https://wiki.centos.org/HowTos/Network/IPTables)  
[Palo Alto Networks CLI Guide](https://docs.paloaltonetworks.com/pan-os.html)  
[Cisco ASA Commands](https://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Initial version generated with enriched firewall rule and IOC review procedures | Leo |

