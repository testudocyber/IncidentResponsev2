# 4.09.5 Detect and Remove Rogue Wireless Access Points

## Task

Detect and remove unauthorized (rogue) wireless access points (APs) that may have been installed by attackers or unauthorized personnel to facilitate unauthorized network access.

---

## Conditions

Given access to the target environment, wireless network monitoring tools, and physical or remote ability to investigate detected access points.

---

## Standards

* Identify wireless APs operating within the environment.
* Differentiate between authorized and unauthorized (rogue) APs.
* Validate and investigate suspicious APs.
* Disable, block, or physically remove rogue APs in coordination with network owners.

---

## End State

All rogue wireless access points are identified, verified, and removed or disabled. Authorized AP inventory is validated and up to date.

---

## Notes

- Rogue APs are a significant security risk → they bypass perimeter controls.
- Not all rogue APs are malicious → some may be accidental or for convenience (ex: personal hotspots).
- Detection is both passive (scanning/listening) and active (connecting and interrogating).

---

## Manual Steps

### Step 1: Understand the Authorized Wireless Environment

Before scanning:

- Obtain a list of authorized SSIDs and AP MAC addresses from network administrators.
- Understand expected channels and wireless bands (2.4 GHz, 5 GHz, 6 GHz if applicable).
- Note physical locations of legitimate APs.

> **Operator Note:** This prevents false positives during detection.

---

### Step 2: Scan for Wireless Networks (Passive Detection)

#### Recommended Tools:

| Tool | Platform | Usage |
|------|----------|-------|
| Kismet | Linux | Passive wireless scanning |
| WiFi Explorer | macOS | GUI wireless scanner |
| Airodump-ng | Linux | CLI wireless scanner |
| NetSpot | Windows/macOS | Visual wireless survey |

#### Example: Kismet (Linux)

```bash
sudo kismet -c wlan0
```

Kismet will display:

- Detected SSIDs
- Hidden SSIDs
- MAC addresses (BSSID)
- Channel and band
- First seen/last seen timestamps

#### Example: Airodump-ng (Linux)

```bash
sudo airodump-ng wlan0
```

> **Operator Note:** Identify SSIDs or MAC addresses NOT on authorized list → these are potential rogues.

---

### Step 3: Analyze Rogue AP Indicators

Look for:

| Indicator | Explanation |
|-----------|-------------|
| SSIDs not in authorized list | Obvious rogue or misconfigured AP |
| MAC addresses not registered | Spoofed or rogue AP |
| SSIDs mimicking authorized SSIDs (Evil Twin) | Attack attempt to harvest credentials |
| Unusual channels or power levels | Attempt to hide or avoid detection |
| Hidden SSIDs with clients connected | Malicious or stealthy rogue AP |

> **Operator Note:** Hidden SSIDs with connected clients → highest priority for investigation.

---

### Step 4: Locate Rogue AP Physically (If Required)

#### Options:

* Use directional Wi-Fi antenna (Yagi) + Kismet → track signal strength.
* Use WiFi Explorer/NetSpot → heatmap mode to triangulate.
* Walk area with laptop or smartphone → observe signal strength increases.

> **Operator Note:** Coordinate with onsite personnel or physical security.

---

### Step 5: Validate and Investigate Rogue AP

For suspected rogue APs:

- Attempt to connect (if authorized to do so).
- Capture traffic using Wireshark or tcpdump:

```bash
sudo tcpdump -i wlan0 -w rogue_ap_capture.pcap
```

- Look for:
  - Unencrypted management frames
  - Unusual DHCP/HTTP/FTP services
  - C2 traffic

> **Operator Note:** Do NOT send active probes unless approved. Passive capture is preferred.

---

### Step 6: Remove or Disable Rogue AP

Options:

| Method | Description |
|--------|-------------|
| Coordinate with network owners → shutdown via managed switch or network ACL | Soft removal |
| Physically disconnect device | Hard removal |
| Use Wireless IDS/WIPS to block (if available) | Real-time block |

> **Operator Note:** Always document and confirm device removal to prevent return.

---

### Step 7: Update Wireless AP Inventory

- Add validated APs to authorized list.
- Document rogue AP detection and removal actions.
- Review and update detection methods.

---

## Dependencies

* Wireless NIC capable of monitoring mode
* Physical or remote access to environment
* List of authorized APs and network layout
* Coordination with IT or network security team

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Kismet | Linux | apt/yum | Passive detection |
| Airodump-ng | Linux | Aircrack-ng suite | Passive detection |
| WiFi Explorer | macOS | Download | Visualization and detection |
| NetSpot | Windows/macOS | Download | Visualization and detection |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain authorized AP list and understand network layout.
- [ ] Scan for APs using passive monitoring.
- [ ] Compare detected APs against authorized list.
- [ ] Analyze suspicious APs for rogue indicators.
- [ ] Locate rogue AP physically (if required).
- [ ] Investigate rogue AP → capture and analyze traffic.
- [ ] Remove rogue AP and validate removal.
- [ ] Update AP inventory and detection playbook.

### Best Practices

- Conduct regular wireless sweeps to catch short-term rogue APs.
- Work closely with physical security during physical detection.
- Avoid active interrogation unless approved → can disrupt legitimate users.
- Maintain historical records of rogue AP incidents for trend analysis.

---

## References

- [Kismet Wireless](https://www.kismetwireless.net/)
- [Aircrack-ng Suite](https://www.aircrack-ng.org/)
- [WiFi Explorer](https://www.adriangranados.com/apps/wifi-explorer)
- [Wireless Rogue AP Hunting Guide (SANS)](https://www.sans.org/blog/wireless-rogue-ap-detection-techniques/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused detection/removal procedures | Leo |
