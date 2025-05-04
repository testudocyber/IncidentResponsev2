# 4.30 Security Onion Hunting

## Task

Conduct advanced threat hunting using Security Onion to identify suspicious or malicious activities, such as:

* Command and Control (C2) beaconing
* Malware artifacts
* Abnormal network traffic or protocol use
* Exploit attempts
* Lateral movement and privilege escalation

---

## Conditions

* Security Onion installed and configured to receive log forwarding from Wazuh agents.
* Endpoints configured with Sysmon using ionstorm config.
* Wazuh agent installed and forwarding logs (Sysmon + Security logs).
* Network traffic is captured and PCAPs are available for analysis.
* Operator has access to Kibana, Hunt, so-event, and PCAP review tools.

---

## Standards

Threat hunting must cover detection and analysis for the following threat activity categories with the appropriate tools and techniques:

| Threat Type | Tools | Example Indicators / Where to Look |
|-------------|-------|------------------------------------|
| C2 Beaconing | Zeek, Suricata, Hunt, Kibana | DNS queries, JA3/SNI anomalies, periodic HTTP/S POST requests, SMB beacons |
| Malware Artifacts | Sysmon, Kibana, Hunt, so-event | File writes, autoruns, suspicious parent-child process creation |
| Abnormal Traffic | Zeek, Suricata, PCAP analysis | Unusual protocols, large outbound traffic, DNS tunneling |
| Exploit Attempts | Suricata, so-event | RPC/DCERPC, SMB exploits, brute force logins |
| Lateral Movement | Sysmon, Suricata, Zeek | CreateRemoteThread, WinRM, SMB access, credential dumping |

---

## End State

* Hunting is performed thoroughly and findings documented.
* Any suspicious activity is triaged and escalated as required.
* Eradication actions are initiated if malicious activity is confirmed.

---

## Notes

Security Onion provides a unified interface for analyzing host and network telemetry:

* **Kibana:** Powerful log search and visualization
* **Hunt:** Fast IOC and behavior-based hunting interface
* **so-event:** Correlated IDS alerts and event metadata
* **PCAP Analysis (so-pcap, Wireshark):** Deep packet inspection

**Hunting requires combining IOC-based and behavior-based methods.**

---

## Manual Steps

### Step 1 — Access Security Onion Interfaces

#### Kibana

- Navigate to `https://[SecurityOnionIP]`
- Click **Kibana**
- Use search to locate event types, suspicious processes, and network activity.

**Example Screenshot:**

![Kibana Search](./images/so-kibana-search.png)

---

#### Hunt

- From Security Onion, open **Hunt**.
- Use Hunt for rapid IOC queries and pattern-based analysis.

**Example Screenshot:**

![Hunt IOC Dashboard](./images/so-hunt-ioc-dashboard.png)

---

#### so-event

- View alerts and correlated metadata.
- Pivot between events, PCAPs, and logs for enriched context.

**Example Screenshot:**

![SO Event Dashboard](./images/so-event-dashboard.png)

These first examples are events from Sysmon showing some of the common tactics used by threat actors.  The events have been generated using MetaSploit and  CobaltStrike  

### Lateral Movement  
A common tactic for lateral movement is to push an executable to a remote system and start a service, similar to how psexec.exe works  

#### Metasploit lateral movement using a service to initiate callback  
```
Registry value set:
RuleName: MitreRef=T1060,Technique=Registry Autorun Keys,Tactic=Persistence
EventType: SetValue
UtcTime: 2021-06-12 03:49:29.470
ProcessGuid: {298db6a9-2c67-60c4-eeb3-000000000000}
ProcessId: 620
Image: C:\Windows\system32\services.exe
TargetObject: HKLM\System\CurrentControlSet\Services\DpRHdvsE\Start
Details: DWORD (0x00000003)
```

