# 4.60 Remove Unauthorized DNS Entries (Local, AD, Cloud DNS)

## Task

Identify and remove unauthorized or malicious DNS records across local hosts, enterprise DNS (Active Directory Integrated DNS), and Cloud DNS services (AWS Route 53, Azure DNS, Google Cloud DNS).

---

## Conditions

Given access to local systems, enterprise DNS servers, and cloud platform DNS management consoles.

---

## Standards

* Enumerate and validate all DNS entries across environments.
* Identify stale, rogue, or unauthorized DNS records.
* Remove or disable unauthorized DNS entries.
* Document and audit all actions taken.

---

## End State

All unauthorized or malicious DNS records are identified, removed, and documented across local, enterprise, and cloud DNS environments.

---

## Notes

- Rogue DNS records can enable phishing, subdomain takeovers, lateral movement, and persistence.
- Attackers often leave dynamic or malicious DNS records post-compromise.
- DNS record validation should be cross-checked against asset inventories.

---

## Manual Steps

### Step 1: Enumerate DNS Entries - Local Hosts Files

#### Windows

```powershell
Get-Content C:\Windows\System32\drivers\etc\hosts
```

#### Linux / macOS

```bash
cat /etc/hosts
```

Look for:

- Unusual host-to-IP mappings
- Redirects to suspicious IP addresses
- Unauthorized overrides (ex: local override of security domains)

> **Operator Note:** Hosts file tampering is a known attacker tactic → flag suspicious entries.

---

### Step 2: Enumerate Enterprise DNS Entries (AD Integrated DNS)

#### Active Directory DNS (Windows Server)

```powershell
Get-DnsServerResourceRecord -ZoneName "corp.local"
```

Look for:

- A, CNAME, TXT, and SRV records without associated authorized hosts
- Stale records with no recent dynamic updates
- Subdomains or forwarders pointing to external or suspicious IPs

> **Operator Note:** Cross-reference with DHCP leases and AD computer accounts.

---

### Step 3: Enumerate Cloud DNS Entries

#### AWS → Route 53

```bash
aws route53 list-hosted-zones
aws route53 list-resource-record-sets --hosted-zone-id ZONEID
```

Look for:

- Records with external or unknown targets
- Wildcard or catch-all records
- Old/deprecated subdomains still pointing to active resources

#### Azure → Azure DNS

```powershell
Get-AzDnsZone
Get-AzDnsRecordSet -ZoneName "example.com" -ResourceGroupName "DNS-RG"
```

Look for:

- Record sets pointing to deprecated or unknown resources
- Records without metadata or tags
- Subdomains with owner = unknown

#### GCP → Google Cloud DNS

```bash
gcloud dns managed-zones list
gcloud dns record-sets list --zone="zone-name"
```

Look for:

- External IP or external domain CNAMEs
- Old TXT records (may include attacker verification)
- Wildcard entries or catch-alls

> **Operator Note:** Orphaned cloud DNS records may point to deleted services → subdomain takeover risk.

---

### Step 4: Validate DNS Record Authorization

- Cross-check against asset inventory and change records.
- Confirm with application, network, or cloud owners.
- Validate IP addresses → internal/private should not point externally unless authorized.

> **Operator Note:** Validate owner and use-case → unknown or unsupported records should be removed.

---

### Step 5: Remove Unauthorized DNS Records

#### Hosts File

```powershell
# Windows
Remove-Item -Path "C:\Windows\System32\drivers\etc\hosts" -Force
# Or manually remove the entry

# Linux / macOS
sudo nano /etc/hosts
# Remove the unauthorized line
```

#### Active Directory DNS

```powershell
Remove-DnsServerResourceRecord -ZoneName "corp.local" -RRType "A" -Name "badhost" -Force
```

#### AWS Route 53

```bash
aws route53 change-resource-record-sets --hosted-zone-id ZONEID --change-batch file://change-batch.json
```

*Example change-batch.json for deletion*

```json
{
  "Comment": "Delete unauthorized record",
  "Changes": [
    {
      "Action": "DELETE",
      "ResourceRecordSet": {
        "Name": "badhost.example.com.",
        "Type": "A",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "192.0.2.44"
          }
        ]
      }
    }
  ]
}
```

#### Azure DNS

```powershell
Remove-AzDnsRecordSet -Name "badhost" -ZoneName "example.com" -ResourceGroupName "DNS-RG" -RecordType A
```

#### Google Cloud DNS

```bash
gcloud dns record-sets delete "badhost.example.com." --zone="zone-name" --type="A"
```

> **Operator Note:** When deleting cloud records, validate no active dependencies → removing legitimate records may impact production services.

---

### Step 6: Validate Removal and Document

- Re-query DNS → confirm record is removed.
- Validate DNS propagation → ensure no cached/stale records.
- Document removal → include ticket/case numbers and approvers.

---

## Dependencies

* Access to local hosts, AD DNS servers, and cloud platform DNS management consoles.
* Approved and current DNS inventory (zones, subdomains, owners).
* Coordination with application and cloud service owners.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| nslookup / dig | Cross-platform | Native | DNS query and validation |
| Windows DNS Manager | Windows | Built-in | AD DNS zone management |
| AWS CLI | Cross-platform | Package manager | Route 53 management |
| Azure CLI / PowerShell | Cross-platform | Package manager | Azure DNS management |
| GCloud CLI | Cross-platform | Package manager | Google Cloud DNS management |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate local hosts file and validate entries.
- [ ] Enumerate AD DNS records → identify stale or unauthorized records.
- [ ] Enumerate AWS Route 53, Azure DNS, and GCP Cloud DNS records.
- [ ] Validate against inventory and owners.
- [ ] Remove unauthorized records following validation.
- [ ] Validate removal and document actions.

### Best Practices

- Disable DNS dynamic updates where not needed.
- Require tagging/metadata for all cloud DNS records.
- Regularly audit DNS zones → stale records are common.
- Use DNS logging (query logs, DNS analytics) to monitor abuse.

---

## References

- [Windows DNS Server Cmdlets](https://learn.microsoft.com/en-us/powershell/module/dnsserver/?view=windowsserver2022-ps)
- [AWS Route 53 CLI Reference](https://docs.aws.amazon.com/cli/latest/reference/route53/)
- [Azure DNS Management](https://learn.microsoft.com/en-us/azure/dns/dns-overview)
- [Google Cloud DNS Management](https://cloud.google.com/dns/docs)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with operator-focused DNS record validation and removal process (Local, AD, Cloud) | Leo |
