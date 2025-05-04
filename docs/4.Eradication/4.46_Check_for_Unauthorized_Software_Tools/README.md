# 4.46 Check for Unauthorized Software and Tools

## Task Identify and Remove Unauthorized or Malicious Software Installed by Attackers

## Conditions

Given access to impacted systems and administrative privileges, the operator will enumerate installed software and tools, investigate suspicious or unauthorized programs, and remove any attacker-installed software to ensure eradication is complete.

> **Operator Note:** Attackers often install tools (credential dumpers, remote access software, persistence frameworks) to aid in lateral movement and post-compromise activities. These must be thoroughly removed.

## Standards

* Team member enumerates all installed software and executables across impacted hosts.
* Team member validates each item against an approved software baseline or with system owners.
* Team member removes unauthorized or malicious software.
* Team member documents findings, actions taken, and retains logs or forensic evidence.
* Team member validates removal and confirms system integrity.

## End State

Unauthorized and attacker-installed software has been fully identified, validated, removed, and documented across the environment.

---

## Notes

- Some software may appear benign (e.g., "Remote Utilities", "7zip") but may have been used maliciously → context matters.
- Use caution when removing software to avoid disruption of legitimate services.
- Retain forensic artifacts for post-incident analysis.

---

## Manual Steps

### Step 1: Enumerate Installed Software

#### Windows

```powershell
Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, InstallDate
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, InstallDate
wmic product get name, installlocation
```

#### Linux

```bash
dpkg -l   # Debian/Ubuntu
rpm -qa   # RHEL/CentOS
find / -type f -executable -exec ls -la {} \; 2>/dev/null | grep -i suspicious
```

#### macOS

```bash
system_profiler SPApplicationsDataType
ls /Applications
find /usr/local/bin /usr/bin /bin -type f -perm +111
```

> **Operator Note:** Focus on unusual install paths (AppData, /tmp, user folders) and unknown publishers.

---

### Step 2: Investigate Suspicious Software

Indicators of suspicious or unauthorized software:

- Installed during the attack timeframe
- Unusual publisher or no publisher info
- Located in user-space or temporary directories
- Known hacking, remote access, or admin tools used by attackers (e.g., Mimikatz, PsExec, AnyDesk)

> **Operator Note:** Cross-reference with threat intelligence sources and MITRE ATT&CK for known attacker tooling.

---

### Step 3: Remove Unauthorized or Malicious Software

#### Windows

```powershell
Get-WmiObject -Class Win32_Product | Where-Object {$_.Name -like "*SuspiciousSoftware*"} | ForEach-Object { $_.Uninstall() }
```

OR via Control Panel / Apps & Features for GUI removal.

#### Linux

```bash
sudo apt remove suspiciouspackage
sudo yum remove suspiciouspackage
sudo rm /path/to/suspicious/file
```

#### macOS

```bash
sudo rm -rf /Applications/SuspiciousApp.app
brew uninstall suspiciouspackage
```

> **Operator Note:** Validate removal after using package managers or manual methods.

---

### Step 4: Validate Removal and Monitor

- Re-enumerate installed software list
- Confirm no related services, scheduled tasks, or residual files remain
- Monitor logs for reinstallation attempts or indicators of persistence

---

## Running Script (Windows - Enumerate Installed Apps and Save to File)

```powershell
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Select-Object DisplayName, InstallDate |
Export-Csv InstalledSoftware.csv -NoTypeInformation
```

> **Operator Note:** Retain lists before and after eradication for recordkeeping.

---

## Dependencies

* Administrative/root access on hosts
* System baseline (if available) or coordination with system owners
* Threat intelligence for identifying attacker tooling
* IR lead approval for software removal actions

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Autoruns (Sysinternals) | Windows | Download from Sysinternals | Detect non-standard software loading at startup |
| PowerShell | Windows | Built-in | Enumerate, remove software |
| Package managers (apt, yum, brew) | Linux/macOS | Built-in | Remove installed software |
| EDR/XDR Platforms | Cross-platform | Enterprise deployment | Detect and alert on unauthorized software |

> **Operator Note:** Autoruns and EDR solutions are highly effective for detecting hidden/unauthorized software.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate installed software and tools across all impacted hosts.
- [ ] Validate findings against approved baseline or system owner input.
- [ ] Investigate and document suspicious or unauthorized software.
- [ ] Remove unauthorized or malicious software safely.
- [ ] Validate removal and monitor for reappearance.
- [ ] Document actions and retain before/after software inventory.

### Best Practices

- Remove software during eradication when risk of disruption is low.
- Prefer automated removal tools where available to ensure completeness.
- Coordinate with system/application owners before removing borderline software.
- Include software audit results in post-incident documentation and lessons learned.

---

## References

[Windows PowerShell Get-WmiObject for Installed Software](https://docs.microsoft.com/en-us/powershell/scripting/samples/get-installed-programs-using-powershell)  
[Linux Package Management (APT/YUM)](https://wiki.debian.org/Teams/Apt)  
[macOS Command Line Tools](https://developer.apple.com/support/command-line-tools/)  
[Sysinternals Autoruns](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for checking/removing unauthorized software and attacker tools with platform-specific guidance | Leo |
