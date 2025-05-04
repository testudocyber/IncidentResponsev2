# 4.31 Run Vulnerability Scan

## Task Run Vulnerability Scan on Impacted and At-Risk Hosts

## Conditions

Given access to a vulnerability scanning platform and the environment is contained and stabilized, the operator will execute comprehensive vulnerability scans on impacted and surrounding systems to identify unpatched software, misconfigurations, or weaknesses that could be exploited again.

> **Operator Note:** Running a vulnerability scan during eradication ensures the environment is hardened before recovery and normal operations resume. Vulnerability scanning should include both impacted systems and adjacent/related assets.

## Standards

* Team member selects and configures a vulnerability scanning tool appropriate to the environment.
* Team member conducts credentialed scans whenever possible for completeness.
* Team member scans all previously impacted systems, systems in proximity, and critical infrastructure.
* Team member reviews findings and coordinates with system owners to plan remediation.
* Team member documents vulnerability scan results and actions taken.

## End State

All vulnerable and misconfigured systems are identified and scheduled for remediation before recovery operations proceed.

---

## Notes

- Credentialed scans (using local administrator/root access) provide deeper and more reliable results.  
- Scanning should be coordinated with IT and system owners to avoid unintentional disruptions.  
- Scans should be retained as part of incident record for future lessons learned.

---

## Manual Steps

### Step 1: Select and Prepare Vulnerability Scanner

Recommended Open-Source and Commercial Tools:

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| OpenVAS / Greenbone | Linux | `apt install openvas` → `gvm-setup` | Web UI or CLI → Full network scan |
| Nessus Essentials | Cross-platform | [Download](https://www.tenable.com/products/nessus/nessus-essentials) | Web UI → Local and remote host scans |
| Nmap with Vulners NSE script | Cross-platform | `apt install nmap` or `brew install nmap` | `nmap -sV --script vuln <target>` |
| Qualys Vulnerability Scanner (commercial) | Cloud | Subscription required | Web UI → Schedule and review scans |

> **Operator Note:** Nessus Essentials (free) and OpenVAS are highly recommended for small/mid environments due to ease of use and no-cost licensing.

---

### Step 2: Define Scan Targets

- Impacted hosts identified during containment
- Critical servers and applications
- Systems adjacent to impacted networks (lateral movement potential)
- Domain controllers and infrastructure systems

---

### Step 3: Configure and Execute Scan

#### Example - Nessus Essentials (Web UI)

- Launch scanner and create new scan → “Advanced Scan”
- Enter target IPs or hostnames
- Configure scan credentials (administrator/root credentials if approved)
- Configure scanning policies (enable thorough plugin set)
- Start scan and monitor

#### Example - OpenVAS

```bash
gvm-cli socket --gmp-username admin --gmp-password password "start_task task_id"
```

#### Example - Nmap

```bash
nmap -sV --script vuln 192.168.1.100
```

> **Operator Note:** Nmap + Vulners script is good for quick snapshot, but full vulnerability scanners are preferred for enterprise use.

---

### Step 4: Review Scan Results

- Filter for Critical and High severity vulnerabilities
- Note unpatched software, insecure services, and configuration issues
- Coordinate with IT to plan remediation timeline
- Validate false positives where necessary

---

### Step 5: Document and Report

- Export scan reports (PDF, CSV, JSON as supported)
- Log high severity findings in IR tracking/ticketing system
- Document remediation plan and ownership per asset

---

## Running Script (Nmap + Vulners Quick Scan Example)

```bash
nmap -sV --script vulners 192.168.1.0/24 -oA vulnscan_results
```

> **Operator Note:** Generates XML and grepable output for easy parsing and documentation.

---

## Dependencies

* Vulnerability scanner installed and licensed (if required)
* Network access to targets
* Administrative credentials (for credentialed scans)
* Coordination with system owners and IT operations
* IR lead approval to conduct scans (ensure scans will not disrupt operations)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| OpenVAS | Linux | `apt install openvas` → `gvm-setup` | Full-featured vulnerability management |
| Nessus Essentials | Cross-platform | Download from vendor | User-friendly, widely adopted scanner |
| Nmap Vulners Script | Cross-platform | `nmap --script vulners` | Fast, script-based scan for known CVEs |
| Qualys / Rapid7 | Enterprise | Vendor provided | Comprehensive enterprise solutions |

> **Operator Note:** Choose scanner based on environment size, criticality, and licensing availability.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Select and deploy vulnerability scanning solution.
- [ ] Conduct credentialed scan on impacted and related systems.
- [ ] Validate and analyze scan findings.
- [ ] Coordinate remediation with system owners.
- [ ] Document results and remediation plan in IR record.
- [ ] Retain scan results for lessons learned and reporting.

### Best Practices

- Prefer credentialed scans for completeness.
- Avoid scanning sensitive systems without coordination.
- Perform post-remediation re-scans to confirm vulnerability closure.
- Integrate vulnerability scan into post-eradication validation steps.

---

## References

[Nessus Essentials - Free Vulnerability Scanner](https://www.tenable.com/products/nessus/nessus-essentials)  
[OpenVAS / Greenbone Community Edition](https://www.greenbone.net/en/community-edition/)  
[Nmap NSE Scripts - Vulners](https://nmap.org/nsedoc/scripts/vulners.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for running vulnerability scans, tool usage, operator checklist | Leo |
