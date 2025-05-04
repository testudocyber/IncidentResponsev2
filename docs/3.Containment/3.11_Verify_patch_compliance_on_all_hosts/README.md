# Verify Patch Compliance on All Hosts

## Task Verify Patch Compliance on all Host Systems

## Conditions

Given domain credentials with the appropriate permissions, and a vulnerability scanner or OS-native update utility, perform vulnerability scans and patch audits from an incident response workstation.

> **Operator Note:** Patch management and validation is critical during incidents. Attackers often leverage missing patches for initial access and lateral movement. Verifying patch compliance ensures hosts are not vulnerable to known exploits.

## Standards

* Detect. Use approved scanning tool to scan systems for missing security patches.  
* Assess. Determine severity of missing patches and assess the risk to the enclave.  
* Approval. Obtain appropriate enclave approval prior to remediation procedures.  
* Document. Record actions and approval per change management procedures.  
* Acquire. Download patches if not addressed by existing security measures.  
* Test. Apply patch to test system and validate before production deployment.  
* Deploy. Apply patches to production hosts, validate apps are unaffected, and roll back if necessary.

## End State

All enclave target systems have been scanned and patch compliance verified. Any deficiencies are tracked and approved for remediation or accepted risk.

---

## Notes

- OpenVAS and MBSA provide automated scanning capabilities.  
- Native OS tooling can validate patch status at the host level.  
- Use a combination of methods to achieve confidence across OS platforms.

---

## Manual Steps

### Vulnerability Scanning

#### OpenVAS (Linux / Cross-platform)

* Access web UI at `https://x.x.x.x:9392`  
* Enter target IPs → `Start Scan`  
* Review results → export report → verify against SOP/policy

#### MBSA (Windows Legacy / GUI)

* Run MBSA → `Scan a computer` or `Scan more than one computer`  
* Select `Check for security updates` only  
* Start scan → review report → download missing updates  
* Validate compliance against SOP

#### MBSA (Command Line)

```bat
mbsacli /i 127.0.0.1 /n OS+IIS+SQL+PASSWORD
mbsacli /c domain\machinename /n OS+IIS+SQL+PASSWORD
mbsacli /r 192.168.0.1-192.168.0.254 /n OS+IIS+SQL+PASSWORD
mbsacli /d NameOfMyDomain /n OS+IIS+SQL+PASSWORD
```

---

### OS Native Methods

#### Windows PowerShell (Modern alternative to MBSA)

```powershell
Get-HotFix
```

> **Operator Note:** Use `Get-HotFix` to list installed updates and patch level.

---

#### Ubuntu / Debian based:

```bash
apt-get update
apt-get upgrade
apt-get dist-upgrade
```

---

#### Red Hat Enterprise Linux 2.1-4:

```bash
up2date
up2date-nox --update
up2date -u <PACKAGE NAME>
```

#### Red Hat Enterprise Linux 5:

```bash
pup
```

#### Red Hat Enterprise Linux 6:

```bash
yum update
yum list installed <PACKAGE NAME>
yum install <PACKAGE NAME>
yum update <PACKAGE NAME>
```

---

#### Kali:

```bash
apt-get update && apt-get upgrade
```

---

## Running Script (Example Script for Windows Hosts)

```powershell
# Pull patch information from multiple Windows hosts
$computers = @("Host1", "Host2", "Host3")

foreach ($computer in $computers) {
    Get-HotFix -ComputerName $computer | Select-Object PSComputerName, HotFixID, InstalledOn | Export-Csv ".\PatchStatus_$computer.csv" -Append -NoTypeInformation
}
```

> **Operator Note:** This script pulls hotfix lists from remote hosts and saves them to CSV.

---

## Dependencies

* Scanning tool (OpenVAS, MBSA, Nessus, etc.)
* OS-native update utilities (apt, yum, PowerShell)
* Domain Admin credentials or local admin credentials
* Access to enclave patch repositories or internet (for patch download)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| OpenVAS / Greenbone | Cross-platform | Full vulnerability scanning |
| MBSA (Legacy) | Windows | Patch auditing (deprecated but useful for legacy) |
| PowerShell (Get-HotFix) | Windows | Simple local/remote patch status |
| WSUS | Windows Enterprise | Patch deployment + compliance |
| SCCM (MEMCM) | Windows Enterprise | Enterprise patch management |
| Qualys / Nessus | Cross-platform | Enterprise vulnerability scanning |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Identify scope and OS types of all hosts.
- [ ] Select appropriate scanning and auditing tools.
- [ ] Perform scans and local patch audits.
- [ ] Validate scan output against organization SOP.
- [ ] Document deficiencies and track in remediation plan.
- [ ] Obtain enclave approval before remediation.
- [ ] Apply/test patches and verify resolution.

### Best Practices

- Use OpenVAS or Qualys/Nessus for full environment audit.
- Use OS-native tooling for quick validation.
- Validate against vulnerability disclosure dates → prioritize critical patches.
- Document all exceptions and approved risk decisions.

---

## References

[OpenVAS Setup Instructions](http://tools.kali.org/vulnerability-analysis/openvas-scanner)  
[OpenVAS Documentation](http://docs.greenbone.net/index.html#user_documentation)  
[Microsoft Baseline Security Analyzer](https://technet.microsoft.com/en-us/security/cc184924.aspx)  
[NIST SP 800-42 Guidelines on Network Security Testing](http://csrc.nist.gov/publications)  
[NIST SP 800-40 Creating a Patch and Vulnerability Management Program](http://csrc.nist.gov/publications)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 2.0 | Full original + enriched with multi-OS operator guidance, PowerShell, alternate tooling, checklist and best practices | Leo |
