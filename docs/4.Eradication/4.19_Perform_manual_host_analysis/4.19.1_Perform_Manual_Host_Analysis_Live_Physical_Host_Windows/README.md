# 4.19.1 Perform Manual Host Analysis – Live Physical Host - Windows

## Task

Perform manual analysis of a suspected compromised **live physical Windows host** to collect volatile data and identify artifacts matching known Indicators of Compromise (IOCs).

---

## Conditions

Given:

* A suspected compromised Windows system.
* Local administrator account credentials.
* USB device or incident response workstation with memory analysis and forensic tools.
* One or more Indicators of Compromise (IOCs).

---

## Standards

* Team member inserts a USB device containing memory analysis tools into the suspected compromised computer.
* Team member loads and runs analysis software from the USB drive to capture volatile data (memory, processes, connections, etc.) and outputs the results to the USB drive.
* Team member analyzes the output and validates presence of known IOCs.
* If malware infection risk exists for removable media, output results should be transmitted over network to a secure share instead.

---

## End State

* Processes, files, registry entries, memory artifacts, and/or network connections that match one or more IOCs are identified and documented.
* Analysis results are saved and transferred securely for further investigation.

---

## Notes

* Be cautious when working on live systems — running malware may detect analysis tools or alter artifacts.
* External tools should be run from removable read-only media or isolated IR jumpkits.
* USB should not be used if IOC suggests malware propagation via USB → use network share or remote analysis.

---

## Tools

* **Redline (Mandiant)** - Analyzes live memory and running processes from external media.
* **Sysinternals Suite (Microsoft)** - Tools like `PsList`, `PsLoggedOn`, `TCPView`, `Autoruns` for live analysis.
* **WinPMEM (Velociraptor project)** - Lightweight memory capture tool.
* **FTK Imager (AccessData)** - Can capture live memory and disk images.
* **LiveKatz / Mimikatz** (use carefully with authorization) - Credential artifacts.
* **Sysmon** (if pre-installed) - Check for suspicious process execution history.

---

## Manual Steps

### Step 1 → Prepare USB or IR Workstation

- Load memory analysis tools (e.g. Redline, Sysinternals) onto a clean USB or IR laptop.
- Validate hashes of toolset before deployment.
- If USB use is discouraged (USB malware IOC), prepare secure share location.

---

### Step 2 → Insert Tools and Collect Memory Snapshot

#### Example with Redline (Preferred)

* Insert USB
* Launch Redline collection configuration wizard
* Select "Create a Standard Collector" → choose collection options (Processes, DLLs, Network Connections, Registry).
* Output directory → select USB or network share
* Run collection → wait for memory capture and parsing.

#### Example with FTK Imager (Alternate)

* FTK Imager → File → Capture Memory
* Choose Destination → USB or network share
* Start capture → Validate capture completion and metadata.

#### Example with WinPMEM (CLI memory capture)

```cmd
winpmem.exe --output C:\Evidence\memory.aff4
```

---

### Step 3 → Analyze Captured Memory for IOC Artifacts

* Use Redline's Analysis Mode → load captured memory dataset.
* Search for known IOCs:
  * Process names
  * DLL hashes
  * Network IPs/domains
  * Registry persistence keys

* Document any matches.

---

### Step 4 → Capture Live Host Process / Connection State

* Use Sysinternals → PsList, TCPView

```cmd
pslist
tcpview
```

* Export process list and network connections.

---

### Step 5 → Collect Registry and Autorun Entries

* Run Autoruns (Sysinternals) and export startup entries:

```cmd
autorunsc -accepteula -a * -c > autoruns.csv
```

---

### Step 6 → Transfer Results Securely

* If using USB → safely eject and hand-off to IR analysis team.
* If using network share → validate upload and access restrictions.

---

## Running Script

```powershell
# Example live collection with Redline
Start-Process "D:\Redline\RunRedline.exe"

# FTK Imager CLI Memory Dump (Optional)
"ftkimager.exe" --capture-memory --destination "X:\CapturedMemory.mem"
```

---

## Dependencies

* USB drive or secure share
* Memory analysis tools
* Administrative credentials
* Pre-defined IOC list

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Redline | Windows | Standalone | Memory + IOC scanning |
| Sysinternals Suite | Windows | Standalone | Live process + network analysis |
| FTK Imager | Windows | Installable | Memory + Disk image |
| WinPMEM | Windows | Portable | Memory dump |
| Autoruns | Windows | Portable | Registry autoruns |

---

## Operator Recommendations and Best Practices

### Checklist

- [ ] Confirm incident scope and indicators before analysis
- [ ] Validate tools prior to usage (hashes, clean versions)
- [ ] Use network share if USB usage poses risk
- [ ] Capture volatile memory before shutting down or rebooting system
- [ ] Analyze captured artifacts → match with IOC set
- [ ] Record and escalate findings if suspicious indicators are identified
- [ ] Maintain chain of custody for evidence artifacts

### Best Practices

* Prefer automated collections (Redline) for consistency.
* Avoid unnecessary interaction on compromised host.
* Always capture volatile artifacts FIRST before disk.
* Use isolated analysis environments when reviewing live capture results.
* Be prepared to escalate for deeper malware analysis if advanced threats are identified.

---

## References

* [FireEye Redline](https://www.fireeye.com/services/freeware/redline.html)
* [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/)
* [Velociraptor WinPMEM](https://github.com/Velocidex/WinPmem)
* [FTK Imager](https://accessdata.com/product-download/ftk-imager-version-3.4.3)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded with operator workflow + multi-platform and advanced tooling | Leo |
