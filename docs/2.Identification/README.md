# Identification Phase Overview

## Purpose

Develop situational awareness and identify potential incidents by leveraging available detection methods, tools, and techniques. Accurate identification supports rapid response and minimizes impact.

## Standards

- Understand the mission partner’s business environment and key assets.
- Establish detection methodologies using available tools and telemetry.
- Collect and analyze data from multiple sources to detect suspicious activity.
- Validate potential incidents before declaring an incident.
- Record all identified incidents in accordance with incident handling procedures.

## Procedural Steps (Checklist)

### Understand Business Environment and Network Layout

- [ ] Review organizational processes and critical business functions.
- [ ] Obtain and review network diagrams and asset inventories.
- [ ] Conduct site survey if necessary (see Task 2.01).

### Establish Detection Methods

- [ ] Review existing detection sources:
  - IDS/IPS
  - Antivirus / EDR
  - Network firewall and proxy logs
  - DNS and DHCP logs
- [ ] Deploy and configure additional detection tools if required.

### Collect and Correlate Data

- [ ] Gather logs and telemetry from all available sources.
- [ ] Use correlation tools (SIEM, grep, Elastic, Splunk) to identify patterns.

### Validate Potential Incidents

- [ ] Analyze data using playbooks and IOC repositories.
- [ ] Confirm activity meets incident declaration criteria.
- [ ] Avoid false positives.

### Declare and Document

- [ ] Record confirmed incidents in the Incident Tracking System.
- [ ] Notify stakeholders and move to containment.

## Tools and Resources

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | Wireshark | Packet capture and analysis |
| Windows/Linux/macOS | Nmap | Host discovery and service enumeration |
| Windows/Linux | OSQuery | Endpoint telemetry collection |
| All | Sysmon | System activity logging |
| Linux/macOS | tcpdump | Network capture |
| Windows/Linux/macOS | Velociraptor | Endpoint artifact collection |

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Initial upgraded version | Leo |
