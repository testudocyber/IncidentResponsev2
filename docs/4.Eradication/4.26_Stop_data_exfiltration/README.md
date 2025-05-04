# 4.26 Stop Data Exfiltration

## Task

Stop data exfiltration

---

## Conditions

* Given a network with sensitive data
* Harden host nodes against data exfiltration

---

## Standards

* Prevention or mitigation of the exfiltration of information from a network is accomplished when the following conditions are met:
  * Prevention of information from being transferred/burned to CD/DVD.
  * Prevention of information from being transferred to removable or additional storage drives.
  * Education on Social Engineering and Phishing Attacks that lead to data exfiltration.
  * Minimize the size of data allowed to be emailed.
  * Minimize the size of data allowed to be sent out of the network.
  * Detection and active prevention of suspicious outbound network traffic, including cloud storage and covert exfiltration channels.

---

## End State

* All unauthorized processes, users, and channels for exfiltration are disabled or removed.
* Exfiltration attempt is stopped and verified.
* Endpoint and network controls are enforced to prevent recurrence.
* Full incident documentation completed for post-incident review.

---

## Notes

The methods described here help prevent host-based data exfiltration. However, network-centric controls should complement host controls:

* FTP, SFTP, SCP, and HTTP/S ports
* Email (attachments, SMTP)
* Cloud storage access (Dropbox, Google Drive, OneDrive)
* DNS tunneling and C2 channels
* USB and removable media

**Considerations:**  
Blocking exfiltration can impact operations. Work closely with stakeholders and IT to minimize business disruption.

---

## Manual Steps

### Prevent Removable Media Exfiltration

#### Disable USB Storage (Windows)

##### Prevent USB Storage Device Installation

1. Open Windows Explorer → `%SystemRoot%\Inf`
2. Right-click `Usbstor.pnf` → Properties → Security
3. Add users → Deny → Full Control
4. Add SYSTEM account → Deny → Full Control
5. Repeat for `Usbstor.inf`.

##### Disable USB Storage through Registry

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR\" -Name "Start" -Value 4
```

#### Disable USB Storage (macOS)

```bash
sudo kextunload /System/Library/Extensions/IOUSBMassStorageClass.kext
```

Re-enable (if needed):

```bash
sudo kextload /System/Library/Extensions/IOUSBMassStorageClass.kext
```

---

### Disable CD/DVD Burning

#### Group Policy Method (Windows)

1. Open Local Group Policy Editor.
2. User Configuration → Administrative Templates → Windows Components → File Explorer
3. Enable → Remove CD Burning features.

#### Registry Method

```reg
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer
NoCDBurning = 1 (DWORD)
```

---

### Employee Training and Awareness

* Train staff to:
  * Identify sensitive information.
  * Understand risks of data exfiltration.
  * Follow secure handling policies.
* Regularly distribute and update training materials.

---

### Detect and Block Network-Based Exfiltration

#### SIEM or Network Monitoring

* Monitor for large outbound transfers, unusual ports, or cloud uploads.

##### Splunk Query Example

```spl
index=network sourcetype=proxy OR sourcetype=firewall | stats sum(bytes_out) by dest_ip, user
```

#### Firewall / ACLs (Windows/Linux/Cloud)

```bash
iptables -A OUTPUT -p tcp --dport 443 -d <exfil_ip> -j DROP
```

#### DNS Firewall

* Block known exfiltration domains.
* Implement DNS tunneling detection.

---

### Block Cloud Storage and SaaS Channels

#### Cloud IAM and Storage Controls

* Revoke upload rights for affected users.
* Enable DLP policies.
* Block uploads during active incidents.

##### AWS Example

```bash
aws s3api put-bucket-policy --bucket mybucket --policy file://deny-upload-policy.json
```

#### Google Workspace

* Admin → Drive → Sharing settings → Disable external sharing and uploads.

---

### Email Exfiltration Controls

* Limit outbound attachment size.
* Block executable and archive formats during incidents.

---

### Validate Exfiltration Stoppage

* Verify no additional outbound exfiltration attempts.
* Use SIEM to confirm that traffic has stopped.
* Increase monitoring sensitivity temporarily.

---

## Running Script Examples

#### Disable USB Storage (Windows)

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR\" -Name "start" -Value 4
```

#### Block Domain/IP on Windows Firewall

```powershell
New-NetFirewallRule -DisplayName "Block Exfil IP" -Direction Outbound -RemoteAddress <IP> -Action Block
```

#### Block IP on Linux (iptables)

```bash
iptables -A OUTPUT -p tcp --dport 443 -d <exfil_ip> -j DROP
```

---

## Dependencies

* Administrative access to host, network, and cloud platforms.
* SIEM, IDS/IPS, and EDR deployment.
* Group Policy or MDM tools for USB and CD/DVD restrictions.
* Cloud IAM and DLP policies.
* Trained and aware user base.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Microsoft Intune | Windows/macOS | Cloud | USB + removable media policy enforcement |
| CrowdStrike / SentinelOne | Cross-platform | Installed | Detect and block malicious behavior |
| Splunk / Elastic / Security Onion | Cross-platform | Installed | Monitor outbound traffic and DNS |
| AWS GuardDuty / CloudTrail | Cloud | Configured | Detect exfil-related activities |
| iptables / pfSense | Linux / Network | Installed | Block IPs and protocols |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Disable removable media (USB/CD/DVD).
- [ ] Block unauthorized cloud uploads and sharing.
- [ ] Enforce size and file type restrictions on email.
- [ ] Block suspicious outbound network destinations.
- [ ] Isolate systems showing signs of exfiltration.
- [ ] Validate exfiltration channels are closed.
- [ ] Document response actions in the incident report.

### Best Practices

* Always confirm before blocking → avoid false positives.
* Use layered controls → block at host, network, and cloud levels.
* For incidents involving sensitive data, preserve forensic evidence before removal.
* Coordinate with IT and Legal for high-sensitivity incidents.
* Update detection content and training post-incident.

---

## References

* [Windows Disable USB Storage Devices](https://support.microsoft.com/en-us/help/823732/how-can-i-prevent-users-from-connecting-to-a-usb-storage-device)
* [Windows Disable burning DVD/CD](https://www.sevenforums.com/tutorials/5942-burning-cd-dvd-enable-disable.html)
* [AWS GuardDuty Data Exfiltration](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_data_exfiltration.html)
* [Azure Sentinel Exfiltration Playbook](https://learn.microsoft.com/en-us/azure/sentinel/)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.1 | Merged original host-focused content and expanded with network, cloud, and SaaS controls for comprehensive operator guidance | Leo |
