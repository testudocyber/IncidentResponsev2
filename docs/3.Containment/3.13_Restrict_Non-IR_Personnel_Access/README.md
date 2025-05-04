# Restrict Non-IR Personnel Access

## Task Restrict Access to Critical Systems to Only Authorized Incident Response Personnel

## Conditions

Given domain credentials with the appropriate permissions and an incident in progress, the operator will limit access to sensitive systems, impacted hosts, and critical infrastructure to only members of the approved Incident Response (IR) team.

> **Operator Note:** Restricting access during containment prevents accidental or malicious tampering, reduces risk of malware spreading, and preserves forensic integrity.

## Standards

* Team member identifies and documents all impacted and critical systems.  
* Team member defines IR team user accounts or groups to be authorized for access.  
* Team member restricts logon access to critical systems through Group Policy, ACL modifications, or firewall rules.  
* Team member verifies that only IR team accounts can access the systems during containment.  
* Team member coordinates any exceptions with the enclave owner or system owner.

## End State

Non-IR personnel are prevented from logging into or accessing critical incident systems. Only designated IR team members have access until containment is complete.

---

## Notes

- Access restrictions should be temporary and lifted after containment and forensic tasks are completed.
- Coordinate with business and enclave owners to avoid disrupting critical business operations.
- Monitor access logs to ensure unauthorized attempts are detected and reviewed.

---

## Manual Steps

### Step 1: Identify Impacted Systems

- Use AD OU structures, asset inventory, or IR logs to document affected systems.
- Prioritize containment zones → domain controllers, critical servers, compromised endpoints.

---

### Step 2: Identify and Prepare IR Team Accounts

- Use existing IR team AD group (preferred) or create a temporary group:

```powershell
New-ADGroup -Name "IR-Access-Only" -GroupScope Global -Path "OU=IncidentResponse,DC=example,DC=com"
Add-ADGroupMember -Identity "IR-Access-Only" -Members "user1","user2","user3"
```

> **Operator Note:** If available, always use group membership vs. individual accounts for easy rollback.

---

### Step 3: Restrict Access Using Group Policy (Preferred)

#### Open Group Policy Management Console (GPMC):

```plaintext
gpmc.msc
```

#### Create or edit GPO:

```plaintext
Computer Configuration → Windows Settings → Security Settings → Local Policies → User Rights Assignment → Allow log on locally
```

- Remove “Domain Users” or “Administrators” if necessary
- Add **IR-Access-Only** group
- Link GPO to impacted hosts’ OU
- Force GPO update:

```cmd
gpupdate /force
```

---

### Step 4: Block Remote Access via Firewall (Optional Enhancement)

```powershell
New-NetFirewallRule -DisplayName "Block RDP" -Direction Inbound -Protocol TCP -LocalPort 3389 -Action Block
```

> **Operator Note:** Use this to prevent RDP access except from known IR jump hosts.

---

### Step 5: Validate Access Restrictions

- Attempt logon with non-IR user → should fail
- Attempt logon with IR team user → should succeed
- Review event logs:

```powershell
Get-EventLog -LogName Security -InstanceId 4625 -After (Get-Date).AddHours(-1)
```

> **Operator Note:** Event ID 4625 indicates failed logon attempts.

---

## Running Script (Automated Access Restriction)

```powershell
# Create IR team group and restrict logon locally on target system
$IRGroup = "IR-Access-Only"
New-ADGroup -Name $IRGroup -GroupScope Global -Path "OU=IncidentResponse,DC=example,DC=com"
Add-ADGroupMember -Identity $IRGroup -Members "user1","user2"

# Set local policy (manual via GPO recommended, but can script with LGPO or secedit)
# Force GPO update
gpupdate /force
```

---

## Dependencies

* RSAT with Group Policy Management Console and AD Module
* Domain Admin or delegated permissions
* Coordination with enclave/business owners
* Firewall administrative rights (if blocking remote access)

---

## Other Available Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| GPMC (gpmc.msc) | Windows | Preferred method for large scale access restriction |
| Local Group Policy Editor (gpedit.msc) | Windows | Non-domain systems |
| PowerShell (New-ADGroup, Add-ADGroupMember) | Windows | IR team group management |
| Windows Firewall (netsh, PowerShell) | Windows | Block remote access |
| VPN or NAC solutions | Cross-platform | Network level access restriction |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Identify all critical and impacted systems.
- [ ] Create or validate IR team AD group.
- [ ] Apply logon restriction policies (GPO preferred).
- [ ] Optionally block remote access at host firewall level.
- [ ] Validate policy effectiveness and log any denied access attempts.
- [ ] Coordinate any exceptions with enclave owner.
- [ ] Remove restrictions post-incident after validation.

### Best Practices

- Always use AD groups to ease management and rollback.
- Use layered approach: GPO + host firewall.
- Document temporary restrictions and review before releasing.
- Coordinate closely with system owners and stakeholders.

---

## References

[Microsoft Group Policy: User Rights Assignment](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment)  
[PowerShell AD Module Documentation](https://learn.microsoft.com/en-us/powershell/module/activedirectory)  
[Windows Firewall PowerShell Commands](https://learn.microsoft.com/en-us/powershell/module/netsecurity)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Fully generated operator guide for restricting non-IR access with GPO, PowerShell, firewall and checklist | Leo |
