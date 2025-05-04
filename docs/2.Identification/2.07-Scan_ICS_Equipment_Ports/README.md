# Scan ICS Equipment Ports

## Task Scan ICS Equipment Ports

2.04 – Active_Host_and_Service_Enumeration  
2.01 – Create a list of active IP addresses  
2.05 – Create a list of active IP addresses with key ports included  
2.07 – Scan ICS Equipment Ports  
2.06 – Scan all ports of all hosts on given network segment

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network segment(s), and network scanning software included in the team’s incident response kit.

> **Operator Note:** ICS environments are extremely sensitive. Nmap scans can cause device failure or process disruption. Always coordinate with system owners and obtain written approval.

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

> **Operator Note:** Use `-T1` or `-T2` scan timing options in Nmap to reduce scan aggressiveness when targeting ICS environments.

## End State

All active hosts, ports and services are carefully enumerated based on the specific sub-task accomplished and any anomalies or mis-configurations are identified.

> **Operator Note:** Carefully review ICS results with facility or SCADA engineers to validate accuracy and rule out false positives.

## Manual Steps

A list of IP ranges can be provided by using the `-f IPRanges` option; otherwise, a range can be specified with `-i IPRange`.  
For each BD, the script will need to be ran.  
The script will need to be marked executable before running:

```bash
chmod +x NmapScript.sh
```

* For Task 2.04, run the NmapScript.sh and choose menu 1:

```
Selection:> 1
[+] Starting Nmap...
```

* For Task 2.02 (active IP list):

```
Selection:> 2
[+] 192.168.69.1 is live!
```

* For Task 2.05 (key ports):

```
Selection:> 3
[+] Starting Nmap...
```

* For Task 2.07 (this task — ICS scan):

```
Selection:> 4
[!] It is possible for SCADA devices to fail with an Nmap scan, do you want to continue? Y
[+] Starting Nmap 7.80 ( https://nmap.org )
[+] Nmap done: 256 IP addresses (6 hosts up) scanned in 3.41 seconds
```

> **Operator Note:** This mode is targeted for ICS systems. However, even so, it may disrupt sensitive equipment. Use the `--scan-delay` and `-T0` or `-T1` flags in custom scans if needed.

* For Task 2.06 (full port scan):

```
Selection:> 5
[+] Starting Nmap...
```

## Dependencies

* If Nmap is not installed:

```bash
sudo apt install nmap
```

> **Operator Note:** Windows: `choco install nmap` or official installer.

## Other available tools

* Angry IP Scanner  
* Advanced IP Scanner  
* Solarwinds IP Scanner

> **Operator Note:** These tools are NOT recommended for ICS scanning — only use Nmap or highly controlled protocols for ICS/SCADA equipment.

## References

[NMAP Site](https://nmap.org)  
[LAN Spy](http://lantricks.com/lanspy/)  
[NMAP Man Pages](http://linuxcommand.org/man_pages/nmap1.html)  
[Nmap Network Scanning Book](https://nmap.org/book/toc.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain written approval before scanning ICS/SCADA environments.
- [ ] Use NmapScript.sh menu 4 for this task.
- [ ] If custom scanning, use `-T0` or `-T1` to minimize impact.
- [ ] Save NmapScript.txt results with timestamp and segment identifier.
- [ ] Validate results with facility engineers before escalating.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nmap, NmapScript.sh | Primary ICS scan tool (use with caution) |
| Windows/Linux/macOS | Nmap CLI with timing/scanning options | Low and slow ICS scanning |
| Mobile | Fing | NOT recommended for ICS, use only for basic discovery if permitted |

### Example Alternate Commands (Recommended for ICS)

#### Nmap low impact ICS scan:

```bash
nmap -sS -p 1-1024 --scan-delay 1s -T1 192.168.1.0/24
```

#### Nmap SCADA targeted (Modbus/TCP):

```bash
nmap -sU -p 502 --script modbus-discover 192.168.1.0/24
```

> **Operator Note:** Use SCADA-specific NSE scripts carefully and only with approval.

### Best Practices

- Avoid scanning during operational hours.
- Consult with ICS/SCADA vendors or site engineers.
- Log, review, and archive all scan artifacts.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched ICS/SCADA operator guidance and recommendations | Leo |
