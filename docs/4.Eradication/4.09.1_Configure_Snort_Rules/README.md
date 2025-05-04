# 4.09.1 Configure Snort Rules

## Task

Configure Snort IDS Rules on Security Onion to detect and alert on network-based malicious activity.

---

## Conditions

Given access to a deployed Security Onion instance running Snort as its detection engine.

---

## Standards

* The operator must be able to locate and manage Snort rule sets.
* The operator should understand how to add, modify, and enable Snort rules.
* The operator will verify that new or modified rules are active and generating alerts on relevant traffic.

---

## End State

Snort rules are correctly configured, loaded, and active. Security Onion is generating alerts based on rule matches.

---

## Notes

- Security Onion automatically manages Snort rule updates and configurations. However, custom rules can and should be added when necessary.
- Rules can be used to detect specific Indicators of Compromise (IOCs) or network behaviors.
- Testing and tuning are critical — poorly written rules can result in false positives or missed detections.

---

## Manual Steps

### Step 1: Access Security Onion

- Log into Security Onion terminal with administrative privileges.

```bash
ssh analyst@securityonion.local
```

### Step 2: Locate Local Rules File

- Navigate to the local rules directory.

```bash
cd /opt/so/rules/nids/
ls -al
```

- Look for `local.rules`. This is where custom rules should be placed.

> **Operator Note:** Always document changes to local.rules. Use comments (`#`) to record date and purpose.

---

### Step 3: Add Custom Rule

- Open `local.rules` in nano or vim.

```bash
sudo nano local.rules
```

- Example custom rule to detect outbound HTTP traffic to suspicious domain:

```snort
alert tcp any any -> any 80 (msg:"Suspicious HTTP domain access detected"; content:"badactor.com"; http_host; sid:1000001; rev:1;)
```

> **Operator Note:** `sid` (Snort ID) for local rules should start at 1000000+ to avoid overlap with community rules.

- Save the file after adding the rule.

---

### Step 4: Validate Snort Configuration

- Check for syntax errors before reloading Snort.

```bash
sudo so-rulecheck
```

- No errors should be reported. If errors are found, edit `local.rules` and fix them.

---

### Step 5: Reload Snort Rules

- To load new rules without rebooting:

```bash
sudo so-rule-update
```

- This will process the `local.rules` file and apply the changes.

> **Operator Note:** Monitor `/opt/so/logs/snort` to verify Snort reload occurred successfully.

---

### Step 6: Test the New Rule

- Generate traffic matching your rule (e.g., access `badactor.com` from internal network).
- Open Security Onion’s Kibana interface and search for alerts.

```text
msg:"Suspicious HTTP domain access detected"
```

- Verify your rule triggered.

---

### Step 7: Tune and Refine Rule as Needed

- Adjust `content`, `pcre`, and other Snort options to reduce false positives.
- Use additional rule options such as `flow`, `depth`, `offset` for precision.

---

## Dependencies

* Security Onion deployed and configured with Snort as IDS.
* Analyst or administrator level access.
* List of relevant IOCs or detection criteria.

---

## Other Available Tools

| Tool | Platform | Installation | Usage |
|------|----------|--------------|-------|
| Snort | Security Onion / Linux | Built-in | IDS engine |
| PulledPork (optional) | Linux | Manual install | Automatically update community rules |
| Suricata (if deployed) | Security Onion / Linux | Built-in alt. to Snort | May use similar rule syntax |

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Validate Snort is installed and running.
- [ ] Edit `local.rules` to add custom rules.
- [ ] Validate rules using `so-rulecheck`.
- [ ] Reload rules with `so-rule-update`.
- [ ] Test rules and verify alerts in Kibana.
- [ ] Tune rule for accuracy.

### Best Practices

- Use meaningful `msg` fields for easier triage.
- Start SID numbers for local rules at `1000000+`.
- Avoid overly broad content matches → they will create alert fatigue.
- Regularly review and prune local.rules → stale rules increase resource usage.

---

## References

- [Security Onion Snort Documentation](https://docs.securityonion.net/en/latest/nids.html)
- [Snort Rule Writing Guide](https://snort.org/documents)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Created from scratch with detailed operator-level guidance and examples | Leo |
