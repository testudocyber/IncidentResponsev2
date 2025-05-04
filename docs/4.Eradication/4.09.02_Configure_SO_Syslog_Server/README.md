# 4.09.2 Configure Security Onion Syslog Server

## Task

Configure Security Onion to act as a Syslog server and ingest logs from external devices such as firewalls, switches, routers, and servers.

---

## Conditions

Given an operational Security Onion deployment and external network devices capable of sending syslog events.

---

## Standards

* Configure Security Onion syslog-ng to accept logs from the network.
* Create and manage firewall rules to permit syslog traffic (UDP/514 or TCP/514).
* Verify log ingestion and visibility in Kibana.
* Maintain logs according to retention and security best practices.

---

## End State

Security Onion receives syslog messages from external sources and logs are searchable in Kibana for analysis.

---

## Notes

- UDP/514 is standard but unreliable → consider TCP/514 if supported for critical logs.
- Source devices must be able to resolve Security Onion hostname or IP.
- Proper timezone and timestamp settings on source devices are critical for log correlation.

---

## Manual Steps

### Step 1: Validate syslog-ng is running

Security Onion uses **syslog-ng** as its syslog receiver by default.

Check syslog-ng service status:

```bash
sudo so-status
```

You should see:

```
[+] syslog-ng................. running
```

If not running:

```bash
sudo so-sensor-restart
```

---

### Step 2: Configure Firewall Access

By default, Security Onion blocks incoming syslog from external networks.

Allow specific networks or hosts to send syslog using `so-allow`:

```bash
sudo so-allow
```

> Select **Custom** when prompted, and allow UDP/514 or TCP/514 from desired source IPs/subnets.

Example:

```bash
Allowed CIDR → 192.168.1.0/24
Protocol → UDP
Port → 514
```

> **Operator Note:** Always restrict allowed networks — never leave open to entire Internet.

---

### Step 3: Configure Source Devices

On each external device (e.g. Palo Alto Firewall, Cisco Switch, Linux Server):

* Set Syslog Server IP → Security Onion management interface IP
* Set Port → 514
* Set Protocol → UDP or TCP (UDP is default but TCP is preferred for reliability)
* Set Facility and Severity → Usually default settings (or informational/notice)

Example (Linux server):

```bash
logger -n 192.168.1.10 -P 514 -d "Test syslog message to Security Onion"
```

---

### Step 4: Validate Log Receipt

Logs sent to syslog-ng are stored locally before forwarding to Elastic stack.

Check raw syslog data:

```bash
sudo tail -f /nsm/sensor_logs/syslog-ng/syslog-ng.log
```

> **Operator Note:** If logs do not appear → verify network connectivity, firewall rules, and source configuration.

---

### Step 5: Validate Log Ingestion in Kibana

* Open Security Onion Web Interface
* Navigate to Kibana
* Select relevant index (ex: `logstash-*` or `syslog-*`)
* Search for your test message or source IP.

Example search:

```
message:"Test syslog message to Security Onion"
```

You should see your log displayed.

> **Operator Note:** Timestamp and source IP should match sender → review parsing and timezone if discrepancies exist.

---

## Dependencies

* Security Onion operational and accessible
* syslog-ng running
* External devices configured to send syslog
* Firewall rules allowing syslog traffic to Security Onion

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| syslog-ng | Built-in (Security Onion) | Native | Syslog reception and processing |
| Kibana | Web UI | Native | Searching and analyzing logs |
| logger (Linux) | Built-in | Generate test syslog events |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm syslog-ng is running on Security Onion.
- [ ] Open firewall using `so-allow` for source IPs and port 514.
- [ ] Configure external devices to forward syslog to Security Onion.
- [ ] Validate receipt using syslog-ng.log.
- [ ] Confirm logs are searchable in Kibana.

### Best Practices

- Use TCP syslog when possible → improves reliability.
- Use meaningful hostname/identifiers on source devices → improves search and correlation.
- Document allowed networks in `so-allow` to ensure security posture.
- Monitor log volume → syslog can generate heavy traffic and consume disk space.

---

## References

- [Security Onion Syslog](https://docs.securityonion.net/en/latest/syslog.html)
- [Syslog-ng Documentation](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with detailed operator guidance and configuration validation steps | Leo |
