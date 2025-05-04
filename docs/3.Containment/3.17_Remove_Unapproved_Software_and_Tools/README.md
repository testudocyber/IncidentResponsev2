# Remove Unapproved Software and Tools

## Task Remove Unapproved or Malicious Software and Tools

## Conditions

Given access to compromised or at-risk systems with local administrator/root permissions, and provided with an approved software baseline or whitelist, the operator will identify and remove unauthorized software or attacker tools to prevent persistence or further exploitation.

> **Operator Note:** Unapproved or malicious tools can provide threat actors with remote access, credential theft capabilities, or data exfiltration paths. During containment, unauthorized software should be removed or disabled carefully to preserve forensic evidence and avoid business disruption.

## Standards

* Team member inventories installed software and running processes to identify unauthorized tools.  
* Team member confirms unauthorized or suspicious nature of software with IR lead and/or enclave owner.  
* Team member removes, disables, or isolates identified software.  
* Team member preserves forensic copies if required.  
* Team member documents removal actions in IR log.

## End State

All unapproved or attacker-deployed software is removed or isolated from the environment, and removal actions are documented and validated.

---

## Notes

- Some attacker tools are memory-only → always supplement with memory capture before removal.  
- Coordinate software removal with enclave/system owners to prevent loss of business-critical functions.  
- Malware and attacker tools may resist removal or have persistence → monitor systems post-removal.

---

## Manual Steps

### Step 1: Identify Installed Software and Tools

#### Windows - Installed Programs (PowerShell)

```powershell
Get-WmiObject -Class Win32_Product | Select-Object Name, Version
```

#### Windows - Running Processes

```powershell
Get-Process | Select-Object Name, Path
```

> **Operator Note:** Look for non-standard paths (AppData, Temp, C:\Users).

---

#### Linux - Installed Packages (Debian/Ubuntu)

```bash
dpkg -l
```

#### Linux - Installed Packages (RedHat/CentOS)

```bash
rpm -qa
```

#### Linux - Running Processes

```bash
ps aux | less
```

> **Operator Note:** Use `lsof` or `netstat` to identify software with network activity.

---

#### macOS - Installed Applications

```bash
system_profiler SPApplicationsDataType
```

#### macOS - Running Processes

```bash
ps aux
```

---

### Step 2: Investigate and Confirm

- Validate unknown or unapproved software against approved software lists.
- Coordinate with IR lead or enclave owner for final approval to remove.

---

### Step 3: Disable or Remove Software

#### Windows - Uninstall

```powershell
Get-WmiObject -Class Win32_Product -Filter "Name='ToolName'" | ForEach-Object { $_.Uninstall() }
```

#### Windows - Manual Removal (Advanced)

- Stop service or process
- Delete application folders
- Clean up registry if required (Regedit)

---

#### Linux - Uninstall Package

```bash
apt-get remove --purge packagename
yum remove packagename
```

#### Linux - Remove Binary / Custom Tools

```bash
rm /path/to/binary
```

> **Operator Note:** Retain a forensic copy before removal if necessary.

---

#### macOS - Uninstall (manual)

```bash
sudo rm -rf /Applications/Tool.app
```

---

### Step 4: Validate Removal

- Verify process is no longer running
- Verify application no longer exists
- Review startup mechanisms (autoruns, crontab, launchctl)

---

## Running Script (Windows - Identify Suspicious Software)

```powershell
Get-WmiObject -Class Win32_Product | Where-Object { $_.Name -notmatch "Microsoft|Adobe|VMware|Cisco" } | Select-Object Name, Version
```

> **Operator Note:** Adjust exclusions based on organization whitelist.

---

## Dependencies

* Local Administrator or root access  
* Knowledge of approved software baseline  
* Coordination with enclave/system owners  
* Backup or forensic tools (optional)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell + WMI | Windows | Installed software and removal |
| Autoruns (Sysinternals) | Windows | Identify software running at startup |
| dpkg, rpm, yum, apt | Linux | Package management and removal |
| ps, lsof, netstat | Linux/macOS | Running process identification |
| Manual removal | All | Remove binaries or application files |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Inventory installed software and running processes.
- [ ] Validate against known good / approved software list.
- [ ] Obtain approval before removal.
- [ ] Disable or uninstall unapproved tools.
- [ ] Retain forensic copy if required.
- [ ] Validate removal and document actions.

### Best Practices

- Do not delete unless necessary → prefer disable or isolate where possible.
- Use forensic preservation before removal on suspicious/malicious tools.
- Automate bulk removal where safe (e.g., remove PUP or common attacker tools).
- Always review startup mechanisms post-removal.

---

## References

[Windows PowerShell Get-WmiObject](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject)  
[Linux Package Management Guide](https://wiki.debian.org/PackageManagement)  
[Sysinternals Autoruns](https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for identifying and removing unapproved software across OS platforms | Leo |
