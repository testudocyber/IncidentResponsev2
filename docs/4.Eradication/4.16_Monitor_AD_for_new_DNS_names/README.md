# 4.16 Monitor AD for New DNS Names (Local + Cloud)

## Task

Monitor Active Directory (AD) DNS zones for new domain names and DNS entries that may indicate unauthorized hosts, attacker infrastructure, or persistence mechanisms.

---

## Conditions

Given:

* Access to Domain Controllers with DNS Server role installed
* Domain account with required permissions
* Workstation with Remote Server Administration Tools (RSAT) or PowerShell
* Access to cloud provider DNS services if applicable (AWS, Azure, GCP)

---

## Standards

* Team member opens DNS Console → navigates to Forward Lookup Zones
* Export records → Save locally
* Repeat task on defined schedule → Compare for new entries
* OR → Team member uses command-line tool/script to export DNS zones
* Repeat and compare outputs periodically to detect new entries

---

## End State

All new DNS zone records are discovered, recorded, validated, and confirmed as authorized or escalated.

---

## Notes

This task can be repetitive. Operators should automate where possible:

* PowerShell → preferred for export + diff
* File comparison → manual (Kdiff) or automated PowerShell compare-object
* Cloud DNS → API / CLI polling + diff

---

## Manual Steps

### Windows Server DNS Console (Manual Export)

1. Open **DNS Management Console**
2. Expand **Forward Lookup Zones**
3. Right-click Zone → Export List → Save as `.txt` or `.csv`
4. Repeat later → Compare saved lists manually

---

### PowerShell → Export DNS Records (Recommended)

#### All DNS Records (WMI Method)

```powershell
Get-WmiObject -Namespace root\MicrosoftDNS -Class MicrosoftDNS_ResourceRecord | 
Select-Object __Class, ContainerName, DomainName, RecordData, OwnerName |
Out-GridView
```

#### Root Hints

```powershell
Get-WmiObject -Namespace root\MicrosoftDNS -Class MicrosoftDNS_ResourceRecord |
Where-Object {$_.DomainName -eq "..roothints"} |
Out-GridView
```

---

### PowerShell → Export and Compare (Automated Detection)

#### Export

```powershell
Get-DnsServerResourceRecord -ZoneName "corp.example.com" | 
Select-Object HostName, RecordType, RecordClass, RecordData |
Export-Csv "C:\Monitor\AD_DNS_Snapshot.csv" -NoTypeInformation
```

#### Compare

```powershell
Compare-Object -ReferenceObject (Get-Content C:\Monitor\AD_DNS_Snapshot.csv) `
-DifferenceObject (Get-Content C:\Monitor\AD_DNS_Snapshot_2.csv)
```

> **Operator Note:** Schedule export + compare daily → alert if diff found

---

## Cloud Environment DNS Monitoring

### AWS Route53

```bash
aws route53 list-resource-record-sets --hosted-zone-id ZONEID
```

- Save JSON → Compare daily → Look for new entries

### Azure DNS

```powershell
Get-AzDnsRecordSet -ResourceGroupName "RG" -ZoneName "example.com"
```

- Save to file → Compare

### GCP Cloud DNS

```bash
gcloud dns record-sets list --zone="example-zone"
```

- Export and compare

> **Operator Note:** New external-facing records (e.g. subdomains) → high risk → verify owners

---

## Running Script

Operators should automate periodic snapshots and compare results daily or weekly.

```powershell
# Example Scheduled Task
powershell.exe -File C:\Scripts\Monitor_AD_DNS.ps1
```

---

## Dependencies

* RSAT + DNS Management Tools (Windows)
* PowerShell DNS Module or WMI access
* Cloud CLI tools (AWS CLI, Azure CLI, GCP SDK)

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| DNS Console (Export List) | Windows | Native | Manual export |
| PowerShell DNS Cmdlets | Windows | Native | Automated export + compare |
| Kdiff / WinMerge | Cross-platform | Installable | Manual compare |
| AWS Route53 CLI | AWS | Native | DNS record export |
| Azure CLI DNS | Azure | Native | DNS record export |
| GCP gcloud DNS | GCP | Native | DNS record export |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate access to AD DNS → Confirm ability to export
- [ ] Collect and export current zone records
- [ ] Compare to baseline → Look for new additions
- [ ] Investigate → Check with system owner for unauthorized entries
- [ ] Review cloud DNS zones (AWS, Azure, GCP) → Detect new records
- [ ] Validate DNS record types (A, CNAME, TXT) → Suspicious types escalate
- [ ] Document findings and escalate unauthorized names

### Best Practices

- Automate export + diff → reduces operator burden
- Investigate unknown A records → lateral movement risk
- Review TXT records → attacker may use for C2/commands
- Integrate DNS detection with SIEM/SOAR for real-time alerts

---

## References

* [Dnscmd tool reference](https://technet.microsoft.com/en-us/library/cc772069.aspx#BKMK_25)
* [Scripting with PowerShell](https://technet.microsoft.com/en-us/library/bb978526.aspx)
* [PowerShell DNS](https://github.com/WiredPulse/PowerShell/tree/master/DNS)
* [AWS Route53 CLI](https://docs.aws.amazon.com/cli/latest/reference/route53/index.html)
* [Azure DNS PowerShell](https://learn.microsoft.com/en-us/powershell/module/az.dns)
* [GCP Cloud DNS](https://cloud.google.com/sdk/gcloud/reference/dns/record-sets/list)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Original retained + expanded to include automation, cloud DNS, operator procedures | Leo |
