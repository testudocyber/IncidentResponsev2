# 4.58 Remove Unauthorized Services (Local/Cloud - Background Processes, SaaS Apps, Shadow IT)

## Task

Identify and remove unauthorized services and applications across local systems, cloud platforms (AWS, Azure, GCP), and SaaS environments to eliminate unapproved, risky, or malicious background processes and shadow IT.

---

## Conditions

Given access to local systems, cloud tenant management consoles, SaaS application reports, and monitoring tools.

---

## Standards

* Discover all running services, background processes, cloud-based apps, and SaaS subscriptions.
* Validate services and apps against approved lists.
* Remove, disable, or terminate unauthorized services and applications.
* Document actions and notify system or app owners.

---

## End State

All unauthorized services, apps, and cloud services are identified, removed or disabled, and documented.

---

## Notes

- Unauthorized services can be installed malware, unauthorized software, or shadow IT.
- Shadow IT (SaaS apps signed up by users without IT approval) can introduce major security risks.
- Cloud platforms often have running services or apps not approved → review is mandatory.

---

## Manual Steps

### Step 1: Enumerate Local Background Services and Processes

#### Windows

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object Name, DisplayName, Status
```

Look for:

- Unfamiliar service names
- Services running from temp folders
- Services without a digital signature

#### Linux

```bash
systemctl list-units --type=service --state=running
ps aux
```

Look for:

- Services running as root
- Unrecognized process names

#### macOS

```bash
launchctl list
ps aux
```

> **Operator Note:** Cross-reference running services with baseline and approved software lists.

---

### Step 2: Enumerate Installed Applications (Local)

#### Windows

```powershell
Get-WmiObject -Class Win32_Product | Select-Object Name
```

#### Linux

```bash
dpkg -l
```

#### macOS

```bash
system_profiler SPApplicationsDataType
```

Look for:

- Applications not in baseline inventory
- Unapproved or unauthorized tools

> **Operator Note:** Alert on remote access tools (AnyDesk, TeamViewer, etc.) if not approved.

---

### Step 3: Enumerate SaaS Applications (Shadow IT)

#### Microsoft 365 / Azure AD

- Azure AD → Enterprise Applications → All Applications
- Filter by “Added by” → detect user-added apps

```powershell
Get-AzureADServicePrincipal -All $true | Select DisplayName, AppId
```

Look for:

- Apps added by non-admin users
- Apps with risky permissions (mail, files, calendar)

#### Google Workspace

- Admin Console → Apps → Web and mobile apps → Connected apps

Look for:

- Apps connected to user accounts without approval

#### Shadow IT Discovery (Optional Tools)

| Tool | Platform | Usage |
|------|----------|-------|
| Microsoft Cloud App Security (Defender for Cloud Apps) | Azure | SaaS discovery |
| Netskope | Cloud | SaaS usage monitoring |
| Zscaler | Cloud | Shadow IT discovery |

> **Operator Note:** SaaS apps added without IT knowledge pose major risk → escalate immediately.

---

### Step 4: Enumerate Cloud Services (AWS / Azure / GCP)

#### AWS → List active services/resources

```bash
aws resourcegroupstaggingapi get-resources
aws ec2 describe-instances
aws lambda list-functions
```

Look for:

- Resources without tags / owners
- Old or unauthorized services

#### Azure

```powershell
Get-AzResource
```

Azure Portal → Resource Groups → Filter by owner / tags.

Look for:

- Unused/unknown resources
- Services with unknown owner or usage

#### GCP

```bash
gcloud projects list
gcloud services list --enabled
gcloud compute instances list
```

Look for:

- Enabled services not part of baseline
- Unused Compute Engine instances

> **Operator Note:** Services with owner=unknown or missing tags should be treated as suspect.

---

### Step 5: Remove or Disable Unauthorized Services

#### Local Services

##### Windows

```powershell
Stop-Service -Name "ServiceName"
Set-Service -Name "ServiceName" -StartupType Disabled
```

##### Linux

```bash
sudo systemctl stop servicename
sudo systemctl disable servicename
```

##### macOS

```bash
sudo launchctl bootout system /Library/LaunchDaemons/com.unauthorized.service.plist
```

---

#### SaaS (Azure AD / Google Workspace)

##### Azure AD

```powershell
Remove-AzureADServicePrincipal -ObjectId <AppId>
```

##### Google Workspace

Admin Console → Apps → Connected Apps → Remove access

---

#### Cloud Resources (AWS, Azure, GCP)

##### AWS

```bash
aws ec2 terminate-instances --instance-ids <instance-id>
aws lambda delete-function --function-name <function-name>
```

##### Azure

```powershell
Remove-AzResource -ResourceId "<resource-id>"
```

##### GCP

```bash
gcloud compute instances delete <instance-name> --zone=<zone>
```

> **Operator Note:** Follow change management process when deleting cloud resources → review with owners first.

---

### Step 6: Validate and Document

- Confirm services removed and no longer running.
- Review audit logs for removal actions.
- Update documentation and notify owners / stakeholders.

---

## Dependencies

* Access to local systems, SaaS platforms, cloud platforms (AWS, Azure, GCP)
* Authorized application and services inventory
* Coordination with business and IT owners

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| PowerShell | Windows | Built-in | Local service/application enumeration |
| systemctl / launchctl | Linux/macOS | Native | Local service management |
| Cloud CLI Tools (aws, az, gcloud) | Cross-platform | Package manager | Cloud service management |
| SaaS App Management Consoles | Web | Native | Azure AD / Google Workspace app control |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Enumerate local running services and background processes.
- [ ] Enumerate SaaS connected apps → identify user-added apps.
- [ ] Enumerate cloud services across AWS, Azure, GCP.
- [ ] Validate all services and apps against approved list.
- [ ] Disable or remove unauthorized services.
- [ ] Validate removal and document actions taken.

### Best Practices

- Quarantine suspicious services → do not delete immediately unless required.
- Coordinate SaaS app removal with end users → avoid business disruption.
- Cloud services → validate no dependency before termination.
- Implement periodic service reviews → especially in cloud and SaaS environments.

---

## References

- [Azure AD Enterprise Apps Management](https://learn.microsoft.com/en-us/azure/active-directory/manage-apps/manage-enterprise-apps)
- [AWS Resource Group Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html)
- [GCP Service Management](https://cloud.google.com/service-usage/docs/enabling)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with deep operator-focused guidance across local, cloud, and SaaS platforms | Leo |
