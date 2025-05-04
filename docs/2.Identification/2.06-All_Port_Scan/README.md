# All Port Scan

## Task All Port Scan

2.04 – Active_Host_and_Service_Enumeration  
2.01 – Create a list of active IP addresses  
2.05 – Create a list of active IP addresses with key ports included  
2.07 – Scan ICS Equipment Ports  
2.06 – Scan all ports of all hosts on given network segment

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network segment(s), and network scanning software included in the team’s incident response kit.

> **Operator Note:** Full port scanning is inherently intrusive and noisy. Ensure coordination with network owner and obtain explicit authorization, especially in production environments.

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

> **Operator Note:** Scans of all ports will take longer and may trigger IDS/IPS alerts. Plan scan windows accordingly.

## End State

All active hosts, ports and services are carefully enumerated based on the specific sub-task accomplished and any anomalies or mis-configurations are identified.

> **Operator Note:** Anomalous services on high ports should be reviewed carefully. Common services usually operate on well-known ports.

## Manual Steps

A list of IP ranges can be provided by using the `-f IPRanges` option; otherwise, a range can be specified with `-i IPRange`. For each BD, the script will need to be ran.  
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

```bash
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

* For Task 2.06 (this task), run the script and choose menu 5. This will scan for all ports on the target/target list:

```bash
Selection:> 5
[+] Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-26 14:01 EDT
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

> **Operator Note:** Scan will attempt TCP ports 1-65535 for each live host. Results should be carefully analyzed for unexpected open ports.

## Dependencies

* If Nmap is not installed:

```bash
sudo apt install nmap
```

> **Operator Note:** On Windows, install via official installer or `choco install nmap`.

## Other available tools

* Angry IP Scanner  
* Advanced IP Scanner  
* Solarwinds IP Scanner

> **Operator Note:** GUI scanners typically do not support full-port scanning. Use CLI tools (Nmap) for comprehensive coverage.

## References

[NMAP Site](https://nmap.org)  
[LAN Spy](http://lantricks.com/lanspy/)  
[NMAP Man Pages](http://linuxcommand.org/man_pages/nmap1.html)  
[Nmap Network Scanning Book](http://nmap.org/book/toc.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm authorization to perform full port scan.
- [ ] Select the correct network and IP range to target.
- [ ] Use NmapScript.sh option 5 for consistency and logging.
- [ ] Save results securely and with timestamped filenames.
- [ ] Analyze results carefully and report unauthorized open ports.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nmap, NmapScript.sh | Primary full port scanner |
| Windows/Linux/macOS | Netcat | Manual port testing |
| Linux/macOS | masscan | High-speed full port scanner (caution required) |
| Mobile | Fing | Lightweight alternative (not suitable for full port scans) |

### Alternate Commands

#### Full TCP port scan using Nmap:

```bash
nmap -p- 192.168.1.0/24
```

#### Netcat manual connection (single port check):

```bash
nc -vz 192.168.1.5 8080
```

### Best Practices

- Avoid running full port scans during peak hours.
- Coordinate with system owners ahead of scans.
- Document and report anomalous or high-risk open ports immediately.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched operator notes and recommendations | Leo |
