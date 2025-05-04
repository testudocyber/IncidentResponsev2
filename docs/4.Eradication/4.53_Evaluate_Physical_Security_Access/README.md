# 4.53 Evaluate Physical Security and Physical Access

## Task

Assess the physical security posture of the environment to identify unauthorized physical access risks, vulnerabilities in physical controls, and signs of potential compromise through physical means.

---

## Conditions

Given access to the physical premises, coordination with building security and IT staff, and knowledge of organizational physical security policy.

---

## Standards

* Perform a thorough physical security review of all critical IT infrastructure and workspaces.
* Identify unauthorized access points, weak physical security controls, and potential indicators of physical tampering or compromise.
* Validate controls are in place and working (locks, cameras, badge systems).
* Document and report all deficiencies or anomalies to site owners.

---

## End State

All physical security risks are identified, documented, and escalated. Unauthorized access risks are mitigated or removed.

---

## Notes

- Physical security failures can allow attackers to bypass all technical controls.
- Physical plant vulnerabilities (wiring closets, server rooms) are priority targets.
- Insider threats often exploit poor physical access controls.

---

## Manual Steps

### Step 1: Review Access Control Mechanisms

#### Areas to check:

| Location | What to Look For |
|----------|------------------|
| Exterior Doors | Secure locks, badge readers, alarms |
| Reception Area | Visitor check-in process |
| Data Centers / Server Rooms | Electronic locks, CCTV coverage, restricted badge access |
| Wiring Closets | Locked doors, no piggybacking |
| Work Areas | Workstations unattended, no passwords/screensavers active |

> **Operator Note:** Review badge access lists to ensure only authorized personnel have access.

---

### Step 2: Validate Monitoring and Surveillance Systems

#### Review:

- CCTV camera locations → ensure coverage of critical areas (entrances, server rooms).
- Retention of footage → minimum 30 days recommended.
- Motion detectors or intrusion alarms → confirm operational.

> **Operator Note:** If footage cannot be reviewed or is unavailable → log as critical deficiency.

---

### Step 3: Conduct Physical Walkthrough and Inspection

#### Observe:

- Tailgating and unauthorized access → follow behind someone entering.
- Unlocked doors to critical areas → wiring closets, MDF/IDF rooms, network cabinets.
- Unattended or abandoned equipment.
- Unusual or unauthorized hardware:
  - USB drops
  - Rogue Wi-Fi APs
  - Laptops/Raspberry Pis plugged into switch ports

> **Operator Note:** Use combination of visual inspection and local network scans to detect rogue devices.

---

### Step 4: Review Access Logs and Visitor Records

#### Collect:

- Badge reader logs → identify unauthorized or suspicious entries.
- Visitor sign-in sheets → validate against badge access.
- Staff rosters → verify individuals were authorized to be onsite.

> **Operator Note:** Look for access outside of normal business hours or into restricted areas.

---

### Step 5: Interview or Coordinate with Facility Staff

- Ask about recent visitors, contractors, or vendors.
- Verify cleaning crew and maintenance staff access restrictions.
- Validate procedures for lost or deactivated badges.

> **Operator Note:** Cleaning and maintenance staff often overlooked → insider risk.

---

### Step 6: Identify and Report Deficiencies

Example issues to flag:

| Deficiency | Example |
|------------|---------|
| Unlocked wiring closet | Open door in hallway with access to network switch |
| No camera coverage | Blind spots near server racks |
| Unlogged visitor | Delivery contractor with no sign-in |
| Abandoned USB drive | Possible malicious device left intentionally |

> **Operator Note:** Work with site security to immediately secure critical deficiencies.

---

## Dependencies

* Onsite access or facility escort
* Cooperation with physical security or building management
* Facility maps and access policies

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Security Cameras / DVR | Physical | Site managed | Video review |
| Badge Reader Logs | Physical | Site managed | Access audit |
| Netspot / WiFi Explorer | Windows/macOS | Passive wireless scan | Detect rogue APs |
| Mobile phone / laptop | Cross-platform | On person | Detect open ports/APs, visual inspection |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain access and perform walk-through of all critical areas.
- [ ] Validate locks, badge readers, alarms, and CCTV systems are operational.
- [ ] Review and correlate access control and visitor logs.
- [ ] Identify unauthorized access methods or weaknesses.
- [ ] Physically locate and assess any unauthorized devices.
- [ ] Document all findings and coordinate remediation.

### Best Practices

- Look for social engineering attempts (tailgating, friendly visitors).
- Challenge unknown individuals politely but firmly.
- Perform sweeps at multiple times of day (peak vs off hours).
- Collaborate with facility staff → they often notice odd patterns.

---

## References

- [NIST SP 800-53 PE Family (Physical and Environmental Protection)](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)
- [CISA Physical Security Measures](https://www.cisa.gov/physical-security)
- [OSHA Facility Security Guidelines](https://www.osha.gov)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused physical security evaluation process | Leo |
