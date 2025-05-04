# 4.55 Verify Network Segmentation and ACLs

## Task

Verify network segmentation and access control lists (ACLs) are properly configured to restrict unauthorized lateral movement, enforce network boundaries, and align with security policies.

---

## Conditions

Given access to network diagrams, VLAN configurations, router/switch/firewall ACLs, and the ability to perform active network validation testing.

---

## Standards

* Review logical network segmentation (VLANs, subnets).
* Review ACLs on routers, switches, and firewalls for proper traffic restrictions.
* Perform active validation tests to confirm isolation and enforcement.
* Document and report any gaps or weaknesses.

---

## End State

Network segmentation and ACL configurations are validated. Unauthorized communication pathways are identified, and corrective actions are tracked.

---

## Notes

- Flat or poorly segmented networks increase risk of lateral movement during incidents.
- ACL misconfigurations are common and often provide attackers unintended access.
- Validation requires both configuration review and live traffic testing.

---

## Manual Steps

### Step 1: Review Network Segmentation Design

- Obtain and review network diagrams and VLAN/subnet documentation.
- Identify:
  - Mission-critical network segments (e.g., servers, domain controllers).
  - User workstations and guest networks.
  - External access zones (DMZ).
  - Management networks (IPMI/iLO, switches, APs).

> **Operator Note:** Critical and sensitive systems should never be on the same VLAN as user workstations or guest users.

---

### Step 2: Review VLAN and Subnet Configurations

#### Switches / VLAN Config

- Review VLAN assignment and trunking on switches:

Example Cisco:

```bash
show vlan brief
show interfaces trunk
```

Look for:

- VLANs with too many mixed roles (e.g., workstations + servers).
- Native VLAN usage → should not carry sensitive traffic.
- Unused ports assigned to "black hole" VLAN (best practice).

> **Operator Note:** VLAN hopping is a real risk → avoid poorly assigned or default VLANs.

---

### Step 3: Review Router, Switch, and Firewall ACLs

#### Example review:

| Device | Command | What to look for |
|--------|---------|------------------|
| Cisco Router | `show access-lists` | Permissive "permit any any" rules |
| Cisco Switch (SVI ACLs) | `show ip access-lists` | Incomplete or missing ACLs |
| Firewalls | Web/CLI | Overly broad or "allow all" rules |

Validate:

- Segments are only allowed access as required (e.g., workstation VLAN → DNS, not SMB or RDP).
- Management interfaces (switches, routers, servers) are not exposed to all VLANs.
- Guest networks have no access to internal segments.

> **Operator Note:** "Permit any" or large CIDR "permit" ranges → red flag for misconfiguration.

---

### Step 4: Perform Active Network Validation Testing

#### Recommended Tools:

| Tool | Platform | Usage |
|------|----------|-------|
| Nmap | Cross-platform | Scan from source VLAN to destination VLAN |
| Netcat | Cross-platform | Port testing |
| Hping3 | Linux | Advanced packet crafting (optional) |

#### Example Nmap Validation:

```bash
nmap -Pn -p 22,135,3389 192.168.100.0/24
```

> From user VLAN → ensure you CANNOT reach sensitive ports (RDP, SMB) on server VLAN.

Example Netcat quick test:

```bash
nc -vz 192.168.100.10 3389
```

> **Operator Note:** Tests should be performed from representative VLANs → user → server, guest → internal, etc.

---

### Step 5: Investigate and Escalate Findings

| Issue Found | Recommended Action |
|-------------|--------------------|
| Flat network (no segmentation) | Recommend VLAN implementation |
| Missing ACLs or overly permissive rules | Work with network admins to implement controls |
| Guest or public networks can access internal systems | Block guest VLAN from internal ranges |
| Unused switch ports active | Shutdown or assign to unused VLAN |

> **Operator Note:** Engage network owners and administrators before making changes → change control procedures apply.

---

## Dependencies

* Access to network diagrams and configuration files
* Access to switches, routers, and firewall configuration interfaces
* Ability to run network scans and connectivity tests

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Nmap | Cross-platform | Package manager | Network connectivity scans |
| Netcat | Cross-platform | Built-in or package manager | Port scanning and testing |
| Switch CLI (Cisco/Aruba/etc.) | CLI | Native | VLAN and ACL validation |
| Firewall GUI / CLI | Network device | Native | Review ACL and rule sets |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain network diagrams and authorized segmentation scheme.
- [ ] Review VLAN configurations for proper segmentation.
- [ ] Review router, switch, and firewall ACLs.
- [ ] Perform active validation from VLAN to VLAN and guest networks.
- [ ] Identify unauthorized access paths or weak rules.
- [ ] Document and recommend corrective actions.

### Best Practices

- Avoid "permit any any" rules → use least privilege principle.
- Isolate critical infrastructure → servers, management interfaces.
- Guest networks → no internal access.
- Monitor VLAN-to-VLAN traffic → validate ACLs are enforced.

---

## References

- [Cisco Security Best Practices](https://www.cisco.com/c/en/us/solutions/enterprise-networks/enterprise-network-security/index.html)
- [NIST 800-41 Rev 1 - Guidelines on Firewalls and Firewall Policy](https://csrc.nist.gov/publications/detail/sp/800-41/rev-1/final)
- [Nmap Network Scanning Guide](https://nmap.org/book/man.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with detailed operator-focused network segmentation and ACL validation process | Leo |
