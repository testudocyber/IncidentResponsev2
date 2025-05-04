# Disable Windows Services

## Task Disable Non-Essential or Suspicious Windows Services

## Conditions

Given access to compromised or at-risk systems and local administrator or domain administrator permissions, the operator will identify and disable unnecessary or suspicious services to disrupt potential attacker persistence and limit lateral movement.

> **Operator Note:** Adversaries frequently abuse Windows services to maintain persistence, escalate privileges, or evade detection. Disabling unneeded or malicious services is a key containment tactic, but should be executed carefully to avoid service disruption.

## Standards

* Team member enumerates all active and configured services.  
* Team member identifies non-essential, suspicious, or malicious services.  
* Team member disables these services using appropriate tools and methods.  
* Team member validates the services are no longer running or able to restart.  
* Team member documents all services disabled and retains forensic evidence.

## End State

All unauthorized or non-essential services on impacted hosts are disabled, and operators have validated these services are no longer running.

---

## Notes

- Disabling services can have operational impact → coordinate with system owners where possible.
- Always collect forensic evidence and document service states before making changes.
- Use automated tooling for fleet-wide service analysis when available.

---

## Manual Steps

### Step 1: Enumerate All Services

#### PowerShell (Preferred)

```powershell
Get-Service | Select-Object Name, DisplayName, Status, StartType
```

#### Command Line (sc query)

```cmd
sc query type= service state= all
```

#### Windows Services GUI (services.msc)

```plaintext
services.msc
```

> **Operator Note:** Use GUI for easier visual review when dealing with complex or unfamiliar services.

---

### Step 2: Investigate Suspicious Services

Look for:

- Unexpected names (random strings, disguised as legit services)
- Non-Microsoft services running from unusual paths (AppData, Temp, etc)
- Recently installed services

```powershell
Get-WmiObject Win32_Service | Select-Object Name, DisplayName, State, StartMode, PathName
```

---

### Step 3: Disable a Service

#### PowerShell (Recommended)

```powershell
Set-Service -Name "ServiceName" -StartupType Disabled
Stop-Service -Name "ServiceName" -Force
```

#### Command Line (sc config)

```cmd
sc config "ServiceName" start= disabled
sc stop "ServiceName"
```

> **Operator Note:** Always stop the service after disabling to ensure it is not running.

---

### Step 4: Validate Disabled Status

```powershell
Get-Service -Name "ServiceName"
```

Expected Status:

```plaintext
Status   Name               DisplayName
------   ----               -----------
Stopped  ServiceName        Example Service
```

---

### Step 5: Back up list of services (optional for rollback or forensics)

```powershell
Get-Service | Export-Csv .\ServicesBackup.csv -NoTypeInformation
```

---

## Running Script (Bulk Disable Suspicious Services Example)

```powershell
$suspiciousServices = @("badsvc1","malwareupdate","strangeSvc")

foreach ($svc in $suspiciousServices) {
    Set-Service -Name $svc -StartupType Disabled
    Stop-Service -Name $svc -Force
}
```

> **Operator Note:** Update the array with service names identified during review.

---

## Dependencies

* Local Administrator or Domain Admin privileges  
* PowerShell  
* sc.exe (built into Windows)  
* WMI tools (optional)  
* Coordination with enclave or system owners

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| PowerShell (Get-Service, Set-Service) | Windows | Bulk review and disabling |
| sc.exe | Windows | CLI method for disabling |
| services.msc | Windows | Visual review and disable (manual) |
| Sysinternals Autoruns | Windows | Detect services and startup programs |
| WMI | Windows | Advanced service interrogation |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate all services on impacted hosts.
- [ ] Identify non-essential or suspicious services.
- [ ] Document suspicious services with executable path and config.
- [ ] Disable services (Set-Service or sc config).
- [ ] Validate service is stopped and does not restart.
- [ ] Export backup of services list for rollback and forensic purposes.

### Best Practices

- Avoid disabling Microsoft core OS services unless necessary.
- Disable and stop (do not delete) services for forensic retention.
- Coordinate service disablement during production hours to minimize impact.
- Use Sysinternals Autoruns for advanced detection of malicious persistence mechanisms.

---

## References

[Microsoft Docs - Service Cmdlets in PowerShell](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-service)  
[sc.exe command-line reference](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/sc-config)  
[Sysinternals Autoruns](https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for disabling Windows services, multi-method instructions, forensic guidance | Leo |
