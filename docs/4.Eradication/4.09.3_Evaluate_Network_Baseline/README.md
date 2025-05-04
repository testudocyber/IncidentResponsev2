# 4.09.3 Evaluate Network Baseline (Upgraded)

## Task

Evaluate and establish a baseline of normal network activity and detect deviations that may indicate malicious behaviors such as lateral movement, beaconing, exfiltration, or command and control (C2) activity.

---

## Conditions

Given access to Security Onion, Zeek logs, Suricata alerts, full packet capture (PCAP), and knowledge of the environment’s expected network layout and mission roles.

---

## Standards

* Collect and analyze representative data to understand "normal" network traffic.
* Document common ports, protocols, hosts, destinations, and communication patterns.
* Identify deviations or anomalies that do not fit into the established baseline.
* Validate anomalies against threat intelligence and Indicators of Compromise (IOCs).

---

## End State

A network baseline is documented and deviations from normal behavior are identified and tagged for escalation or eradication actions.

---

## Notes

- Baselining allows identification of stealthy and advanced techniques which may not trigger signature-based alerts.
- Expect some unknown or ambiguous traffic — anomalies are not always malicious but must be explained.

---

## Manual Steps

### Step 1: Review Available Network Telemetry

Confirm which telemetry sources are active on Security Onion:

```bash
sudo so-status
```

- **Zeek** → Metadata of every connection (conn.log), DNS, SSL/TLS, HTTP, files.
- **Suricata** → IDS/IPS alerts (snort-like signature matches).
- **Stenographer (PCAP)** → Full packet capture.
- **Elastic/Kibana** → Easy searching and visualization.

> **Operator Note:** If Zeek is down, restart sensors before proceeding. Zeek is critical for establishing the baseline.

---

### Step 2: Capture and Identify "Normal" Network Behavior

#### Focus Areas

| Area | What to Baseline | Why |
|------|------------------|-----|
| Internal to Internal | AD, DNS, SMB, RDP, File Shares | Identify lateral movement pathways |
| Internal to External | Web, Email, VPN | Identify normal business flows |
| Inbound | VPN, Reverse Proxies | Ensure only expected services are exposed |
| Outbound to Unusual Destinations | Rare external IPs/Domains | Potential C2 or data exfiltration |

#### Example Kibana Queries:

- Find top talkers:

```text
event.dataset:zeek.conn AND network.direction:"outbound"
```

- Find DNS queries:

```text
event.dataset:zeek.dns
```

- Find SSL connections (encrypted sessions):

```text
event.dataset:zeek.ssl
```

> **Operator Note:** Record observed legitimate hosts and expected domains (office365.com, google.com, internal NTP servers).

---

### Step 3: Identify and Document Normal Services and Ports

Using Zeek conn logs:

```bash
zcat /nsm/zeek/logs/current/conn.log.gz | zeek-cut id.orig_h id.resp_h id.resp_p service
```

Example results:

```
192.168.1.10 192.168.1.1 53 dns
192.168.1.10 192.168.1.5 445 smb
192.168.1.10 8.8.8.8 443 ssl
```

Document in table format:

| Source IP | Dest IP | Port | Service | Notes |
|-----------|---------|------|---------|-------|
| 192.168.1.10 | 192.168.1.5 | 445 | SMB | Normal domain traffic |
| 192.168.1.10 | 8.8.8.8 | 443 | SSL | Expected web traffic |

---

### Step 4: Identify Abnormal Patterns and IOC Signals

#### IOC and Suspicious Signals to Search For

| Indicator Type | How to Find | Example IOC |
|----------------|-------------|-------------|
| Beaconing Behavior | Zeek conn logs → periodic, regular intervals | conn.log → Check duration/intervals |
| External Connections to Rare IPs | Zeek conn logs + threat feeds | IP addresses → known malicious IPs |
| DNS Tunneling | Zeek dns.log → very long query names | suspicious.domain.exfil.data.com |
| Non-standard Ports | Zeek conn.log → high or unexpected ports | RDP (3389) → from internal clients |
| SMB/LDAP Lateral Movement | Zeek smb.log or conn.log → host-to-host SMB traffic | Multiple internal hosts communicating unexpectedly |
| Cleartext Credentials | Zeek http.log, Suricata alerts | HTTP login forms over port 80 |
| Unusual User-Agent Strings | Zeek http.log | Custom User-Agent indicating tool-based C2 |

> **Operator Note:** Use correlation and time-series views in Kibana to look for repeat patterns or relationships between hosts.

---

### Step 5: Validate with Network Owners and Documentation

- Confirm suspicious findings with network administrators or asset owners.
- Update baseline with verified "normal" activity.
- Flag unresolved anomalies for IR escalation.

> **Operator Note:** Always ask → many false positives can be ruled out by simple questions to owners.

---

## Dependencies

* Security Onion sensors functioning (Zeek, Suricata, PCAP).
* Access to Kibana and/or CLI for log review.
* Understanding of network architecture and mission profile.
* Access to threat intelligence (MISP, commercial feeds, open-source feeds).

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Zeek | Built-in (Security Onion) | Native | Network metadata analysis |
| Wireshark | Cross-platform | Package manager | PCAP analysis |
| ELK / Kibana | Built-in (Security Onion) | Native | Search and visualization |
| Threat Intelligence Feeds | External | N/A | IOC correlation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate all telemetry sources are operational.
- [ ] Collect normal traffic data from Zeek logs and PCAP.
- [ ] Document internal, outbound, and inbound traffic patterns.
- [ ] Identify anomalies → beaconing, rare destinations, unusual ports.
- [ ] Validate anomalies with owners or mission lead.
- [ ] Update baseline documentation.

### Best Practices

- Use time-based views → stealthy C2 beaconing may only show over long periods.
- Use threat intel enrichments where possible → known bad IPs/domains can highlight hidden threats.
- Build simple playbooks for common protocols → know what SMB, DNS, SSL normally look like.

---

## References

- [Zeek Documentation](https://docs.zeek.org/en/current/)
- [Security Onion Docs](https://docs.securityonion.net/en/latest/)
- [Wireshark Display Filter Reference](https://wiki.wireshark.org/DisplayFilters)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 2.0 | Fully expanded version with IOCs, examples, and deep operator context | Leo |
