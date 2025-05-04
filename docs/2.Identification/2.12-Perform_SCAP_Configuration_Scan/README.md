# Perform SCAP Configuration Scan

## Task Perform a SCAP or Retina Scan of Network Configurations

## Conditions

Given an incident response workstation configured with the latest SCAP Compliance Checker, a current Open Vulnerability and Assessment Language (OVAL) file, and a copy of the configuration file for the device to be tested.

> **Operator Note:** SCAP scanning is primarily used for configuration compliance (STIG, CIS Benchmarks). It does not find vulnerabilities in software but verifies hardening status.

## Standards

* Team member checks the make, model, and software version of the device to be tested and ensures the latest OVAL file has been obtained from the manufacturer’s website or other trusted source such as [DoD Cyber Exchange](https://public.cyber.mil/stigs/scap/).  
* Team member uploads the OVAL file to the SCAP tool.  
* Team member selects the configuration file for the device to be tested in the SCAP tool.  
* Team member runs the SCAP tool and upon completion selects for output the detailed report for the scan.  
* Team member uses the report findings to research remediation steps for all findings.

> **Operator Note:** Not all SCAP benchmarks apply to every system. Select benchmarks carefully based on OS and system classification (e.g., MAC-1_Sensitive).

## End State

All configuration vulnerabilities present in the device configuration file are found.

> **Operator Note:** The scan report is the authoritative record for compliance and should be saved with incident and assessment records.

## Manual Steps

* Open SCAP (Admin Account needed).  
* Under Content, right-click SCAP Content section and select "Delete All".  
* Select Install → Browse → select SCAP 1.2 content to install.  
* Select Open → Install → ensure installed SCAP content is highlighted.  
* Under Stream Details → select MAC-1_Sensitive for each SCAP content installed.  
* Go to Options → Show Options → uncheck "Perform OCIL Scan" (reduces scan duration).  
* Set Output Options → Save Results to a Custom Directory.  
* Under Scan section → choose scan type:  
    * Local (system you are logged into)  
    * Single Remote (enter FQDN)  
    * Multiple Remote (host.txt file with DNS names)  
* Select SCAP Benchmarks (Show Content if needed) → Start Scan.  
* Results will be saved in specified folder after completion.

> **Operator Note:** Scan results will typically include XCCDF and HTML formatted outputs. Review HTML version for readability.

## Dependencies

* Latest SCAP Compliance Checker (SCC) or equivalent tool  
* SCAP content files (OVAL, XCCDF, Benchmark) downloaded from vendor or DoD Cyber Exchange

> **Operator Note:** Update SCAP content regularly to align with evolving DISA STIGs and CIS Benchmarks.

## References

[SCC 5.3 GUI Scanner](https://public.cyber.mil/stigs/scap/)  
[Cisco OVAL content](http://www.cisco.com/go/psirt)  
[OVAL Adoption Program](http://oval.mitre.org/adoption/participants.html)  
[The Security Content Automation Protocol](https://scap.nist.gov/index.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm system classification and compliance benchmark required.
- [ ] Obtain latest SCAP/OVAL content from DoD, vendor, or community sources.
- [ ] Configure scan profile appropriately (disable OCIL unless needed).
- [ ] Execute scans and export results.
- [ ] Review findings and prepare remediation recommendations.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows/Linux/macOS | SCAP Compliance Checker (SCC) | Primary GUI scanning tool for DISA STIG and USGCB |
| Linux | OpenSCAP | Open-source SCAP CLI/GUI scanner |
| Universal | CIS-CAT Lite | Lightweight configuration compliance scanner (optional alternative) |

### Alternate Commands and Examples

#### OpenSCAP Command-Line Example (Linux):

```bash
oscap xccdf eval --profile stig --results scan-results.xml --report scan-report.html /path/to/benchmark-xccdf.xml
```

#### OpenSCAP Remote Scan (via SSH):

```bash
oscap-ssh username@remote-host 22 xccdf eval --profile stig --report remote-scan.html /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

> **Operator Note:** OpenSCAP is best suited for Linux environments. SCC is better suited for Windows and enterprise mixed environments.

### Best Practices

- Always run scans during authorized assessment windows.
- Validate SCAP scan profiles to avoid unnecessary test cases (which can slow scans).
- Use host grouping and scan scheduling for large environments.
- Archive raw results and HTML reports for audit trail and after-action reports.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched SCAP/OVAL tools usage, CLI examples, and operator guidance | Leo |
