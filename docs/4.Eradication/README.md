# 4. Eradication

## Task Overview

## Purpose

During the eradication phase, the objective is to **completely remove attacker presence** and **restore systems to a clean, trusted state**. This phase follows successful containment and ensures that any persistence mechanisms, malware, or residual attacker artifacts are eliminated to prevent re-compromise.

> **Operator Note:** Eradication is not just about removing malware — it requires methodically identifying and eliminating ALL artifacts and attacker footholds across hosts, network infrastructure, accounts, and applications.

---

## Standards

* Team members identify and document all attacker-controlled accounts, malware, rootkits, scripts, unauthorized tools, and backdoors.
* Team members remove malicious or unauthorized software and artifacts from all impacted systems.
* Team members clean and restore systems to a trusted state when necessary (rebuilds may be required).
* Team members validate that eradication steps were successful through scanning, host validation, and network monitoring.
* Team members document all eradication actions in the incident record.
* Team members receive approval from IR lead before transitioning to recovery.

---

## End State

All attacker artifacts and malicious access mechanisms have been eliminated. Systems and accounts have been cleaned, validated, and are ready to be transitioned to recovery operations.

---

## Notes

- **Persistence must be fully eliminated.** Missed scheduled tasks, registry keys, rootkits, or user accounts can lead to re-compromise.
- **Rebuild vs Clean Decision:** Some systems may need to be rebuilt or reimaged if assurance of cleanliness is not achievable.
- **Document everything:** Every eradication action should be logged, including validation results.

---

## Eradication Manual Checklist

### Host-Level

- [ ] Identify and remove malicious software, scripts, and tools.
- [ ] Disable and remove unauthorized user accounts.
- [ ] Remove persistence mechanisms (scheduled tasks, services, registry keys).
- [ ] Confirm system drivers and kernel are clean (anti-rootkit scans).
- [ ] Validate that security software (EDR, antivirus) is functional and up to date.

### Network-Level

- [ ] Confirm network indicators (C2, DNS, IPs) are no longer active.
- [ ] Remove temporary containment blocks and rules as appropriate.
- [ ] Verify network segmentation is in place where required.

### Enterprise/Domain-Level

- [ ] Remove attacker-created AD objects, accounts, and permissions.
- [ ] Validate GPO, DNS, and enterprise services integrity.
- [ ] Confirm all credentials used during incident are reset or rotated.

---

## Recommended Tools and Usage

| Tool | Platform | Installation | Usage Example |
|------|----------|--------------|---------------|
| Sysinternals Suite (Autoruns, Process Explorer) | Windows | Download from https://learn.microsoft.com/en-us/sysinternals/downloads/ | Autoruns.exe → detect and remove persistence |
| Malwarebytes / ESET / Windows Defender Offline | Windows | Install from vendor site | Full disk scans to remove malware |
| Velociraptor | Windows/Linux/Mac | Install via https://velociraptor.app/ | Query endpoint artifacts (scheduled tasks, processes, registry) |
| PowerShell | Windows | Built-in | `Get-ScheduledTask`, `Remove-Item` |
| YARA | Cross-platform | Install via pip or binary release | Scan for malware indicators (`yara -r rules.yar /path`) |
| ClamAV | Linux/Mac | `apt-get install clamav` or `brew install clamav` | `clamscan -r /` for malware scanning |
| GRR Rapid Response | Cross-platform | https://github.com/google/grr | Enterprise artifact collection and validation |

> **Operator Note:** Always ensure tools used in eradication are sourced from trusted repositories and validated before use on production hosts.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Use layered detection (EDR, AV, manual review) to identify malicious artifacts.
- [ ] Prefer clean removal methods (AV cleaning, tool removal) before considering rebuild.
- [ ] Validate removal by rescanning and checking persistence locations.
- [ ] Document each eradication action performed and validate success.
- [ ] Coordinate closely with IT/system owners during disruptive actions.

### Best Practices

- Eradication should be carefully planned to avoid unintentional service disruption.
- Involve IT and system owners in rebuild decisions.
- Ensure IR team validates success through review and scanning before closing eradication.
- Do not rush → incomplete eradication can lead to reinfection.

---

## References

[NIST SP 800-61 Rev. 2 - Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
[Sysinternals Suite](https://learn.microsoft.com/en-us/sysinternals/downloads/)  
[Velociraptor Incident Response Tool](https://velociraptor.app/)  
[GRR Rapid Response](https://github.com/google/grr)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Generated eradication phase introduction and operator guide | Leo |
