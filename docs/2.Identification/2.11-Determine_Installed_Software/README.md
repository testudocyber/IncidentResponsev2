# Determine Installed Software

## Task ID.AM-3.1 Determine Installed Software

## Conditions

Given a responder’s computer, a network host (workstation or server), and proper access credentials.

> **Operator Note:** Ensure network access and administrative privileges before conducting software enumeration activities. Confirm authorization and scope with network owner.

## Standards

* Team member verifies access to the network host.  
* Team member selects a method of listing installed applications on the network host and a storage location for the output file.  
* Team member runs the necessary script and verifies the data collected with the system owner upon completion.

> **Operator Note:** Multiple techniques exist for software discovery. Consider environment, scale, and level of access when selecting your approach.

## End State

The list of installed software for the selected host has been collected and validated as correct by the system owner.

> **Operator Note:** Store results securely with timestamp and hostname in filename for easy reference during later phases of the incident.

## Manual Steps

### PowerShell (Local - Run as Administrator)

Run BOTH commands for 32-bit and 64-bit installed software:

```powershell
Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName,DisplayVersion, Publisher, InstallDate | Format-Table -AutoSize > C:\software.txt
```

```powershell
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, Publisher, InstallDate | Format-Table -AutoSize >> C:\software.txt
```

### PowerShell (Remote - Run as Administrator)

```powershell
Invoke-command -cn <computername> -Scriptblock {Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, Publisher, InstallDate | Format-Table -AutoSize} > C:\software.txt
```

```powershell
Invoke-command -cn <computername> -Scriptblock {Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, Publisher, InstallDate | Format-Table -AutoSize} >> C:\software.txt
```

> **Operator Note:** Ensure WinRM is enabled on target hosts for remote PowerShell usage.

### WMIC (Local - Run in CMD as Administrator)

```bat
wmic /output:"C:\software.txt" product get name,version /format:"C:\Windows\System32\wbem\en-us\csv"
```

```bat
wmic /output:"C:\%Computername%_software.txt" product get name,version /format:"C:\Windows\System32\wbem\en-us\csv"
```

### WMIC (Remote)

```bat
wmic /node:"computername" product get name,version /format:csv > c:\software.txt
```

> **Operator Note:** WMIC is deprecated in latest Windows builds. Use PowerShell if WMIC is unavailable.

### PSInfo (Sysinternals - must be downloaded)

```bat
psinfo \\computername -u username -p password -s > c:\software.txt
```

> **Operator Note:** Download PsTools from Sysinternals and ensure psinfo.exe is in PATH or run from extracted folder.

Output should follow `output_format_template.csv` and be named:

```
[mm/dd/yyyy_hh:mm:ss_Installed_Software_(computer name)]
```

Notify mission element lead and intelligence analyst of completion.

## Running Script

* Download script from `./script/determine_installed_software.ps1`

* Run script:

```powershell
./determine_installed_software.ps1
```

* Select method of enumeration (1 - 4)
* Provide output file path (full path required, e.g. `C:\software_output.txt`)

> **Operator Note:** The script simplifies local and remote enumeration steps. Use when bulk processing is required.

## Dependencies

* Administrative credentials on target machines.
* PSTools downloaded if using PSInfo.

```plaintext
https://learn.microsoft.com/en-us/sysinternals/downloads/pstools
```

## References

[PowerShell find installed software](https://blogs.technet.microsoft.com/heyscriptingguy/2013/11/15/use-powershell-to-find-installed-software/)  
[PowerShell list installed programs](http://adriank.org/list-addremove-programs-on-a-remote-machine/)  
[Sysinternals Psinfo](https://technet.microsoft.com/en-us/sysinternals/psinfo.aspx)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate credentials and connectivity to target hosts.
- [ ] Choose appropriate collection method (PowerShell, WMIC, PsInfo, script).
- [ ] Store outputs using standardized naming convention.
- [ ] Notify mission lead and intelligence team after collection.
- [ ] Validate accuracy of discovered software list with system owner/admin.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows | PowerShell | Primary scriptable option, local and remote |
| Windows | WMIC (deprecated) | Alternate local/remote query |
| Universal | PsInfo (Sysinternals) | Portable utility for system info collection |
| Universal | Custom PowerShell Script | Automated multi-host enumeration |

### Alternate Commands and Examples

#### PowerShell local inventory:

```powershell
Get-WmiObject -Class Win32_Product | Select-Object Name, Version
```

#### PsInfo remote command:

```bat
psinfo \\192.168.1.50 -u domain\admin -p password -s > software_192.168.1.50.txt
```

### Best Practices

- Prefer PowerShell for modern environments.
- Use PSInfo where PowerShell remoting is unavailable.
- Validate scripts and tools before running in sensitive environments.
- Save all outputs securely and share via IR coordination platform.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + expanded operator tool usage, procedures, and best practices | Leo |