#### Metasploit lateral movement using a service to initiate callback, second event  
```
Registry value set:
RuleName: MitreRef=T1060,Technique=Registry Autorun Keys,Tactic=Persistence
EventType: SetValue
UtcTime: 2021-06-12 03:49:29.470
ProcessGuid: {298db6a9-2c67-60c4-eeb3-000000000000}
ProcessId: 620
Image: C:\Windows\system32\services.exe
TargetObject: HKLM\System\CurrentControlSet\Services\DpRHdvsE\ImagePath
Details: %%COMSPEC%% /b /c start /b /min powershell.exe -nop -w hidden -noni -c "if([IntPtr]::Size -eq 4){$b='powershell.exe'}else{$b=$env:windir+'\syswow64\WindowsPowerShell\v1.0\powershell.exe'};$s=New-Object System.Diagnostics.ProcessStartInfo;$s.FileName=$b;$s.Arguments='-noni -nop -w hidden -c &([scriptblock]::create((New-Object System.IO.StreamReader(New-Object System.IO.Compression.GzipStream((New-Object System.IO.MemoryStream(,[System.Convert]::FromBase64String(''H4sIAMguxGACA7shortentedforreadability''))),[System.IO.Compression.CompressionMode]::Decompress))).ReadToEnd()))';$s.UseShellExecute=$false;$s.RedirectStandardOutput=$true;$s.WindowStyle='Hidden';$s.CreateNoWindow=$true;$p=[System.Diagnostics.Process]::Start($s);"
```  

Process create is a useful event to look for.  It is common for an infected to host to create additional processes when performing other tasks such as, credential dumping or lateral movement  

#### Process create event  
```
Process Create:
RuleName: technique_id=T1086,technique_name=PowerShell
UtcTime: 2018-09-01 02:26:12.384
ProcessGuid: {79579f2b-f8c4-5b89-0000-0010a8e24300}
ProcessId: 5568
Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
FileVersion: 10.0.14393.0 (rs1_release.160715-1616)
Description: Windows PowerShell
Product: Microsoft® Windows® Operating System
Company: Microsoft Corporation
CommandLine: powershell.exe  -nop -w hidden -encodedcommand JABzAD0ATgBlAHcALQBPAGIAagBlAGMAdAAgAEkAT
CurrentDirectory: C:\Windows\system32\
User: computer\user
LogonGuid: {79579f2b-f60c-5b89-0000-0020415e0600}
LogonId: 0x65E41
TerminalSessionId: 1
IntegrityLevel: High
Hashes: SHA1=044A0CF1F6BC478A7172BF207EEF1E201A18BA02,MD5=097CE5761C89434367598B34FE32893B,SHA256=BA4038FD20E474C047BE8AAD5BFACDB1BFC1DDBE12F803F473B7918D8D819436,IMPHASH=CAEE994F79D85E47C06E5FA9CDEAE453
ParentProcessGuid: {79579f2b-f737-5b89-0000-0010a2d22200}
ParentProcessId: 2764
ParentImage: C:\Windows\System32\cmd.exe
ParentCommandLine: "C:\Windows\system32\cmd.exe"
```  

#### Process create event for lateral movement  
```
Process Create:
RuleName: -
UtcTime: 2021-06-24 02:44:23.786
ProcessGuid: {f3da3d38-f187-60d3-b099-6a1600000000}
ProcessId: 7072
Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
FileVersion: 10.0.14393.206 (rs1_release.160915-0644)
Description: Windows PowerShell
Product: Microsoft® Windows® Operating System
Company: Microsoft Corporation
OriginalFileName: PowerShell.EXE
CommandLine: powershell.exe -nop -w hidden -enc SQBFAFgAIAAoACgAbgBlAHcALQBvA==
CurrentDirectory: C:\WINDOWS\system32\
User: computer\user
LogonGuid: {f3da3d38-f187-60d3-e798-6a1600000000}
LogonId: 0x166A98E7
TerminalSessionId: 0
IntegrityLevel: High
Hashes: MD5=097CE5761C89434367598B34FE32893B,SHA256=BA4038FD20E474C047BE8AAD5BFACDB1BFC1DDBE12F803F473B7918D8D819436,IMPHASH=CAEE994F79D85E47C06E5FA9CDEAE453
ParentProcessGuid: {f3da3d38-d166-60cb-4b31-020000000000}
ParentProcessId: 2868
ParentImage: C:\Windows\System32\wbem\WmiPrvSE.exe
ParentCommandLine: C:\WINDOWS\system32\wbem\wmiprvse.exe -secured -Embedding
```  

