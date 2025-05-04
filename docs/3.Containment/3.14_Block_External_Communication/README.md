# Block External Communication Methods

## Task Block External Communication Methods from Compromised Systems

## Conditions

Given systems identified as impacted or at risk, and access to endpoint and network controls, the operator will block all external communication capabilities to prevent command and control (C2), data exfiltration, or attacker pivoting.

> **Operator Note:** Blocking external communications reduces adversary capabilities while containment and eradication operations are underway. This prevents data loss and stops live connections.

## Standards

* Team member identifies systems requiring external communications block.  
* Team member determines and implements appropriate blocking methods (host firewall, network firewall, DNS).  
* Team member validates block effectiveness.  
* Team member coordinates with enclave and network owners to avoid unintended disruption.  
* Team member documents all changes and actions taken.

## End State

Compromised or impacted systems are fully isolated from external communications until remediation is complete.

---

## Notes

- Blocking should be as granular as possible to avoid unnecessary disruption.  
- When possible, only block outbound traffic.  
- Use a layered approach → host firewall, network ACLs, DNS sinkholing.  
- Validate with packet captures or monitoring tools.

---

## Manual Steps

### Method 1: Host Firewall (Windows)

#### Block all outbound traffic except internal ranges

```powershell
New-NetFirewallRule -DisplayName "Block External Outbound" -Direction Outbound -RemoteAddress 0.0.0.0/0 -Action Block
New-NetFirewallRule -DisplayName "Allow Internal Outbound" -Direction Outbound -RemoteAddress 192.168.0.0/16,10.0.0.0/8 -Action Allow
```

> **Operator Note:** Adjust internal ranges as necessary.

---

### Method 2: Host Firewall (Linux iptables)

```bash
iptables -P OUTPUT DROP
iptables -A OUTPUT -d 192.168.0.0/16 -j ACCEPT
```

> **Operator Note:** Be cautious. This blocks all external outbound unless explicitly allowed.

---

### Method 3: DNS Sinkhole or Block External DNS

#### Example - Windows hosts via GPO:

```plaintext
Computer Configuration → Administrative Templates → Network → DNS Client → DNS Servers
```

- Set DNS servers to internal only.
- Or block UDP/TCP 53 traffic to external DNS using firewall.

---

### Method 4: Network Firewall / Router ACLs

- Block outbound traffic to external internet from impacted hosts’ IP ranges.
- Only allow communication to IR tools or known trusted systems.

---

### Method 5: Remove Default Gateway (Emergency Isolation)

#### Windows:

```powershell
Remove-NetRoute -DestinationPrefix "0.0.0.0/0"
```

#### Linux:

```bash
ip route del default
```

> **Operator Note:** This will block all internet access but retains local communications.

---

## Running Script (Windows example for rapid host blocking)

```powershell
# Block all external traffic but allow internal
New-NetFirewallRule -DisplayName "Block All External" -Direction Outbound -RemoteAddress 0.0.0.0/0 -Action Block
New-NetFirewallRule -DisplayName "Allow Internal" -Direction Outbound -RemoteAddress 192.168.0.0/16,10.0.0.0/8 -Action Allow
```

---

## Dependencies

* RSAT and Firewall management permissions  
* Network firewall / switch ACL access  
* DNS server administration (if using sinkholing or blocking)  
* Endpoint control agents (optional)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| Windows Firewall + PowerShell | Windows | Rapid per-host block |
| Linux iptables / ufw | Linux | Host-level outbound blocking |
| Network Firewall (Palo Alto, Cisco ASA, Fortigate) | Network | Segment or site-wide blocking |
| DNS Sinkhole (Unbound, Bind, Infoblox) | Network | Block DNS-based outbound |
| VPN or NAC solutions | Enterprise | Restrict network access |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Identify systems requiring external communications block.
- [ ] Determine level of block (DNS, firewall, gateway removal).
- [ ] Implement block using most appropriate method.
- [ ] Validate that outbound connections are denied.
- [ ] Log actions and communicate with enclave/network owner.
- [ ] Remove or adjust blocks after containment is complete.

### Best Practices

- Use host firewalls for speed, network firewalls for scale.
- Avoid blanket blocking unless necessary → target impacted systems only.
- Use DNS blocklists or sinkholing for stealthy C2 detection and blocking.
- Document and review blocks regularly during IR operations.

---

## References

[Windows Defender Firewall PowerShell Cmdlets](https://learn.microsoft.com/en-us/powershell/module/netsecurity/)  
[iptables Linux Firewall Guide](https://linux.die.net/man/8/iptables)  
[DNS Sinkhole Strategies](https://www.crowdstrike.com/cybersecurity-101/dns-sinkhole/)  
[NIST SP 800-94 Guide to Intrusion Detection and Prevention Systems (IDPS)](https://csrc.nist.gov/publications/detail/sp/800-94/archive/2007-02-27)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for blocking external communication via firewall, DNS, and ACLs | Leo |
