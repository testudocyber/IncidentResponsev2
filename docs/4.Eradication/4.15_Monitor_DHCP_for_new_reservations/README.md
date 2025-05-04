# 4.15 Monitor DHCP for New Reservations (Local + Cloud)

## Task

Monitor DHCP servers for new reservations or leases that could indicate unauthorized device connections or adversary persistence mechanisms.

---

## Conditions

Given:

* Access to Dynamic Host Configuration Protocol (DHCP) server
* Administrator-level account
* Incident response workstation with PowerShell
* Access to cloud DHCP-equivalent services for review

---

## Standards

* Team member determines if audit logging is enabled within the DHCP Management Console.
* Team member enables logging if not already enabled.
* Team member connects to the DHCP server log and determines the presence of new reservations.
* Team member exports applicable Windows events to an external file for documentation and comparison.
* Team member compares against known good reservation list.

---

## End State

All new DHCP reservations and lease assignments are discovered, logged, validated, and documented.

---

## Notes

There are various ways to do this task. From an incident response perspective, the best choice (when supported) is a PowerShell script that queries DHCP event logs or leverages DHCP cmdlets to capture reservations and lease events.

* The applicable log on Server 2008+ is:

```
Microsoft-Windows-DHCP Server Events/Operational
```

* Applicable Event ID:

```
Event ID 106 → New reservation created
```

* CSV files from DHCP exports can be parsed with PowerShell via the `Get-Content` Cmdlet.

* Windows Server 2012 and higher support DHCP PowerShell module commands that may also leave traces in PowerShell logs.

---

## Manual Steps

### Windows DHCP Server → Enable Logging

```bat
reg add HKLM\System\CurrentControlSet\Services\DhcpServer\Parameters /v ActivityLogFlag /t REG_DWORD /d 1
```

#### Default log location:

```
%windir%\System32\Dhcp
```

---

### View DHCP Event Logs → Event ID 106

```powershell
Get-WinEvent -LogName "Microsoft-Windows-DHCP Server Events/Operational" -FilterXPath "*[System/EventID=106]"
```

Look for:

- IP Address
- MAC Address
- Client Hostname
- Reservation details

---

### Linux DHCP Monitoring (optional)

#### View DHCP lease logs

```bash
grep -Ei 'dhcp' /var/log/syslog.1
```

#### Real-time view:

```bash
tail -f dhcpd.log
```

---

### PowerShell Script → Compare new DHCP leases to baseline

```powershell
# Gather DHCP lease info
netsh dhcp server scope <IP ADDRESS> show clients 1 > C:\Monitor\New_dhcp_list.txt

# Remove top and bottom lines
$file = cat C:\Monitor\New_dhcp_list.txt | select -skip 8
$len = $file.length-4
$file = $file[0..($len)]

# Extract relevant columns
$file | foreach-object{$_.split()[0,4,7,18] -join ' '} > C:\Monitor\New_dhcp_list.txt

# Compare to known good and alert if different
Compare-Object $(Get-Content .\Known_good_dhcp_list.txt) $(Get-Content .\new_dhcp_list.txt) >> .\Domain_Changes_Log.txt
if ((Compare-Object $(Get-Content .\Known_good_dhcp_list.txt) $(Get-Content .\New_dhcp_list.txt)) -ne $null) {
    [System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms")
    $oReturn=[system.windows.forms.messagebox]::show("DHCP Client has been added! This change needs to be verified.")
}
```

---

### Cloud Environment Monitoring

#### AWS → DHCP Options Sets and Network Leases (VPC)

- DHCP Options Sets → Static → not typically used for dynamic reservations.
- Use CloudWatch + VPC Flow Logs → Detect unexpected clients.

```bash
aws ec2 describe-dhcp-options
aws ec2 describe-instances --query 'Reservations[*].Instances[*].PrivateIpAddress'
```

#### Azure → DHCP via VNets (Dynamic by default)

- NSG + Azure Monitor → Detect new IP to MAC bindings
- Optionally enable Network Watcher + Flow logs

#### GCP → Cloud DHCP (VPC internal IP assignment)

- VPC Flow Logs → Detect new devices pulling IPs
- GCP SCC → Detect unmanaged assets appearing on network

---

## Dependencies

* Windows DHCP Server / Domain Admin Access
* PowerShell for automation
* Cloud provider IAM access (reader roles)
* Network visibility for DHCP-bound traffic (optional)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| DHCP Server Logs | Windows | Native | Reservation + lease logging |
| PowerShell DHCP Module | Windows | Native (2012+) | Query + automate DHCP monitoring |
| Linux Syslog / DHCPD Logs | Linux | Native | DHCP lease monitoring |
| AWS CloudWatch + VPC Flow Logs | AWS | Native | Detect network DHCP activity |
| Azure Monitor + NSG Flow Logs | Azure | Native | Detect new IP/MAC pairs |
| GCP VPC Flow Logs + SCC | GCP | Native | Detect new DHCP clients |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate that DHCP server logging is enabled.
- [ ] Collect and review DHCP event logs → look for Event ID 106.
- [ ] Execute PowerShell script and compare new leases to baseline.
- [ ] Investigate any new reservations or leases → validate with system owner.
- [ ] Check for unusually short or long lease times.
- [ ] Review cloud DHCP-equivalent → AWS, Azure, GCP → look for new clients/IP bindings.
- [ ] Document all findings and escalate unknown devices.

### Best Practices

- Monitor for new DHCP reservations → adversaries may use persistent MAC addresses
- Compare reservation list daily/weekly → look for drift
- In cloud → unexpected DHCP usage or dynamic IP → high risk indicator
- Integrate DHCP monitoring into broader asset discovery and management process

---

## References

* [DHCP Diagnostics](https://technet.microsoft.com/en-us/library/dn800671(v=ws.11).aspx)
* [DHCP Operational Events](https://technet.microsoft.com/en-us/library/dn800668(v=ws.11).aspx)
* [Get-Content Commandlet](https://technet.microsoft.com/en-us/library/ee176843.aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained and expanded with cloud + operator workflow + recommendations | Leo |
