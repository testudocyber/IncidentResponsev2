# Create Firewall Rule List

## Task Create Firewall Rule List

## Conditions

Given access to suspected compromised hosts and appropriate credentials.

> **Operator Note:** Local firewall rules provide critical insight into allowed and blocked communications on hosts.

## Standards

* Establish local firewall rules baseline.  
* Periodically rerun checks and compare against baseline.  
* Review rules for security gaps and misconfigurations.

> **Operator Note:** Document all rule changes and justification for audit purposes.

## End State

Local firewall rules baseline established and maintained.

## Manual Steps

### Display all firewall rules:

```bat
netsh advfirewall firewall show rule name=all
```

### Set firewall on/off:

```bat
netsh advfirewall set currentprofile state on
netsh advfirewall set currentprofile firewallpolicy blockinboundalways,allowoutbound
netsh advfirewall set allprofile state on
netsh advfirewall set allprofile state off
```

> **Operator Note:** Changing firewall status impacts network security. Obtain approval before altering settings.

### Example firewall rules:

```bat
netsh advfirewall firewall add rule name="Open Port 80" dir=in action=allow protocol=TCP localport=80
netsh advfirewall firewall delete rule name="My Application" program="C:\\MyApp\\MyApp.exe"
netsh advfirewall firewall set rule group="remote desktop" new enable=Yes profile=domain
netsh advfirewall set currentprofile logging C:\\Logs\\firewall.log
```

### Windows Firewall Log Settings:

```bat
more %systemroot%\system32\LogFiles\Firewall\pfirewall.log
netsh advfirewall set allprofile logging droppedconnections enable
netsh advfirewall set allprofile logging allowedconnections enable
```

### PowerShell cmdlets for firewall:

```powershell
Get-Command *-*firewall*
```

## Running Script

* Transfer and run `2.19_CreateFirewallRuleList.ps1`:

```powershell
.\2.19_CreateFirewallRuleList.ps1
```

* Review export file for local firewall configuration.

## Dependencies

* Windows operating system  
* PowerShell installed and configured

## Other available tools

* Group Policy Management Console (GPMC) - Domain-wide firewall management
* Advanced Security Windows Firewall GUI

> **Operator Note:** Domain firewall rules may override local rules. Validate which profile is active.

## References

[PowerShell NetSecurity](https://technet.microsoft.com/en-us/library/jj554906(v=wps.630).aspx)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Collect and export local firewall rules from target hosts.
- [ ] Establish and maintain firewall rule baselines.
- [ ] Review rules for risky configurations.
- [ ] Report gaps or misconfigurations to network owner.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | netsh | CLI-based firewall rule management |
| Windows | PowerShell NetSecurity module | Scriptable firewall rule collection |
| Universal | Advanced Security Firewall GUI | Visual firewall rule review |

### Alternate Commands

#### Quick allow rule creation:

```powershell
New-NetFirewallRule -DisplayName "Allow HTTP" -Direction Inbound -LocalPort 80 -Protocol TCP -Action Allow
```

#### Export firewall configuration:

```powershell
netsh advfirewall export "C:\firewall-config.wfw"
```

### Best Practices

- Document rule baselines for future comparison.
- Review default and allow rules for security gaps.
- Validate firewall profiles (Domain, Private, Public) are correctly applied.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched firewall rule collection, validation, and operator procedures | Leo |
