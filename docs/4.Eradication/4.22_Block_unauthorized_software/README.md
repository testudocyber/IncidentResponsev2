# 4.22 Block Unauthorized Software

## Task

Identify and block unauthorized or potentially malicious software from executing on affected hosts to prevent further compromise, persistence, or data exfiltration during an incident.

---

## Conditions

Given:

* Active incident where unauthorized software has been identified or is suspected.
* Access to endpoint management tools (e.g. Group Policy, Endpoint Protection, Application Control platforms).
* Administrative access to affected systems.
* Approved software baseline or application whitelist.

---

## Standards

* Unauthorized software is identified through host analysis, IOC matching, user reports, or security monitoring.
* Software is reviewed and validated as unauthorized or suspicious.
* Blocking mechanisms are deployed using appropriate tooling (GPO, EDR policies, application control).
* Blocking actions are verified and logged for incident response reporting.

---

## End State

* Unauthorized software is prevented from executing on affected systems.
* Blocking measures are enforced across affected organizational systems.
* Operator logs and reports actions taken for IR documentation and potential lessons learned.

---

## Notes

* Blocking unauthorized software is a containment action. It should be done carefully to avoid interrupting legitimate business operations.
* In large environments, blocking should be staged (test → pilot → global) unless emergency dictates immediate enterprise-wide enforcement.
* Unauthorized software may include:
  * Malware (confirmed malicious executables)
  * Hacking tools (mimikatz, metasploit payloads)
  * Shadow IT (user-installed unsupported software)
  * Outdated or vulnerable versions of legitimate software

---

## Manual Steps

### Step 1 → Identify Unauthorized Software

* Analyze host artifacts (process listings, file system, registry, installed programs list).
* Review endpoint alerts and security monitoring (EDR, SIEM).
* Leverage IOC sweeps → Hashes, filenames, paths.
* Validate against enterprise software inventory / approved software list.

#### Tools/Commands

##### Windows

```powershell
# List installed programs
Get-WmiObject -Class Win32_Product | Select-Object Name, Version

# List running processes
Get-Process

# Get autoruns
autorunsc -accepteula -a * -c > autoruns.csv
```

##### Linux/macOS

```bash
# List running processes
ps aux

# List installed packages (Linux Debian/Ubuntu)
dpkg --list

# List installed packages (macOS)
brew list
```

---

### Step 2 → Determine Blocking Mechanism

#### Windows Options

* **Group Policy - Software Restriction Policies (SRP)** → Block by hash, path, or publisher.
* **Group Policy - AppLocker** → Allow/deny execution by path, publisher, or hash.
* **Windows Defender Application Control (WDAC)** → Advanced binary blocking (requires planning).
* **Windows Defender Antivirus / EDR (Defender for Endpoint)** → Custom Indicators → Block hash or filename.

#### Linux/macOS Options

* **AppArmor (Linux)** → Deny execution of unauthorized software.
* **SELinux (Linux)** → Enforce execution control via policies.
* **macOS Gatekeeper and MDM** → Block unauthorized apps via configuration profiles.
* **EDR Agents (Linux/macOS)** → Block by hash or path (Crowdstrike, SentinelOne, etc.)

---

### Step 3 → Apply Blocking Policy

#### Example → Windows Group Policy SRP (Block by Path)

* Open `Group Policy Management Console`
* Navigate → `Computer Configuration > Policies > Windows Settings > Security Settings > Software Restriction Policies`
* Create new path rule:
  * Path: `C:\Path\To\UnauthorizedSoftware\*`
  * Security Level: Disallowed
* Force policy update:

```cmd
gpupdate /force
```

#### Example → Defender for Endpoint IOC Block

* Go to Security Center → Threat Intelligence → Indicators → Files
* Add hash and select "Block execution"
* Assign policy to affected systems

#### Example → AppArmor Profile (Linux)

```bash
sudo aa-complain /etc/apparmor.d/usr.bin.exampleapp
```

Or set enforce mode:

```bash
sudo aa-enforce /etc/apparmor.d/usr.bin.exampleapp
```

#### Example → macOS MDM (Jamf, Intune)

* Create custom configuration profile
* Add unauthorized app bundle ID or path
* Deploy profile to targeted macOS endpoints

---

### Step 4 → Verify Blocking

* Attempt to execute unauthorized software → Validate it is denied.
* Check system event logs / security tools for block events.
* Report success or troubleshooting required.

---

### Step 5 → Report and Document

* Log:
  * Unauthorized software identified
  * Method of blocking implemented
  * Validation of enforcement
  * Hostnames, usernames, timestamps
* Notify incident commander and document in IR management platform.

---

## Running Script Examples

#### Block by Hash (Windows Defender via PowerShell + Advanced Threat Protection API)

```powershell
Add-MpPreference -AttackSurfaceReductionRules_Ids <hash> -AttackSurfaceReductionRules_Actions Block
```

#### Block by Filename Path (Linux via chmod)

```bash
chmod 000 /path/to/unauthorized_binary
```

#### Block via Windows Software Restriction Policy (SRP) Path Rule

```cmd
gpupdate /force
```

---

## Dependencies

* Administrative access to systems
* Approved software baseline
* Group Policy Management tools
* Endpoint Protection platform (Windows Defender ATP, CrowdStrike, SentinelOne, etc)
* Network connectivity to push policies
* IR case tracking platform

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Group Policy (SRP, AppLocker) | Windows | Native | Application control |
| Windows Defender ATP / Intune | Windows | Native + cloud | Custom IOC blocking |
| AppArmor / SELinux | Linux | Native | Mandatory Access Control |
| MDM (Jamf / Intune) | macOS | Cloud / Installed | Application blocking |
| EDR Tools (Crowdstrike, SentinelOne) | Cross-platform | Installed | IOC + binary blocking |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Confirm software is unauthorized/malicious
- [ ] Validate hash/path and location
- [ ] Select appropriate blocking mechanism (GPO, EDR, etc)
- [ ] Apply block and confirm enforcement
- [ ] Validate blocking is working properly (execution fails)
- [ ] Document action and escalate if widespread deployment needed

### Best Practices

* Favor hash blocking for precise control → Less risk of false positive.
* Path-based blocking is useful for quick containment.
* Blocking via enterprise endpoint agent provides easiest global enforcement.
* Avoid deleting unauthorized binaries until forensics complete.
* Work with IT to identify legitimate exceptions or business impact.

---

## References

* [Microsoft AppLocker](https://learn.microsoft.com/en-us/windows/security/threat-protection/applocker/applocker-overview)
* [Windows Defender Antivirus Custom Indicators](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/indicator-ip-url)
* [SELinux User Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/)
* [AppArmor Documentation](https://gitlab.com/apparmor/apparmor/-/wikis/home)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Generated full version with operator workflow, procedural steps, and blocking options for Windows, Linux, and macOS | Leo |
