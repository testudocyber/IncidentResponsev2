# Request Logs from Mission Owner Task

## Conditions

- IR team activated and network investigation required.
- Logs may be needed from internal and external sources.

## Standards

- Immediately request logs covering the previous 30 days (minimum).
- Focus requests on IAP, proxy, DNS, DHCP, and host/network sensor logs.

## Procedural Steps (Checklist)

### Identify Required Logs

- [ ] Confirm log sources with network owner:
  - Internet Access Provider (IAP)
  - Proxy firewall logs
  - DNS logs
  - DHCP logs
  - Host and network sensor logs (EDR, IDS, SIEM)

### Prepare and Send Log Request

- [ ] Draft log request memo or email:
  - Include log types, timeframe (30 days minimum), and format.
  - Use mission justification.
- [ ] Send request to network owner POC.
- [ ] Track request submission and ETA for receipt.

### Receive and Validate Logs

- [ ] Validate format and completeness upon receipt.
- [ ] Log receipt date/time and completeness in mission log.

#### Tools
- Secure file transfer: SFTP, encrypted email, external hard drive
- Log review: ElasticSearch, Kibana, Splunk, grep (Linux)

#### Example Log Validation Command (Linux):

```bash
gzip -l firewall_logs_202504.tar.gz
wc -l firewall_logs_202504/*.log
```

### Organize and Store Logs

- [ ] Store logs securely (IR evidence repository).
- [ ] Document chain of custody if needed.

### Analyze Logs

- [ ] Assign log analysis tasks to IR team members.
- [ ] Use tools/scripts to parse and identify indicators of compromise.

## End State

- Logs successfully requested, received, validated, and stored.
- Ready for analysis and use in incident investigation.

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Expanded procedural checklist and log validation examples | Leo |
