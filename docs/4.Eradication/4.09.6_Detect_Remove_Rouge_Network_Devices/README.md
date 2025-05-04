# 4.09.6 Detect and Remove Rogue Network Devices

## Task

Detect and remove unauthorized (rogue) network devices that may have been connected to the network to facilitate unauthorized access, sniffing, bridging, or lateral movement.

---

## Conditions

Given access to the network infrastructure (switches, routers), network scanning tools, physical access to network areas, and knowledge of expected network architecture.

---

## Standards

* Detect all active network devices connected to the environment.
* Differentiate between authorized and unauthorized devices.
* Investigate and validate unknown devices.
* Physically or logically remove rogue devices from the network.

---

## End State

Rogue network devices are detected, validated, and removed or disconnected. Network inventory is updated and reviewed with network owner.

---

## Notes

- Rogue network devices can include unmanaged switches, unauthorized wireless routers, hubs, and sniffing/bridging devices.
- These devices may be malicious (planted for sniffing or C2) or accidental (brought in by users).
- Physical and logical detection techniques must be combined for full coverage.

---

## Manual Steps

### Step 1: Obtain Network Inventory and Authorized Device List

- Gather list of authorized switches, routers, APs, printers, and any other network-connected infrastructure.
- Obtain network diagrams if available.
- Understand authorized VLANs and subnet allocations.

> **Operator Note:** This will help you quickly identify unknown MAC and IP addresses later.

---

### Step 2: Scan Network for Active Devices

#### Recommended Tools:

| Tool | Platform | Usage |
|------|----------|-------|
| Nmap | Linux/Windows/macOS | Network discovery and port scanning |
| arp-scan | Linux | Layer 2 network discovery |
| Angry IP Scanner | Cross-platform | Simple IP scanner |
| Netdisco | Linux | Network inventory and discovery |

#### Example: Nmap

```bash
nmap -sn 192.168.1.0/24
```

#### Example: arp-scan (Linux)

```bash
sudo arp-scan --interface=eth0 192.168.1.0/24
```

Results will show:

- IP address
- MAC address
- Vendor (if available)

> **Operator Note:** Devices with unfamiliar vendors (ex: "Shenzhen Co.") are high priority for review.

---

### Step 3: Analyze Switch CAM Tables for Unknown MAC Addresses

Access managed switches and dump the MAC address table:

```bash
show mac address-table
```

or

```bash
show mac-address-table dynamic
```

Look for:

- MAC addresses on ports where no device should be connected.
- Ports showing multiple MAC addresses (indicating rogue switches or hubs).
- Unknown vendors in MAC OUI (Organizational Unique Identifier).

> **Operator Note:** Multiple MACs on a port often indicates a switch/hub → rogue network expansion risk.

---

### Step 4: Physically Locate Suspect Devices

#### Methods:

* Use port documentation to identify physical location (patch panel labels, switch port mapping).
* Use tone generator and probe kit to trace cabling.
* Walk area and visually inspect → look for unmanaged switches, Wi-Fi routers, or small Ethernet devices.

> **Operator Note:** Work with facility or building management if access to secured areas is needed.

---

### Step 5: Validate Device Authorization

- Compare against asset inventory and consult network owners.
- Validate with end users if device ownership is unclear.

Example unauthorized devices:

| Device Type | Example |
|-------------|---------|
| Unmanaged Switch | $20 switch connected to expand network access |
| Wi-Fi Router | Personal router → risk of DHCP conflicts or open AP |
| Raspberry Pi / Small PC | Potential rogue C2 node |
| IP Phone with PC passthrough used improperly | Risky but not always malicious |

> **Operator Note:** Not every rogue device is malicious → accidental misconfigurations should still be removed.

---

### Step 6: Disable or Remove Rogue Devices

Options:

| Method | Description |
|--------|-------------|
| Disable switch port | Prevent rogue device from communicating |
| Physically remove device | Preferred for unknown or malicious devices |
| Block MAC address | Secondary control if physical removal not immediately possible |

Example (Cisco Switch):

```bash
configure terminal
interface FastEthernet0/10
shutdown
```

> **Operator Note:** Always coordinate with network owners → avoid accidental outages.

---

### Step 7: Document and Update Network Inventory

- Document rogue device detection and removal.
- Update switch port mappings and network diagrams.
- Inform network owners of updates and actions taken.

---

## Dependencies

* Access to network switches and routers
* Ability to run scans and pull MAC address tables
* Physical access or support for physical device location
* Network owner coordination

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Nmap | Cross-platform | Package manager | Network discovery |
| arp-scan | Linux | Package manager | Layer 2 scan |
| Switch CLI | Switches | Native | Show MAC tables |
| Netdisco | Linux | Web-based tool | Network device inventory |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain and validate network inventory and authorized device list.
- [ ] Conduct active network scans to identify all devices.
- [ ] Review switch CAM tables for unknown MAC addresses.
- [ ] Investigate and physically locate suspicious devices.
- [ ] Validate device legitimacy with network owner.
- [ ] Disable or remove rogue devices.
- [ ] Update documentation and communicate findings.

### Best Practices

- Always correlate discovered MAC addresses with vendor OUI.
- Review ports with multiple MACs carefully → often rogue switches.
- Plan detection sweeps during off-peak hours if possible.
- Establish periodic rogue device detection as part of security hygiene.

---

## References

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [arp-scan User Guide](https://linux.die.net/man/1/arp-scan)
- [Cisco Switch Port Security](https://www.cisco.com/c/en/us/products/collateral/switches/campus-lan-switches-802-11ac/white-paper-c11-740390.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator guidance and practical detection/removal procedures | Leo |
