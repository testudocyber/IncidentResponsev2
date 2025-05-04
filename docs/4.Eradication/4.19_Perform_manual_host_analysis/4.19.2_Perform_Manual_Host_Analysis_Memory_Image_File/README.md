# 4.19.2 Perform Manual Host Analysis – Memory Image File

## Task

Perform analysis of a **captured memory image file** to identify suspicious processes, artifacts, and evidence matching Indicators of Compromise (IOCs). This is often done after initial acquisition to allow deeper analysis without impacting the live host.

---

## Conditions

Given:

* A memory image from a suspected compromised system
* Memory analysis tools (Volatility, Memoryze, Rekall, etc.)
* Known Indicators of Compromise (IOCs)
* A validated copy of the memory image

---

## Standards

* Team member copies the memory image to the incident response workstation.
* Team member performs hash verification to ensure image integrity.
* Team member uses memory analysis tools to extract forensic artifacts and compare them to IOCs.
* Analysis should include (but not limited to):
  * Running processes and hidden processes
  * DLLs loaded
  * Network connections
  * User command history
  * Registry hives
  * Extracted credentials or tokens

---

## End State

Suspicious processes, files, network connections, and/or command history matching IOCs are identified and documented. Artifacts are preserved for deeper forensic or malware analysis.

---

## Notes

Memory analysis can reveal active malware, fileless malware, credential stealing attacks, and lateral movement attempts. The responder should approach this methodically to not overlook artifacts:

* Always validate hashes to confirm no corruption.
* Avoid running unverified scripts or plugins from unknown sources.
* Review tool documentation regularly for updated profiles and plugins.

---

## Tools

* **Volatility** - The most widely used memory analysis framework.
* **Memoryze (FireEye)** - Alternative GUI and CLI memory analysis tool.
* **Rekall** - Advanced fork of Volatility (Python-based).
* **LiME + Volatility (Linux)** - Memory acquisition and analysis.
* **FTK Imager (For acquisition + viewing)**

---

## Manual Steps

### Step 1 → Copy Memory Image to IR Workstation

* Ensure write-blocking or read-only mount of evidence media
* Copy to dedicated analysis drive
* Calculate and validate hash:

```bash
# Windows
certutil -hashfile memory.raw sha256

# Linux / macOS
sha256sum memory.raw
```

* Verify against original capture hash.

---

### Step 2 → Identify Operating System and Profile (Volatility)

```bash
volatility -f memory.raw imageinfo
```

* Returns suggested profile(s), such as `Win7SP1x64`.

---

### Step 3 → List Running Processes

```bash
volatility -f memory.raw --profile=Win7SP1x64 pslist
```

* Review process names, parent-child relationships.
* Look for suspicious or unsigned processes.

---

### Step 4 → Identify Hidden or Injected Processes

```bash
volatility -f memory.raw --profile=Win7SP1x64 psscan
volatility -f memory.raw --profile=Win7SP1x64 malfind
```

* Hidden processes = potential malware
* Malfind → review injected code and memory sections

---

### Step 5 → List Open Network Connections

```bash
volatility -f memory.raw --profile=Win7SP1x64 netscan
```

* Look for connections to unusual IP addresses or ports.

---

### Step 6 → Extract DLLs loaded by processes

```bash
volatility -f memory.raw --profile=Win7SP1x64 dlllist
```

* Review DLL paths → Unusual or temp directory DLLs may indicate malware.

---

### Step 7 → Extract Registry Hives and Review

```bash
volatility -f memory.raw --profile=Win7SP1x64 hivelist
volatility -f memory.raw --profile=Win7SP1x64 printkey -K "Path_to_key"
```

* Identify persistence mechanisms or malware config.

---

### Step 8 → Extract Command History (CLI Usage)

```bash
volatility -f memory.raw --profile=Win7SP1x64 consoles
```

* Review for attacker use of PowerShell, cmd.exe, etc.

---

### Step 9 → Extract Strings (optional bulk review)

```bash
strings memory.raw | grep "suspicious_keyword"
```

* Fast and simple → may reveal malware commands, passwords, etc.

---

### Optional Cloud Workflows (if analyzing VM memory dump)

#### AWS

* Memory image from EC2 can be acquired via forensic acquisition (EBS snapshots).
* Process with Volatility after conversion to raw format.

#### Azure / GCP

* Use snapshots or disk capture → process same as physical.

---

## Running Script

```bash
# Example Volatility Run
volatility -f memory.raw --profile=Win7SP1x64 pslist > pslist.txt
volatility -f memory.raw --profile=Win7SP1x64 netscan > netscan.txt
volatility -f memory.raw --profile=Win7SP1x64 malfind > malfind.txt
```

---

## Dependencies

* Python 2.x / 3.x (for Volatility or Rekall)
* Volatility Framework
* Administrative access on IR workstation
* Memory image file + hash verification tool

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Volatility | Windows/Linux/macOS | Python-based | Deep memory analysis |
| Rekall | Windows/Linux/macOS | Python-based | Memory analysis (modern alternative) |
| Memoryze | Windows | Installable | GUI + CLI analysis |
| FTK Imager | Windows | Installable | Memory acquisition + viewing |
| strings (Sysinternals or GNU) | Cross-platform | Native/Open Source | Extract readable content |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Validate integrity of memory image (hash comparison)
- [ ] Identify operating system profile using `imageinfo`
- [ ] Extract and review:
  - [ ] Running processes (`pslist`)
  - [ ] Hidden processes (`psscan`)
  - [ ] Injected/malicious code (`malfind`)
  - [ ] Open network connections (`netscan`)
  - [ ] Loaded DLLs (`dlllist`)
  - [ ] Registry persistence (`hivelist`)
  - [ ] Command history (`consoles`)
- [ ] Compare artifacts to IOC set
- [ ] Document and escalate suspicious findings

### Best Practices

- Use a dedicated forensic workstation for analysis
- Always hash evidence pre/post-processing
- Avoid uploading memory images to cloud/public sites
- Use multiple plugins → attackers may hide artifacts in obscure memory sections
- Review findings with malware analyst for confirmation

---

## References

* [Volatility Command Reference](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference)
* [Stuxnet Trojan Memory Forensics](http://www.behindthefirewalls.com/2013/12/stuxnet-trojan-memory-forensics-with_16.html)
* [FireEye Memoryze](https://www.mandiant.com/resources/memoryze)
* [Rekall Memory Forensics](https://github.com/google/rekall)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded with operator workflow, multi-platform analysis and advanced tooling | Leo |
