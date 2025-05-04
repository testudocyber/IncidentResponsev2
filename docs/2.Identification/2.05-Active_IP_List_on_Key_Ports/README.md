# Active IP List on Key Ports

## Task Active IP List on Key Ports

2.04 – Active_Host_and_Service_Enumeration  
2.01 – Create a list of active IP addresses  
2.05 – Create a list of active IP addresses with key ports included  
2.07 – Scan ICS Equipment Ports  
2.06 – Scan all ports of all hosts on given network segment

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network segment(s), and network scanning software included in the team’s incident response kit.

> **Operator Note:** Always confirm scanning authorization from the network owner and ensure proper communication with affected system owners. Key ports scans can identify sensitive services and create alerts.

## Standards

* The team member identifies possibly compromised network segment(s).  
* The team member accesses a system that can scan the identified network segment(s).  
* The team member utilizes an IP-based network scanning utility to perform one of the following scanning tasks and directs the output to a text file for analysis:  
    * 2.04 – Active Host and Service Enumeration  
    * 2.01 – Create a list of active IP addresses  
    * 2.05 – Create a list of active IP addresses with key ports included  
    * 2.07 – Scan ICS Equipment Ports  
    * 2.06 – Scan all ports of all hosts on given network segment  
* The resulting scan data is compared to known network host data to determine anomalies present in the scanned network segment.

> **Operator Note:** Focus on high-value ports such as RDP (3389), SSH (22), HTTP/HTTPS (80/443), SMB (445). These are commonly exploited ports.

## End State

All active hosts, ports and services are carefully enumerated based on the specific sub-task accomplished and any anomalies or mis-configurations are identified.

> **Operator Note:** Unknown or unauthorized services running on key ports should immediately be escalated to IR lead.

## Manual Steps

A list of IP ranges can be provided by using the `-f IPRanges` option; otherwise, a range can be specified with `-i IPRange`. For each BD, the script will need to be ran.  
The script will need to be marked executable before running:

```bash
chmod +x NmapScript.sh
```

* For Task 2.04, run the NmapScript.sh and choose menu 1:

```
Selection:> 1
[+] Starting Nmap 7.80 ( https://nmap.org ) at ...
[+] Nmap done: ...
```

* For Task 2.02 (Active IP), run NmapScript.sh and choose menu 2:

```
Selection:> 2
[+] 192.168.69.1 is live!
[+] ...
```

* For Task 2.05 (this task), run the NmapScript.sh and choose menu 3:

```
Selection:> 3
[+] Starting Nmap 7.80 ( https://nmap.org )
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

> **Operator Note:** This scan focuses on scanning specific ports only, so if no ports are open on target, no output will be shown for those hosts.

* For Task 2.07 (ICS scan):

```
Selection:> 4
[!] It is possible for Scada devices to fail with an Nmap scan, do you want to continue? Y
[+] Nmap done...
```

* For Task 2.06 (all ports):

```
Selection:> 5
[+] Starting Nmap...
[+] Nmap done
```

## Dependencies

* If Nmap is not installed:

```bash
sudo apt install nmap
```

> **Operator Note:** On Windows, install via Chocolatey or official Nmap installer.

## Other available tools

* Angry IP Scanner  
* Advanced IP Scanner  
* Solarwinds IP Scanner

> **Operator Note:** Use GUI scanners only if authorized and validated for enterprise network use.

## References

[NMAP Site](https://nmap.org)  
[LAN Spy](http://lantricks.com/lanspy/)  
[NMAP Man Pages](http://linuxcommand.org/man_pages/nmap1.html)  
[Nmap Network Scanning Book](http://nmap.org/book/toc.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm with network owner the critical ports to scan.
- [ ] Schedule scans during off-peak hours to avoid impact.
- [ ] Use NmapScript.sh for consistency across team members.
- [ ] Validate scan output is complete and save as timestamped log.
- [ ] Investigate and escalate unknown services or unexpected open ports.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nmap, NmapScript.sh | Primary scanning tool for key ports |
| Windows/Linux/macOS | Netcat | Manual port connection testing |
| Linux/macOS | masscan | High-speed port discovery |
| Mobile | Fing | Lightweight local port scanning |

### Alternate Commands

#### Simple Nmap scan for common ports:

```bash
nmap -p 22,80,443,445,3389 192.168.1.0/24
```

#### Netcat manual connection check:

```bash
nc -vz 192.168.1.100 3389
```

### Best Practices

- Prioritize sensitive ports for IR (SSH, RDP, SMB).
- Avoid scanning ICS/SCADA segments without express approval.
- Correlate identified hosts with asset database and ticketing systems.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enhanced operator guidance and recommendations | Leo |