Malware almost always has to connect back to a C2 system for follow on instructions and to maintain persistance  

#### Network event.  This is an example of powershell used as the initial infection making a call back.  Not a great example, since it was done locally in vmware  
```
Network connection detected:
RuleName: technique_id=T1218,technique_name=Signed Binary Proxy Execution
UtcTime: 2018-09-01 02:26:19.331
ProcessGuid: {79579f2b-f8c7-5b89-0000-0010f4864400}
ProcessId: 5552
Image: C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe
User: computer\user
Protocol: tcp
Initiated: true
SourceIsIpv6: false
SourceIp: 192.168.1.130
SourceHostname: computer
SourcePort: 1907
SourcePortName: 
DestinationIsIpv6: false
DestinationIp: 192.168.1.143
DestinationHostname: 
DestinationPort: 443
DestinationPortName: https
```  

### Credential Events  
#### mimikatz grabbing credentials from current logged on users
```
Process accessed:
RuleName: technique_id=T1003,technique_name=Credential Dumping
UtcTime: 2018-09-01 02:40:12.833
SourceProcessGUID: {79579f2b-fc0c-5b89-0000-001097eb6f00}
SourceProcessId: 5212
SourceThreadId: 6996
SourceImage: C:\Windows\System32\rundll32.exe
TargetProcessGUID: {79579f2b-f5fa-5b89-0000-00105a9c0000}
TargetProcessId: 604
TargetImage: C:\Windows\system32\lsass.exe
GrantedAccess: 0x1010
CallTrace: C:\Windows\SYSTEM32\ntdll.dll+a6574|C:\Windows\System32\KERNELBASE.dll+20edd|UNKNOWN(000001C7A2AA710D)
```  

#### mimikatz grabbing hashes from the local accounts  
```
Process accessed:
RuleName: technique_id=T1003,technique_name=Credential Dumping
UtcTime: 2018-09-01 02:45:26.030
SourceProcessGUID: {79579f2b-fd45-5b89-0000-0010c5657300}
SourceProcessId: 6420
SourceThreadId: 4172
SourceImage: C:\Windows\System32\rundll32.exe
TargetProcessGUID: {79579f2b-f5fa-5b89-0000-00105a9c0000}
TargetProcessId: 604
TargetImage: C:\Windows\system32\lsass.exe
GrantedAccess: 0x1FFFFF
CallTrace: C:\Windows\SYSTEM32\ntdll.dll+a6574|C:\Windows\System32\KERNELBASE.dll+20edd|UNKNOWN(0000022B28BB1D25)
```  

### Process Injection  
```
Registry object added or deleted:
CreateRemoteThread detected:
RuleName: -
UtcTime: 2021-06-24 03:10:58.927
SourceProcessGuid: {f3da3d38-f187-60d3-b099-6a1600000000}
SourceProcessId: 7072
SourceImage: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
TargetProcessGuid: {f3da3d38-d163-60cb-f2d1-010000000000}
TargetProcessId: 1316
TargetImage: C:\Program Files\VMware\VMware Tools\vmtoolsd.exe
NewThreadId: 4032
StartAddress: 0x000001A4BC400000
StartModule: -
StartFunction: -
```  
---

### Step 2 — Execute Hunting Queries

#### C2 Beaconing

**DNS Example (Kibana or Hunt)**

```lucene
dns.question.name:*.xyz AND dns.answers.data:"<external_ip>"
```

**HTTP Periodic Beaconing**

```lucene
event.module:http AND event.dataset:zeek.http AND http.request.method:POST
```

**Cobalt Strike Beacon Pattern (Zeek)**

```lucene
server.packets >8 AND server.packets <12
```

**PCAP (Wireshark)**

```wireshark
ip.dst == [malicious_ip] && tcp.port == 443
```

---

#### Malware Artifacts

**Registry Autorun Key Modification (Sysmon)**

```plaintext
HKLM\System\CurrentControlSet\Services\[malicious]\Start
```

```lucene
event.module:"sysmon" AND event.dataset:"registry_value_set"
```

**Malware Process Creation (Sysmon)**

```lucene
event.module:"sysmon" AND event.dataset:"process_create" AND process.command_line.keyword:"powershell -nop"
```

