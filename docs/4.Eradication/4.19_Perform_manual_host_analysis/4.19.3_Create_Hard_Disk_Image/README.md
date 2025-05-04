# 4.19.3 Create Hard Disk Image

## Task

Create a **forensically sound image of a hard disk** from a live or offline system to preserve evidence for analysis without altering the original data.

---

## Conditions

Given:

* A Windows or Linux machine suspected of compromise
* Hard drive acquisition tools (dc3dd, dd, FTK Imager, Guymager)
* Access to destination storage or network share
* One or more Indicators of Compromise (IOCs) identified

---

## Standards

* Team member connects to the machine and creates a physical disk image using supported tools.
* Team member must provide a log file and cryptographic hash (MD5/SHA256 preferred) to validate image integrity.
* Team member must store the image in a secure location for further forensic analysis.

**Examples**

#### Windows

```cmd
dc3dd.exe if=\\.\c: of=d:\<TARGET DRIVE>\<IMAGE NAME>.dd hash=md5 log=d:\<LOG DIRECTORY>\<LOG NAME>.log
```

#### Linux (Remote Copy)

```bash
dd if=/dev/sdX | ssh user@destination_ip "dd of=/destination_path/image.dd"
```

*Note: Replace `/dev/sdX` with source disk identifier (e.g., `/dev/sda`).*

#### FTK Imager GUI (Windows)

* Launch FTK Imager
* Select "Create Disk Image"
* Choose Physical Drive
* Configure output → raw (dd) or E01 format
* Enable hash calculation
* Complete acquisition and review log/hash report

#### Guymager GUI (Linux)

* Launch Guymager
* Select physical drive
* Configure acquisition → raw or EWF
* Enable hashing + logging
* Start imaging process

---

## End State

* A verified and complete physical image of the source disk is created.
* Hash integrity value is recorded and log files retained.
* Image is ready for forensic analysis without impacting source disk.

---

## Notes

Imaging can be performed from:

* Live systems (volatile + disk capture possible)
* Offline systems (safer, reduces risk of anti-forensic techniques)
* Network acquisition (if physical access is limited)

**Important:** Ensure chain of custody with logging, hashing, and evidence bagging procedures.

---

## Tools

* **dc3dd (Windows/Linux)** → CLI imaging + hashing tool (enhanced dd)
* **dd (Linux/macOS)** → Standard disk imaging utility
* **FTK Imager (Windows)** → GUI-based forensic disk imager
* **Guymager (Linux)** → Fast, GUI-based forensic imager

---

## Manual Steps

### Step 1 → Prepare Destination

* Validate enough space exists (raw image sizes = size of disk)
* Prepare evidence storage → USB, NAS, forensic server

### Step 2 → Prepare Imaging Tool

* Use verified tool binaries (hash validated)
* Configure for log + hash calculation

### Step 3 → Perform Acquisition

* Start disk acquisition (dc3dd, dd, FTK Imager, or Guymager)
* Monitor process for errors
* Upon completion → validate hash (automated or manual comparison)

### Step 4 → Secure and Document

* Move image + log + hash file to secure evidence storage
* Document process in forensic acquisition worksheet
* Notify IR lead or forensic team

---

## Running Script

#### Automated CLI (Linux Example)

```bash
dc3dd if=/dev/sda of=/mnt/evidence/disk_image.dd hash=sha256 log=/mnt/evidence/disk_image.log
sha256sum /mnt/evidence/disk_image.dd > /mnt/evidence/disk_image.dd.sha256
```

#### Automated CLI (Windows Example)

```cmd
dc3dd.exe if=\\.\PhysicalDrive0 of=E:\evidence\disk_image.dd hash=sha256 log=E:\evidence\disk_image.log
```

---

## Dependencies

* USB or network storage location
* IR toolkit with imaging utilities
* Local administrator access
* Verified toolsets (dc3dd, dd, FTK Imager, Guymager)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| dc3dd | Windows/Linux | Download from Sourceforge | Imaging + hashing |
| dd | Linux/macOS | Native | Imaging |
| FTK Imager | Windows | Installer | GUI imaging + hashing |
| Guymager | Linux | Pre-installed on many distros | Fast GUI imaging |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Validate and prepare destination storage (enough space + permissions)
- [ ] Select correct disk → avoid imaging wrong device
- [ ] Validate hashing and logging options enabled
- [ ] Execute acquisition and monitor progress
- [ ] Validate resulting hash matches log hash
- [ ] Record imaging process in IR documentation
- [ ] Secure evidence storage → immutable location if possible

### Best Practices

* Always hash BEFORE and AFTER transfers (ensures integrity)
* Avoid writing to source disk during acquisition
* Use write blockers when available (especially offline capture)
* For large drives → consider segmenting or using EWF (E01) for management
* Encrypt evidence if stored long-term or moved between sites

---

## References

* [Sourceforge DC3DD](https://sourceforge.net/projects/dc3dd/files/dc3dd/7.2%20-%20Windows/)
* [FTK Imager](https://accessdata.com/product-download/ftk-imager-version-3.4.3)
* [Guymager forensic image](http://guymager.sourceforge.net)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded with detailed operator workflow, multi-platform guidance, and procedural checklist | Leo |
