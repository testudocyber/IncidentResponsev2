# 4.28 Stop C2 Beaconing

## Task

Stop Command and Control (C2) Beaconing

---

## Conditions

* Given a network environment with potential malware infections or unauthorized backdoors
* Monitoring tools available to detect and analyze outbound network traffic
* Administrative access to DNS, firewall, and endpoint security infrastructure

---

## Standards

* Detect and identify C2 beaconing behavior using network, DNS, and endpoint analysis.
* Identify and document suspicious DNS names, IP addresses, and ports associated with C2 channels.
* Block malicious IPs/domains at the firewall, endpoint, and DNS resolver.
* Flush DNS caches to prevent re-resolution.
* Create DNS sinkhole zones for malicious domains to prevent external resolution.
* Remove associated malware/backdoors/persistence mechanisms from infected hosts.
* Validate and monitor for beaconing cessation.

---

## End State

* All C2 beaconing activity has ceased.
* Suspicious domains no longer resolve externally.
* Endpoint persistence mechanisms have been removed.
* Network and endpoint telemetry confirms eradication.
* IOC and response actions are documented.

---

## Notes

**C2 Beaconing Techniques Include:**

* DNS tunneling (subdomain beaconing)
* HTTPS encrypted beaconing (POST and GET requests to staging servers)
* HTTP non-standard port beaconing (8080, 8443, etc.)
* TCP/IP covert channels
* Cloud relay infrastructure usage

**Known Ports and Protocols (Example — Cobalt Strike):**

| Protocol | Port | Notes |
|----------|------|-------|
| HTTPS (TCP) | 443 | Default beaconing (encrypted) |
| HTTP (TCP) | 80 | C2 in clear-text |
| TCP (custom ports) | 50050, 2222, 8080, 8443 | Often used in red team or attacker deployments |
| DNS (UDP/TCP) | 53 | DNS beaconing and tunneling |

Cobalt Strike — one of the most common commercial/offensive C2 platforms — often uses HTTP/HTTPS and DNS-based beaconing as default profiles.

---

## Manual Steps

### Step 1 → Detect Beaconing

#### Using Network Tools (Zeek, Wireshark, SIEM)

* Look for frequent, regular interval outbound connections to unknown domains/IPs.
* Review DNS queries → Look for strange subdomains and high entropy.

##### Example Zeek command:

```bash
zeek -r traffic.pcap dns.log http.log conn.log
```

##### Suspicious DNS Example:

```
xyz987dnscommand.c2malicious.net
```

#### Using EDR / AV Telemetry

* Detect parent processes generating outbound connections.
* Look for svchost.exe, rundll32.exe, or powershell.exe contacting external IPs.

#### Using DNS Server Logs

* Windows DNS → Enable and query DNS analytical logs.
* BIND/Linux → Review `named` query logs.

---

### Step 2 → Isolate Infected Hosts

#### EDR Isolation

```powershell
falconctl -g --isolation
```

#### Manual Isolation

* Disable network adapters.
* Block outbound access via local firewall rules.

```powershell
New-NetFirewallRule -DisplayName "Block C2 IP" -Direction Outbound -RemoteAddress <C2_IP> -Action Block
```

---

### Step 3 → Block Malicious Domains and IPs

#### Flush DNS Cache (Local)

```powershell
ipconfig /flushdns
```

```bash
sudo systemd-resolve --flush-caches
```

```bash
sudo killall -HUP mDNSResponder
```

#### Block at DNS (Sinkhole)

##### Windows DNS

1. Open DNS Manager.
2. New Zone → Primary → `maliciousdomain.com`.
3. Disable dynamic updates.
4. Add A record → Point to 127.0.0.1.

##### Linux (Bind)

```bash
zone "maliciousdomain.com" {
    type master;
    file "/etc/bind/db.null";
};
```

##### AWS Route53 / Azure DNS

* Create Private Hosted Zone → Add A record → Point to internal IP (blocking exfil).

#### Block at Network Firewall

```bash
iptables -A OUTPUT -p tcp --dport 443 -d <C2_IP> -j DROP
```

* Block HTTP/HTTPS, DNS, and custom ports associated with Cobalt Strike or other C2 infrastructure.

---

### Step 4 → Remove Malware Backdoors (Related to Beaconing)

* Use Cerberus (as in **4.27**) to identify injected threads and malicious memory artifacts.

```powershell
Cerberus.exe --analyze
Cerberus.exe --kill
```

* Validate persistence mechanisms (Autoruns, Scheduled Tasks, Services, Registry Run keys).
* Remove accordingly across:

##### Windows

```powershell
Remove-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Backdoor"
```

##### macOS

```bash
launchctl remove com.malware.launcher
```

##### Linux

```bash
crontab -l
crontab -r
```

---

### Step 5 → Monitor and Validate

* Confirm sinkholed domains resolve to localhost/internal IP.
* Monitor network traffic for any fallback domains or C2 IPs.
* Review EDR/network telemetry for re-attempts.

```bash
zeek -i eth0 dns.log http.log conn.log
```

---

## Running Script Examples

##### Block Domain via Hosts File (Quick Block)

```powershell
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "`n127.0.0.1 beacon.badactor.com"
```

##### Block IP via Firewall (Linux)

```bash
iptables -A OUTPUT -p tcp -d 192.168.100.100 --dport 8080 -j DROP
```

---

## Dependencies

* DNS server access (internal and cloud hosted)
* Firewall or proxy policy management
* EDR with process and network telemetry
* SIEM or Zeek for traffic analysis
* Administrative access to endpoints

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Zeek | Cross-platform | Installed | Network and DNS beaconing detection |
| Splunk | Cross-platform | Installed | Detect C2 beaconing |
| CrowdStrike / SentinelOne | Cross-platform | Installed | Endpoint and C2 process detection |
| Cerberus | Windows | Included in toolkit | Identify + kill injected threads |
| BIND | Linux | Installed | Sinkhole domains |
| Windows DNS Server | Windows | Installed | Create sinkhole zones |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Detect and confirm C2 beaconing activity
- [ ] Identify domains/IPs and communication ports
- [ ] Flush DNS caches
- [ ] Block or sinkhole domains via DNS
- [ ] Block outbound IP traffic via firewall (host/network)
- [ ] Remove malware backdoors and persistence
- [ ] Validate beaconing has stopped
- [ ] Monitor for re-infection or fallback channels

### Best Practices

* Sinkhole malicious domains for visibility and validation rather than simply blocking.
* Document all IOC findings and coordinate with threat intelligence teams.
* Monitor after eradication for new domains, domain-fluxing, or alternate C2 channels.
* Use layered blocking (host, network, DNS) to prevent re-establishment.

---

## References

* [Cobalt Strike C2 Profiles](https://www.cobaltstrike.com/help-beacon)
* [Zeek DNS Tunneling Detection](https://zeek.org/)
* [Windows DNS Sinkhole Zones](https://learn.microsoft.com/en-us/windows-server/networking/dns/deploy/primary-zones)
* [CrowdStrike Isolation Documentation](https://www.crowdstrike.com)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.2 | Integrated original instructions + expanded with Cobalt Strike ports, advanced C2 detection, blocking, and DNS handling | Leo |
