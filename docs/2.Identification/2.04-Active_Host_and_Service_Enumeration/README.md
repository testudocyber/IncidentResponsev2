# Active Host and Service Enumeration

## Task Active Host and Service Enumeration

2.04 – Active_Host_and_Service_Enumeration  
2.01 – Create a list of active IP addresses  
2.05 – Create a list of active IP addresses with key ports included  
2.07 – Scan ICS Equipment Ports  
2.06 – Scan all ports of all hosts on given network segment

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network segment(s), and network scanning software included in the team’s incident response kit.

> **Operator Note:** Always verify network access rules, ROE, and mission owner authorization before performing active scans. In critical infrastructure environments, scanning may cause disruption.

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

> **Operator Note:** Service identification scans can be noisy. When possible, use timing options in Nmap to reduce network load during production hours.

## End State

All active hosts, ports and services are carefully enumerated based on the specific sub-task accomplished and any anomalies or mis-configurations are identified.

> **Operator Note:** After scanning, work with mission partner system administrators to validate unknown or unauthorized services.

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

* For Task 2.01 (Active IP):

```
Selection:> 2
[+] 192.168.69.1 is live!
[+] 192.168.69.2 is live!
[+] ...
```

* For Task 2.05 (Key Ports):

```
Selection:> 3
[+] Starting Nmap 7.80 ( https://nmap.org )
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

* For Task 2.07 (ICS scan with caution):

```
Selection:> 4
[!] It is possible for Scada devices to fail with an Nmap scan, do you want to continue? Y
[+] Starting Nmap...
[+] Nmap done
```

* For Task 2.06 (All Ports):

```
Selection:> 5
[+] Starting Nmap...
[+] Nmap done
```

> **Operator Note:** Always carefully check NmapScript.txt logs for errors and complete scan information.

## Dependencies

* Nmap

```bash
sudo apt install nmap
```

> **Operator Note:** Ensure Nmap is latest stable version for best service fingerprinting accuracy.

## Other available tools

* Angry IP Scanner  
* Advanced IP Scanner  
* Solarwinds IP Scanner

> **Operator Note:** Validate use of third-party scanning tools in mission environments before installation.

## References

[NMAP Site](https://nmap.org)  
[LAN Spy](http://lantricks.com/lanspy/)  
[NMAP Man Pages](http://linuxcommand.org/man_pages/nmap1.html)  
[Nmap Network Scanning Book](http://nmap.org/book/toc.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Verify scanning approval with mission owner or IT administrator.
- [ ] Determine subnet scope and schedule scans during low-traffic hours.
- [ ] Prefer NmapScript.sh for consistency and record keeping.
- [ ] Backup NmapScript.txt results and save with timestamp.
- [ ] Review services for anomalies and unexpected open ports.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nmap + NmapScript.sh | Primary host and service enumeration |
| Windows | Netcat | Manual port testing and banner grabbing |
| Linux/macOS | masscan | High-speed port scanning |
| Mobile | Fing | Basic service discovery |

### Alternate Commands

#### Netcat (manual check):

```bash
nc -v 192.168.1.5 80
```

#### Linux Nmap quick TCP full port scan:

```bash
nmap -p- 192.168.1.0/24
```

### Best Practices

- ICS environments must be scanned only with explicit permission.
- Validate unknown services with application owners or IT.
- Log and track findings in central incident documentation.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched operator notes and recommendations | Leo |