**Executable File Dropped**

```lucene
file.mime_type.keyword: application/x-dosexec
```

---

#### Abnormal Protocol or Traffic Patterns

**SMB Executable File Transfer**

```lucene
rule.name: "ET POLICY SMB Executable File Transfer"
event.dataset.keyword: smb_files
file.name.keyword: *.exe
```

**DNS Tunneling (Zeek)**

```lucene
dns.query.name.keyword:* AND dns.query.type_name.keyword:A
```

---

#### Exploit Attempts

**Remote Service Control Access (DCERPC)**

```lucene
rule.name: "ET RPC DCERPC SVCCTL - Remote Service Control Manager Access"
dce_rpc.operation.keyword: OpenSCManagerA
```

**WinRM (Lateral Movement)**

```lucene
rule.name: "ET POLICY WinRM wsman Access - Possible Lateral Movement"
rule.metadata.tag.keyword: WinRM
```

---

#### Lateral Movement and Privilege Escalation

**Remote Thread Injection (Sysmon CreateRemoteThread)**

```lucene
event.module:"sysmon" AND event.dataset:"create_remote_thread"
```

**Credential Dumping (LSASS access)**

```lucene
event.module:"sysmon" AND event.dataset:"create_remote_thread" AND winlog.event_data.targetImage:"*lsass.exe"
```

**NTLM Pass-the-Hash (Zeek NTLM Events)**

```lucene
event.dataset.keyword: ntlm
```

---

### Step 3 — Analyze PCAP (optional step)

#### Using so-pcap GUI

* Select timeframe or relevant alert from so-event.
* Download PCAP or analyze inline using Wireshark.

**Example Wireshark Filters**

```wireshark
tcp.port == 443 and ip.dst == [suspicious_ip]
```

* Look for SSL/TLS handshakes with abnormal JA3 or periodic beaconing.

---

## Running Scripts (optional)

#### Example Kibana Export

```json
GET _search
{
  "query": {
    "match": {
      "dns.question.name": "maliciousdomain.com"
    }
  }
}
```

#### Blocking Malicious Domains

```powershell
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "`n127.0.0.1 malicious.badactor.com"
```

---

## Dependencies

* Fully deployed Security Onion Stack (Kibana, Hunt, so-event, so-pcap)
* Sysmon + ionstorm config installed on endpoints
* Wazuh agent forwarding logs
* Analyst operator trained on MITRE ATT&CK, network analysis, and incident response playbooks

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Zeek | Cross-platform | Installed | Network protocol inspection and correlation |
| Suricata | Cross-platform | Installed | IDS/IPS alerting |
| Sysmon | Windows | Installed on endpoints | Process, Registry, and network activity monitoring |
| Wireshark | Cross-platform | Installed | PCAP analysis |
| Sigma Rules + Elastalert | Optional | Installed or cloud | Convert hunting queries to detection rules |

---

## Operator Recommendations and Best Practices

### Operator Checklist

- [ ] Access Kibana, Hunt, and so-event interfaces.
- [ ] Run hunting queries for C2, malware artifacts, abnormal traffic, exploits, and lateral movement.
- [ ] Analyze PCAP if necessary for beaconing and covert channels.
- [ ] Document findings in hunt log with screenshots, queries, and notes.
- [ ] Submit findings for eradication or closure as appropriate.

### Best Practices

* Pivot frequently between host and network telemetry.
* Use IOC and behavior-based queries together.
* Work collaboratively and validate suspicious findings.
* Document everything for Lessons Learned and detection rule creation.

---

## References

* [Windows Event Log Examples](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES)
* [Sigma Rules](https://github.com/SigmaHQ/sigma/tree/master/rules/windows)
* [RUNDLL32 Internet Connection Article](https://www.cobaltstrike.com/blog/why-is-rundll32-exe-connecting-to-the-internet/)
* [Security Onion Quick Malware Analysis](https://www.youtube.com/watch?v=KBjr1fdb3jY)
* [Security Onion Malware Traffic Analysis](https://blog.securityonion.net/2021/07/quick-malware-analysis-malware-traffic.html)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.3 | FULL MERGE: Original content + screenshots + advanced standards + multi-platform procedures and examples for technical operators | Leo |
