# 4.21 Find and Contain Rogue Workstations

## Task

Locate rogue or unauthorized workstations on the network and isolate them to prevent potential damage, data exfiltration, or continued attacker access.

---

## Conditions

Given:

* Notification of suspicious host activity or rogue workstation alert.
* Network access and scanning tools (Nmap, Nessus).
* Switch and firewall administrative access.
* Administrator credentials and network documentation (known hosts, DHCP, AD).
* One or more Indicators of Compromise (IOCs) suggesting rogue or unauthorized devices.

---

## Standards

* Rogue workstation indicators are validated and confirmed.
* The network is scanned and analyzed to locate unauthorized workstations.
* Identified rogue workstations are isolated immediately from the production environment using switch ports, firewall rules, and DHCP controls.
* All steps are documented and reported to the incident response lead.

---

## End State

* Rogue workstations are successfully identified and removed or contained.
* Network is restored to a secure state.
* Post-incident review identifies prevention and detection improvements.

---

## Notes

* Rogue workstations may be unauthorized employee devices, attacker-controlled systems, or compromised hosts acting outside of normal boundaries.
* Finding rogue workstations requires correlation of network data, endpoint visibility, and configuration baselines.
* Always coordinate containment actions with the network owner or responsible party.

---

## Manual Steps

### Detection Phase - Identify Rogue Workstation

#### Indicators of Rogue Workstations

* New or unknown MAC addresses on network switches
* Unauthorized IP addresses in DHCP
* Unknown or suspicious workstation names in Active Directory
* Devices detected using WiFi scans but not registered
* Devices communicating with C2 infrastructure

---

#### Step-by-Step Detection Procedures

##### 1. Validate the Alert or Suspicion

* Review SIEM alerts
* Review DHCP logs → Look for unknown MAC addresses
* Review AD → Look for newly joined or renamed workstations

##### 2. Scan Network for Unauthorized Devices

###### Nmap Options:

```bash
# List Scan - See what hosts are visible
nmap -sL <IP Address/Subnet>

# Ping Scan - Find active hosts
nmap -sn <IP Address/Subnet>

# ARP Scan - Fast local network discovery
nmap -PR <IP Address/Subnet>

# No ping scan (useful if hosts block ping)
nmap -Pn <IP Address/Subnet>
```

###### Additional detection methods

* Use Wireshark to sniff new MAC addresses on local subnet
* Review switch CAM tables:

```bash
show mac address-table
```

* Use `arp -a` on known hosts to review detected devices

###### Nessus Scan (if authorized)

* Perform host discovery scan to fingerprint unknown systems
* Review OS, hostname, open ports, and services

---

### Containment Phase - Isolate Rogue Workstation

#### Step-by-Step Containment Procedures

##### Option 1 → Switch Port Shutdown

* Locate MAC address on switch
* Disable port:

```bash
interface GigabitEthernet1/0/24
shutdown
```

* Dispatch Security to investigate physical location

##### Option 2 → Switch Access Layer ACL

* Create ACL to drop all traffic from rogue host IP or MAC
* Apply ACL to VLAN or port

##### Option 3 → Boundary Firewall

* Apply deny rule on edge firewall for rogue workstation IP
* Block all inbound/outbound traffic

##### Option 4 → DHCP Deny List (Windows DHCP)

* DHCP Manager → IPv4 → Filters → Deny
* Add rogue MAC address to deny list

##### Option 5 → Group Policy (GPO)

* Apply Windows Firewall rule to block rogue IP across domain joined hosts
* Apply policy to the OU where rogue workstation was detected

---

### Reporting and Follow-up

* Document:
  * MAC, IP, hostname, username (if applicable), location
  * Detection and containment steps
  * Associated IOC (if known)
* Coordinate forensic acquisition (if necessary)
* Notify network and security stakeholders

---

## Running Script Examples

#### Nmap Full Discovery Example

```bash
nmap -sn 192.168.1.0/24
nmap -PR 192.168.1.0/24
```

#### Windows DHCP Deny MAC (manual method)

* DHCP Console → IPv4 → Filters → Deny → Add MAC

#### Switch Port Example (Cisco)

```bash
show mac address-table
interface GigabitEthernet1/0/24
shutdown
```

---

## Dependencies

* Network scanning utilities (Nmap, Nessus)
* Switch and firewall administrative access
* DHCP administrative access
* Active Directory credentials and access
* Physical security coordination (for physical rogue workstations)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Nmap | Cross-platform | Native or installed | Network discovery |
| Nessus | Cross-platform | Installed | Vulnerability and host scanning |
| Wireshark | Cross-platform | Installed | MAC / ARP / IP analysis |
| Switch CLI | Network | Native to switch | Port control and MAC review |
| DHCP Manager | Windows Server | Native | Manage DHCP deny/allow lists |
| Group Policy | Windows | Domain controllers | Enforce network rules |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Confirm alert → Validate that rogue device exists
- [ ] Determine IP, MAC, Hostname
- [ ] Scan subnet to identify rogue workstation
- [ ] Validate against asset inventory
- [ ] Initiate containment:
  - [ ] Switch port shutdown or ACL
  - [ ] Firewall block
  - [ ] DHCP deny
  - [ ] GPO block
- [ ] Document containment action
- [ ] Notify IR lead for further action

### Best Practices

* Always confirm rogue status before containment → avoid impacting legitimate devices
* Coordinate with network team → avoid bypassing change control or policy
* Maintain logs of all network modifications (audit trail)
* Validate rogue workstation isolation post-containment → retest reachability
* Collect forensic image (if applicable) prior to removal

---

## References

* [NMAP Host Discovery](https://nmap.org/book/man-host-discovery.html)
* [SANS Whitepaper - Detecting and Preventing Rogue Devices](https://www.sans.org/reading-room/whitepapers/wireless/detecting-preventing-rogue-devices-network-1866)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded with operator workflow, containment methods, tool usage, and escalation guidance | Leo |
