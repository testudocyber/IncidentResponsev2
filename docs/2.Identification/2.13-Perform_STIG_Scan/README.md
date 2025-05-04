# Perform STIG Scan

## Task Perform STIG Scan

## Conditions

Given a list of hosts with known operating systems and/or applications, the user will complete a scan using the appropriate STIG (Security Technical Implementation Guide) using an assigned tool. These standards follow using Nessus as the tool to perform the scan. SCAP (Security Content Automation Protocol) compliant STIGs should be used when possible.

> **Operator Note:** SCAP STIG scanning assesses secure configurations based on DISA, CIS, or organizational hardening standards. It does not scan for software vulnerabilities.

## Standards

* Identify appropriate STIG(s) to be used to analyze.  
  * This is dependent upon the Network Owner disclosing their complete IT Inventory.
* Scan using the chosen STIG using Nessus.  
  * These will likely be conducted several times, one per software target.
* Export results of the scan.  
  * Standardize naming convention to include Software Target and Time/Date.

> **Operator Note:** Use DoD-approved or NIST SCAP content whenever possible for best results.

## End State

Results of the STIG scan baselines the host and identifies any security control weaknesses.

> **Operator Note:** Exported scan reports should be saved, backed up, and shared securely with incident and compliance teams.

## Manual Steps

### CLI-based Nessus (Legacy / Automated Process)

* Navigate to the Mission Directory:

```bash
cd /ios/data/assess/<MISSION_DIR>
```

* View discovered targets:

```bash
ls –la ./collect/targ
```

* Run a Nessus STIG scan against each OS type:

```bash
nessus.sh localhost win_<OS Type>_stig ./collect/targ/<OS Type>.ips
```

* View the scan output:

```bash
ls -la ./collect/nbe
```

* Convert the `.nbe` file to a tabular report:

```bash
nbeSTIG2tab.sh ./collect/nbe/<timestamp>-win_<OS Type>_stig.nbe > win_<OS Type>_stig.tab
```

* Move the result into the report directory:

```bash
mv ./win_<OS Type>_stig.tab ./report/
```

### Nessus Command Line Switches (Quick Reference)

```
-I Input file for report format conversion
-o Output file for report format conversion
-p List plugins
-P Show plugin/server preferences
-S SQL syntax output
-T Report output format (nbe, html, xml, text, etc)
```

### Nessus Web Interface (Preferred Modern Method)

* Download certified SCAP content ZIP file (from NIST or DoD Cyber Exchange).
* Create a new Scan → Use SCAP Compliance Audit template.
* Upload SCAP zip in the "Active SCAP Components" section.
* Choose appropriate benchmark/profile.
* Add scan targets and credentials.
* Launch scan and review results after completion.

> **Operator Note:** Nessus Professional is recommended for larger enterprise scanning. Free version limited to 16 IPs.

### Downloading SCAP Content

SCAP content bundles can be downloaded for use:

- [NIST SCAP Content Repository](https://nvd.nist.gov/ncp/repository)
- [DISA STIG Content](https://public.cyber.mil/stigs/)

### Exporting Scan Results

In Nessus Web UI:

- Export as `.nessus` format to allow portability and re-import.
- Name convention: `<scan_name>_<scan_ID>.nessus`

## Running Script

N/A (scripts embedded in Nessus or manual commands)

## Dependencies

* Nessus or Nessus Pro (or equivalent SCAP scanner)

> Nessus Free version allows up to 16 IPs, Professional version allows enterprise-scale scanning.

* SCAP/STIG Benchmark Content

## Other available tools

* [DISA SCAP Compliance Checker (SCC)](https://public.cyber.mil/stigs/) — DISA-approved SCAP scanner  
* [OpenSCAP](https://www.open-scap.org/) — Linux-based command-line SCAP scanning and reporting  
* [SCAP Workbench](https://www.open-scap.org/tools/scap-workbench/) — Desktop-based SCAP scanning for small scale use

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate targets and obtain OS/application inventory.
- [ ] Download and validate SCAP content bundles.
- [ ] Configure Nessus or DISA SCC for scan profiles.
- [ ] Execute scans and monitor results.
- [ ] Export, archive, and distribute results securely.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nessus / Nessus Pro | Primary tool for SCAP/STIG scanning (GUI + CLI) |
| Windows/Linux | DISA SCAP Compliance Checker (SCC) | DISA-approved alternative |
| Linux | OpenSCAP / SCAP Workbench | Open-source STIG scanning (optional) |

### Alternate Commands and Examples

#### OpenSCAP example (Linux):

```bash
oscap xccdf eval --profile stig --results result.xml --report result.html /path/to/benchmark.xml
```

#### SCAP Workbench example:

```plaintext
Open SCAP Workbench GUI
Load XCCDF or DataStream
Select profile → Run Scan → Export Report
```

### Best Practices

- Use standardized naming for all exported scan artifacts.
- Schedule scans during low-use windows (as they may impact performance).
- Validate and archive raw `.nessus` or `.xml` files for auditability.
- Review findings carefully with system owners for applicability.

---

## References

[Tenable Nessus Trial](https://www.tenable.com/try)  
[Tenable SCAP Guide](http://static.tenable.com/documentation/Nessus_6.1_SCAP_Assessments.pdf)  
[DISA SCAP Tool](https://public.cyber.mil/stigs/)  
[Stig Viewer](https://www.stigviewer.com/stigs)  
[NIST SCAP Content](https://nvd.nist.gov/ncp/repository)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched Nessus SCAP scan operator guidance and alternate tooling | Leo |
