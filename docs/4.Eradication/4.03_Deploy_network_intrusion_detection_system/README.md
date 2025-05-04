# 4.03 Deploy Network Intrusion Detection System (NIDS)

## Task
Deploy Network Intrusion Detection System (NIDS)  

## Conditions

### Deploy
IR Team supporting a mission partner or Guardnet/DoDIN enclave without appropriate Network IDS or IPS installations.  

### Evaluate
IR Team supporting a mission partner or Guardnet/DoDIN enclave with access to appropriate systems/capabilities to evaluate existing Network IDS or IPS installations.  

---

## Standards

### Deploy (in addition to Evaluate below)

- Assess mission partner or Guardnet/DoDIN enclave networks for placement of IDPS capabilities.
- Deploy IDPS capabilities where directed.
- Once deployed, conduct the Evaluate process to assess operational effectiveness.

#### Identify and prepare Detection Systems
- Find what systems are available or what is necessary to the mission (likely directed if Unit is not authorized to decide).
- Identify/Rate strengths and weaknesses of systems.
- Install necessary programs/systems critical to mission.

#### Identify or Obtain login credentials
- Obtain IP path to login.
- Obtain/create User ID to login.
- Obtain/create Password (ensure strong password creation).

#### Test accessibility
- Connect to the system with credentials.
- Report inconsistencies.

#### Develop the detection scheme
- Establish detection plan following evaluation of the network (see Evaluate process below).
- Re-evaluate and improve detection throughout the mission.

---

### Evaluate (Assess existing NIDS)

#### Identify and correlate current system to typical components and network architectures
- Build or utilize a Common Operating Picture (COP) for the team.
- See Figures 4-2 and 4-3 for reference architectures.

#### Identify/locate potential entry points
- Identify Internet entry points.
- Identify contained wireless connections.

#### Identify/locate choke points and firewalls
- Identify locations where traffic must pass through.
- Identify firewall control points and protected zones.
- Grade firewalls based on placement and coverage (per reference figures).
- Evaluate protection of zones and failure conditions (does loss of Zone A expose Zone B?).

#### Identify/locate current detection systems and sensors
- Locate detection systems on the network.
- Locate installed sensors on systems.
- Map paths from IR Team entry points to detection systems.
- Map paths from detection systems to network sensors.
- Grade sensors based on coverage of choke points and zones.

#### Identify full system maintenance schedule
- Patch schedule for network components (routers, switches).
- Patch schedule for firewalls.
- Patch/update schedule for detection systems.
- Grade frequency and availability of patch/update mechanisms.

---

### Components and Architecture (For Reference)

- Typical Components
- Network Architectures

### Security Capabilities (For Reference)

- Information Gathering Capabilities
- Logging Capabilities
- Detection Capabilities
- Prevention Capabilities

### Management (For Reference)

- Implementation
- Operation and Maintenance

> **Operator Note:** Consider both Wired and Wireless IDPS capabilities.

---

## End State

- NIDS/IDPS deployed or evaluated.
- Proper placement at critical network chokepoints.
- Detection capabilities validated.
- System integrated into monitoring workflows.

---

## Notes

- NIDS/IDPS should not impact network performance.
- Sensors should cover ingress/egress and sensitive internal zones.
- Rules should be tuned to reduce false positives.
- Consider options for wired and wireless coverage.

---

## Manual Steps

### Step 1: Select NIDS Platform (if Deploying)

| Tool | Platforms | Installation | Notes |
|------|-----------|--------------|-------|
| Suricata | Linux / BSD / Cloud | `apt install suricata` or from source | Balanced, high-performance IDS/IPS |
| Zeek | Linux / BSD / MacOS | `apt install zeek` or from source | Deep inspection and behavior analysis |
| Snort | Linux / BSD / Windows | `apt install snort` or from source | Mature signature-based detection |

> **Operator Recommendation:** Suricata is recommended for general purpose detection, while Zeek is excellent for detailed behavioral analysis.

---

### Step 2: Plan Sensor Placement

- Obtain network diagrams or request from mission owner.
- Identify ingress/egress points, inter-zone choke points, and critical data paths.
- Plan sensor placement to maximize visibility without oversaturation.

---

### Step 3: Install and Configure NIDS

#### Suricata Example

```bash
sudo apt update
sudo apt install suricata
sudo suricata-update
sudo suricata -c /etc/suricata/suricata.yaml -i eth0
```

#### Zeek Example

```bash
sudo apt update
sudo apt install zeek
sudo zeekctl deploy
```

#### Snort Example

```bash
sudo apt update
sudo apt install snort
snort -v -i eth0
```

- Configure rulesets (ET Open, Community, or custom mission-specific).
- Test detection via simulated benign activities (e.g., port scan).
- Tune rules to environment and mission.

---

### Step 4: Validate Accessibility and Operational Status

- Verify SSH or management console access.
- Confirm alert and log generation.
- Confirm integration with SIEM or central log collection.

---

### Step 5: Implement and Validate Detection Plan

- Simulate attacks (port scans, malware downloads, brute-force logins).
- Confirm alerts trigger appropriately.
- Adjust and tune based on test results and false positives.

---

### Step 6: Establish Update and Maintenance Process

- Configure automatic rule updates.

```bash
sudo suricata-update
```

- Define patch management plan.
- Schedule periodic detection plan reviews.

---

## Running Script Example (Suricata Health Check)

```bash
sudo systemctl status suricata
sudo tail -f /var/log/suricata/fast.log
```

---

## Dependencies

- Access to the network topology and infrastructure.
- Agreement and coordination with mission/network owners.
- Administrative permissions to deploy and maintain NIDS systems.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| pfSense + Snort/Suricata | BSD | Install via pfSense package manager | Embedded NIDS solution |
| Security Onion | Linux | ISO install | Turnkey IDS/SIEM deployment |
| Zeek | Linux/MacOS | apt or source | Network behavior analysis |
| Suricata | Linux | apt or source | High performance IDS/IPS |

> **Operator Note:** Consider Security Onion for full integrated monitoring when applicable.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Assess/evaluate or deploy NIDS solution.
- [ ] Identify and document network choke points and sensor placement.
- [ ] Install, configure, and validate NIDS operation.
- [ ] Tune and optimize detection for mission relevance.
- [ ] Establish update and maintenance plans.
- [ ] Integrate with enterprise monitoring and incident response workflows.

### Best Practices

- Use layered detection (behavioral + signature based).
- Continuously tune rulesets to reduce false positives.
- Ensure sensors provide visibility without performance degradation.
- Plan for failover/redundancy when possible.

---

## References

[Suricata IDS](https://suricata.io/)  
[Zeek Network Security Monitor](https://zeek.org/)  
[Security Onion](https://securityonionsolutions.com/)  
[NIST SP 800-94 IDPS Guide](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-94.pdf)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.1 | Corrected and expanded version to reintegrate original mission planning and evaluation language with operator procedures and tools | Leo |
