# Create Active IP List

## Task Create Active IP List

2.04 – Active_Host_and_Service_Enumeration  
2.01 – Create a list of active IP addresses  
2.05 – Create a list of active IP addresses with key ports included  
2.07 – Scan ICS Equipment Ports  
2.06 – Scan all ports of all hosts on given network segment  

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network segment(s), and network scanning software included in the team’s incident response kit.  

> **Operator Note:** Before scanning, confirm ROE (Rules of Engagement) and authorization from network owner or mission lead. If ICS/SCADA involved, scanning may need to be coordinated and approved.

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
* The results should be saved and uploaded to a central repository for team-wide access and review.

> **Operator Note:** Use timestamped filenames and document scan scope (date, network, target ranges) for later validation and correlation.

## End State

All active hosts, ports and services are carefully enumerated based on the specific sub-task accomplished and any anomalies or mis-configurations are identified.  

> **Operator Note:** Review scan results with NetOps or SysAdmins to clarify unknown hosts or services and avoid false positives.

## Manual Steps

A list of IP ranges can be provided by using the `-f IPRanges` option; otherwise, a range can be specified with `-i IPRange`.  

For each BD, the script will need to be ran.  
The script will need to be marked executable before running:

```bash
chmod +x NmapScript.sh
```

* For Task 2.04, run the NmapScript.sh and choose menu 1:

```
1. 2.04 – Active_Host_and_Service_Enumeration
2. 2.01 – Create a list of active IP addresses
3. 2.05 – Create a list of active IP addresses with key ports included
4. 2.07 – Scan ICS Equipment Ports
5. 2.06 – Scan all ports of all hosts on given network segment
6. Exit

Selection:> 1
```

* A log will be generated to NmapScript.txt and the output will be shown to the user:

```
Selection:> 1
[+] Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-26 13:47 EDT
[+] Nmap scan report for 192.168.69.2
[+] Host is up (0.00084s latency).
[+] Not shown: 999 closed ports
[+] PORT   STATE SERVICE VERSION
[+] 53/tcp open  domain  dnsmasq 2.55
[+] MAC Address: 00:50:56:EB:3D:1D (VMware)
[+] Service detection performed.
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 17.23 seconds
```

* For Task 2.02, run the NmapScript.sh and choose menu 2:

```
Selection:> 2
[+] 192.168.69.1 is live!
[+] 192.168.69.2 is live!
[+] 192.168.69.142 is live!
[+] 192.168.69.254 is live!
[+] 192.168.69.132 is live!
[+] 192.168.69.137 is live!
```

* For Task 2.05, run the NmapScript.sh and choose menu 3:

```
Selection:> 3
[+] Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-26 13:56 EDT
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

* For Task 2.07, run the script and choose menu 4 (ICS warning):

```
Selection:> 4
[!] It is possible for Scada devices to fail with an Nmap scan, do you want to continue? Y
[+] Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-26 13:59 EDT
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

* For Task 2.06, run the script and choose menu 5:

```
Selection:> 5
[+] Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-26 14:01 EDT
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

## Dependencies

* If Nmap is not installed:

```bash
sudo apt install nmap
```

> **Operator Note:** On Windows, use Nmap installer or `choco install nmap`.

* Python (optional if running dns_hostname_mapping.py later).

## Other available tools

* Angry IP Scanner  
* Advanced IP Scanner  
* Solarwinds IP Scanner

> **Operator Note:** Validate licensing and environment compatibility before using third-party tools.

## References

[NMAP Site](https://nmap.org)  
[LAN Spy](http://lantricks.com/lanspy/)  
[NMAP Man Pages](http://linuxcommand.org/man_pages/nmap1.html)  
[Nmap Network Scanning Book](http://nmap.org/book/toc.html)  

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate network authorization and ROE before scanning.
- [ ] Identify the subnet/IP range from mission partner.
- [ ] Use NmapScript.sh for consistency.
- [ ] Save all output in standardized folder and filename format.
- [ ] Review active IPs against known good baselines.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nmap, NmapScript.sh | Primary active IP discovery |
| Windows | PowerShell Test-Connection | Alternate ping sweep |
| Linux/macOS | arp-scan | Lightweight local discovery |
| Mobile | Fing | Lightweight subnet scan |

### Alternate Commands

#### Linux ARP scan:

```bash
sudo arp-scan --interface=eth0 --localnet
```

#### Linux Bash simple ping sweep:

```bash
for ip in $(seq 1 254); do ping -c 1 192.168.1.$ip | grep "bytes from"; done
```

### Best Practices

- Schedule scans during low traffic times.
- Confirm scan completion and review NmapScript.txt after every task.
- Avoid scanning ICS without explicit permission.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.1 | Finalized version with revision history moved | Leo |
