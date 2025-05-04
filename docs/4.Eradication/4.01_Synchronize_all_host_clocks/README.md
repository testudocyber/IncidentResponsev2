# Synchronize All Host Clocks

## Conditions

Given a known network configuration, an NTP data source(s), and system owner preferences on time synchronization within their enclave.

## Standards

* Identify or stand up Network Time Protocol (NTP) server.
* Ensure NTP server is set to synchronize from a known good time source (ex: time.nist.gov).
* Configure all non-Windows systems to synchronize from the local NTP server.
* Configure all Windows systems which are not members of a domain to synchronize from the local NTP server.
* Configure Windows primary domain controller to synchronize from the local NTP server and configure GPO settings for domain.
* Validate proper synchronization by checking host clocks and confirming drift is within acceptable thresholds (generally < 5 seconds).
* Document NTP configuration and validation results.

## End State

All host clocks in the environment are synchronized from a known good time source, enabling operations and accurate forensic log analysis.

---

## Notes

Windows domain member systems will automatically synchronize their time based on the primary domain controller (PDC). Best Practices for NTP configuration include filtering the NTP protocol at the firewall and blocking outbound NTP (to prevent being used in a Distributed Denial-of-Service (DDoS) attack).

---

## Manual Steps

### Windows

* Check NTP Status:
```bat
w32tm /query /status
```

* Check NTP Configuration:
```bat
w32tm /query /configuration
```

* Start NTP Windows:
```bat
net start w32time
```

* Check NTP server settings in registry:
```bat
reg QUERY HKLM\SYSTEM\CurrentControlSet\Services\W32Time\Parameters
```

* Check NTP settings in registry:
```bat
reg QUERY HKLM\SYSTEM\CurrentControlSet\Services\W32Time\Config
w32tm /dumpreg
```

* Get Date and Time:
```powershell
Get-Date -F o
```

* Set NTP Server:
```bat
w32tm /config /manualpeerlist:time.nist.gov /syncfromflags:manual /reliable:yes /update
```

* Restore NTP settings back to default:
```bat
net stop w32time
w32tm /unregister
w32tm /unregister (Yes run twice)
w32tm /register
net start w32time
```

---

### Linux

* Check synchronized clock status:
```bash
systemctl status systemd-timesyncd.service
```

* Check NTP Status:
```bash
timedatectl status
```

* Configure NTP Server selection options:
```bash
/etc/systemd/timesyncd.conf
```

* Current NTP settings:
```bash
cat /etc/ntp.conf
```

* Restart NTP service after configuration changes:
```bash
/etc/init.d/ntp restart
```

* Check NTP synchronized servers:
```bash
ntpq -p
```

---

## Running Script

### PowerShell (Quick Validation Script)

```powershell
$computers = Get-ADComputer -Filter * -SearchBase "OU=Workstations,DC=example,DC=com"

foreach ($computer in $computers) {
    Invoke-Command -ComputerName $computer.Name -ScriptBlock {
        w32tm /query /status
    }
}
```

> **Operator Note:** This bulk queries domain-joined systems for NTP status to validate clock sync.

---

## Dependencies

* NTP Server (internal preferred, external (e.g. time.nist.gov) as fallback)
* Windows Time Service running on hosts
* NTP client on Linux hosts (ntpd or systemd-timesyncd)
* Firewall rule exceptions for NTP (UDP/123) if using external time source
* Administrative privileges on systems to check/set time settings

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| chrony | Linux | `apt install chrony` or `yum install chrony` | Lightweight NTP client for Linux. `chronyc sources` to check sync. |
| Meinberg NTP | Windows | [Download](https://www.meinbergglobal.com/english/sw/ntp.htm) | Full-featured NTP daemon for Windows environments. |
| ntpdate | Linux (legacy) | `apt install ntpdate` | Quick, one-time sync: `ntpdate time.nist.gov` |
| pool.ntp.org | Cross-platform | Pre-configured | Free distributed NTP service, easy alternative source |

> **Operator Note:** chrony is recommended over ntpd for modern Linux distributions due to better performance and ease of configuration.

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Identify authoritative time source (internal NTP preferred).
- [ ] Validate that Domain Controllers and domain member systems sync properly.
- [ ] Validate that non-domain Windows and Linux hosts sync with correct source.
- [ ] Use group policy or scripts to enforce settings at scale.
- [ ] Validate after configuration using `w32tm /query` (Windows) or `ntpq -p` (Linux).
- [ ] Document NTP server used and time sync verification.

### Best Practices

- Prefer internal NTP servers to reduce reliance on internet resources.
- Harden NTP servers and restrict access to prevent abuse.
- Validate synchronization regularly → clock drift can impact log correlation and forensic timeline analysis.
- Include NTP validation as a post-eradication validation task.

---

## References

[Good information on implementing NTP](http://Support.ntp.org/bin/view/Support/SelectingOffsiteNTPServers)  
[VMWare info on NTP for Windows Guest operating systems](https://kb.vmware.com/kb/1318)  
[Cisco Best Practices](https://www.cisco.com/c/en/us/support/docs/availability/high-availability/19643-ntpm.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original content enhanced with tools, operator recommendations, validation scripts, and checklist | Leo |
