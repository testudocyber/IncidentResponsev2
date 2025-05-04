# 4.09.9 Verify Wireless Security

## Task

Verify that authorized wireless access points (APs) and wireless network configurations adhere to security best practices to prevent unauthorized access, eavesdropping, and rogue device connections.

---

## Conditions

Given access to wireless networks, wireless access point controllers or management consoles, and wireless scanning tools.

---

## Standards

* Identify and document all authorized wireless SSIDs and APs.
* Validate that wireless networks are secured using proper encryption and authentication.
* Detect and document misconfigured or weak wireless security settings.
* Recommend and/or implement corrective actions where necessary.

---

## End State

Wireless networks are verified as secure, unauthorized or weakly secured wireless networks are identified, and corrective actions are tracked.

---

## Notes

- Weak wireless security creates entry points for attackers bypassing perimeter defenses.
- Wireless security reviews should include validation of both configuration and live wireless spectrum analysis.
- Configuration review and passive detection should be conducted together for accuracy.

---

## Manual Steps

### Step 1: Inventory Authorized Wireless Networks and Access Points

- Obtain a list of all authorized SSIDs and their security configurations (from Wireless LAN Controller (WLC) or AP management system).
- Document:
  - SSID names
  - Encryption type (WPA2, WPA3, Open, WEP)
  - Authentication method (Pre-shared key (PSK), 802.1X/EAP)
  - VLAN or network segmentation
  - AP MAC addresses and locations

Example:

| SSID | Encryption | Auth Type | VLAN | Notes |
|------|------------|-----------|------|-------|
| CorporateWiFi | WPA3 | 802.1X | VLAN 10 | Corporate devices only |
| GuestWiFi | WPA2 | PSK | VLAN 20 | Internet-only, segregated |

> **Operator Note:** SSIDs using Open or WEP encryption are HIGH RISK → flag immediately.

---

### Step 2: Perform Passive Wireless Scan for Active Wireless Networks

#### Recommended Tools:

| Tool | Platform | Usage |
|------|----------|-------|
| Kismet | Linux | Passive wireless detection |
| WiFi Explorer | macOS | GUI visualization |
| Airodump-ng | Linux | CLI-based scanning |

#### Example (Airodump-ng):

```bash
sudo airodump-ng wlan0
```

Review discovered networks:

- Compare SSIDs and BSSIDs (MAC addresses) to authorized list.
- Identify unknown SSIDs or unauthorized APs.
- Note encryption type → open or weak encryption should be flagged.

> **Operator Note:** Look for duplicate SSIDs → possible Evil Twin attacks.

---

### Step 3: Validate Encryption and Authentication Settings

#### Wireless Controller / AP Management Console

- Log into wireless controller or AP management console.
- Validate SSID security settings:

| Setting | What to Verify |
|---------|----------------|
| Encryption | WPA2 or WPA3 → avoid WEP/Open |
| Authentication | Use 802.1X for enterprise or PSK for guest |
| MAC Filtering (optional) | Enforced where applicable |
| SSID Broadcast | Hidden SSIDs do NOT equal secure → ensure proper controls in place |

> **Operator Note:** Guest networks MUST be segmented (different VLAN, no LAN access).

---

### Step 4: Detect and Investigate Rogue Clients and Unusual Activity

- Use Kismet or WLC to detect clients connected to APs.
- Investigate:

| Suspicious Activity | Action |
|---------------------|--------|
| Unauthorized client connected to corporate SSID | Validate device ownership and MAC address |
| High number of deauthentication packets | Possible wireless attack (deauth/Evil Twin) |
| Unusual MAC vendors (ex: virtualized MAC addresses) | Flag for investigation |

> **Operator Note:** If using WPA2 Enterprise, validate only managed devices can connect.

---

### Step 5: Review RF Spectrum for Unusual Signals (Optional but Recommended)

#### Tools:

| Tool | Platform | Usage |
|------|----------|-------|
| Wi-Spy / Chanalyzer | Windows/macOS | RF analysis |
| Kismet + SDR | Linux | RF signal detection |

- Look for unusual signals in 2.4/5/6 GHz bands → could indicate rogue APs or unauthorized Wi-Fi devices.

> **Operator Note:** Rogue APs may not broadcast SSIDs → RF detection helps find hidden threats.

---

### Step 6: Remediate or Escalate Issues

For any weaknesses or issues found:

| Issue | Action |
|-------|--------|
| Open/WEP networks | Migrate to WPA2/WPA3 immediately |
| Rogue AP detected | Coordinate with network owners for removal |
| Unsegmented guest networks | Implement VLAN separation |
| Unauthorized clients | Investigate and block if malicious |

> **Operator Note:** Always document proposed fixes and work through network owners or administrators.

---

## Dependencies

* Wireless NIC in monitor mode
* Access to Wireless LAN Controller / AP Management
* Physical access or remote view of wireless spectrum

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Kismet | Linux | Package manager | Passive wireless scanning |
| Airodump-ng | Linux | Aircrack-ng suite | Passive wireless scanning |
| WiFi Explorer | macOS | GUI visualization |
| Wireless LAN Controller | Web/Appliance | Native | Configuration validation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain and document authorized SSIDs and AP configurations.
- [ ] Passively scan wireless environment for all active networks.
- [ ] Compare findings to authorized list → identify unknown/unauthorized SSIDs.
- [ ] Validate encryption and authentication settings for each SSID.
- [ ] Detect and review rogue clients and unusual wireless activity.
- [ ] Recommend remediation for weak configurations or detected issues.
- [ ] Document findings and update network security plan.

### Best Practices

- Always prefer WPA3 or WPA2-Enterprise with 802.1X where possible.
- Isolate guest networks from internal systems.
- Regularly audit wireless configurations and spectrum for new threats.
- Disable unused SSIDs and hidden SSIDs unless operationally required.

---

## References

- [Wi-Fi Alliance Security Best Practices](https://www.wi-fi.org/discover-wi-fi/security)
- [Kismet Wireless](https://www.kismetwireless.net/)
- [Cisco Secure Wireless Architecture Guide](https://www.cisco.com)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with detailed operator-focused wireless security validation process | Leo |
