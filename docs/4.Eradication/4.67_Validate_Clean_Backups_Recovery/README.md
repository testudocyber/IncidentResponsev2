# 4.67 Validate Clean Backups Exist and Recovery Procedures Are Ready (Local, Cloud, SaaS)

## Task

Ensure clean, malware-free backups exist across local, cloud, and SaaS environments, and that recovery procedures are documented, validated, and executable.

---

## Conditions

Given access to backup management consoles, storage platforms, cloud-native backup services, and SaaS backup solutions.

---

## Standards

* Verify existence and completeness of backups.
* Confirm backup integrity and malware-free status.
* Confirm retention meets organizational policies.
* Validate and document recovery procedures.
* Test recovery process (where feasible) to confirm readiness.

---

## End State

Confirmed existence of clean and usable backups with documented and validated recovery procedures across local, cloud, and SaaS environments.

---

## Notes

- Backups made during infection periods may be contaminated → malware scans required.
- Backups should be immutable or air-gapped where possible.
- SaaS often requires separate backup tools → validate retention and export capabilities.

---

## Manual Steps

### Step 1: Validate Local and Enterprise Backups

#### Common Platforms → Veeam, Commvault, Rubrik, Veritas

- Access backup management console
- Validate:
  - Last successful backup dates
  - Backup jobs completed without errors
  - Backup destinations → no errors/missing targets

> **Operator Note:** Stale or failed backup jobs must be re-run.

---

#### Validate Malware-Free Status

- Select sample backups (recent + critical systems).
- Restore to isolated/test environment.
- Run full AV/EDR scan on restored files.

```bash
clamscan -r /restore/location
```

> **Operator Note:** Backups from infection window → higher priority for scanning.

---

#### Validate Retention and Offsite Copies

- Confirm retention settings meet policy.
- Validate offsite/immutable copies exist.

```bash
# Example Veeam immutable backup chain check (GUI or CLI)
Get-VBRBackup | Select-Object Name, IsImmutable
```

> **Operator Note:** Immutable or offline backups should be prioritized during recovery.

---

### Step 2: Validate Cloud Backups

#### AWS → Backup and Snapshots

```bash
aws backup list-backup-vaults
aws backup list-recovery-points-by-backup-vault --backup-vault-name VaultName
```

Look for:

- Recent successful recovery points
- No expired or missing critical workloads

#### Validate Malware-Free Backups (Optional Scan → Restore and AV Scan)

- Restore EC2 snapshot into isolated VPC
- Run AV/EDR scan on restored instance

```bash
aws ec2 create-volume --snapshot-id snap-xxxx --availability-zone us-east-1a
```

> **Operator Note:** Enable AWS Backup Vault Lock → immutable backups.

---

#### Azure → Azure Backup

```powershell
Get-AzRecoveryServicesVault
Get-AzRecoveryServicesBackupItem
```

Look for:

- Latest recovery points
- No errors in backup jobs

Validate by:

- Performing test restore → isolated VM or disk
- Running AV scan after recovery

---

#### GCP → Cloud Storage / Snapshots / Backup for GKE

```bash
gcloud compute snapshots list
gcloud storage ls
```

Look for:

- Latest snapshots available
- No errors or gaps

Restore + Validate:

- Create disk from snapshot → attach to test VM
- Run AV scan on recovered data

> **Operator Note:** Validate bucket object versioning and retention policies for ransomware protection.

---

### Step 3: Validate SaaS Application Backups

#### M365, Google Workspace, Salesforce → SaaS Backup Tools (Veeam, Druva, Backupify)

- Access SaaS backup portal
- Confirm:
  - Backup success → latest backup date/time
  - No job failures
  - Retention policies active

Validate recovery capability:

- Perform test restore of emails/files/documents to sandbox or alternate account.

> **Operator Note:** Confirm SaaS admins are familiar with recovery procedures.

---

### Step 4: Validate Recovery Procedures and Playbooks

- Review and confirm documented recovery playbooks exist.
- Validate:
  - Personnel aware and trained
  - Playbook up to date → reflecting current environment and backup platform
  - Recovery priorities aligned with Business Continuity Plan (BCP)

Perform tabletop or live recovery test:

- Select sample workload (non-critical preferred).
- Execute recovery plan end-to-end.
- Document results.

> **Operator Note:** Test for speed, completeness, and gaps during recovery exercise.

---

### Step 5: Validate and Document

- Ensure all backups (local, cloud, SaaS) validated.
- Confirm test recoveries performed and passed.
- Document validation evidence → backup job logs, recovery success, AV scans.
- Update Incident Record.

---

## Dependencies

* Access to backup software and cloud backup management portals
* Malware scanning tools (AV/EDR/YARA)
* Cloud resource access for restore testing
* SaaS application administrative access

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| EDR / AV (CrowdStrike, Defender) | Cross-platform | Native | Malware validation |
| AWS CLI + Backup + Vault Lock | AWS | Package manager | Cloud backup validation |
| Azure CLI + Recovery Services Vault | Azure | Package manager | Cloud backup validation |
| GCloud CLI + Snapshots + Storage | GCP | Package manager | Cloud backup validation |
| SaaS Backup Consoles (Veeam, Druva) | SaaS | Native | Backup validation + recovery tests |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate local + enterprise backup job success and retention.
- [ ] Malware scan test restores to validate cleanliness.
- [ ] Validate cloud backups → AWS, Azure, GCP success + malware-free.
- [ ] Validate SaaS backups → job success + sandbox restores.
- [ ] Validate recovery documentation + personnel readiness.
- [ ] Document validation and gaps.

### Best Practices

- Periodically validate malware-free backups.
- Use immutable/offsite copies as primary recovery source.
- Test recovery playbooks at least annually.
- Integrate backup validation status into incident postmortems.

---

## References

- [AWS Backup Vault Lock](https://docs.aws.amazon.com/aws-backup/latest/devguide/vault-lock.html)
- [Azure Backup Documentation](https://learn.microsoft.com/en-us/azure/backup/)
- [GCP Snapshots](https://cloud.google.com/compute/docs/disks/snapshots)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with operator-focused backup and recovery validation workflow | Leo |
