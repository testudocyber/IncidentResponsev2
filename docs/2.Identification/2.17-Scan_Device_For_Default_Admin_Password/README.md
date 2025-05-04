# Scan Device for Default Admin Password

## Task Scan Device For Default Admin Passwords

## Conditions

Given a suspected compromised network segment(s), access to a system that can access and scan the identified network devices, and scanning software included in the team’s incident response kit.

> **Operator Note:** Default or weak admin credentials are a common foothold used by attackers. Identify and report them immediately.

## Standards

* Team member scans network for devices with default passwords.  
* Team member identifies devices and notifies network owner of findings.

> **Operator Note:** Always coordinate with network owner prior to scanning production systems.

## End State

All devices on network with weak or default passwords will be identified.

> **Operator Note:** Results should be documented, validated with system owners, and used to plan remediation actions.

## Manual Steps

### Using Nessus (Web UI)

1. Log into Nessus via IP and port `8834`.

Example:

```
https://x.x.x.x:8834
```

2. Verify correct plugins are enabled:

- Navigate to **Scans > New Scan > Advanced Scan**.
- Click the **Plugins** tab.
- Use the **Filter** toolbar at the top.
- Change dropdown to "Default/Known accounts" and click **Apply**.
- Review filtered plugins and ensure they are enabled.

> **Operator Note:** Some plugins may be disabled by default for safety. Review plugin descriptions carefully.

3. Create a new Advanced Scan:

- Title: `Weak & Known Password Scan`
- Targets: Enter IP range (example `x.x.x.x/24`)
- Leave other settings default.
- Save the scan.
- Launch the scan.

4. Monitor and review results once completed.

> **Operator Note:** Findings may include embedded device logins, network appliances, and misconfigured admin interfaces.

## Running Script

* Not applicable for this task.
* GUI + web-based scan via Nessus is the method used.

## Dependencies

* Nessus server with valid license or community edition.
* Network reachability to target subnet.

### Nessus installation quick guide:

#### Ubuntu/Debian:

```bash
dpkg -i Nessus-latest-debian6_amd64.deb
sudo /bin/systemctl start nessusd.service
```

#### Access GUI:

```
https://localhost:8834
```

> **Operator Note:** Install Nessus on isolated assessment VM or approved incident response host.

## Other available tools

* Nmap NSE Scripts (optional):

```bash
nmap --script=default-credentials -p 22,23,80,443 x.x.x.x/24
```

* Hydra (optional advanced tool for password brute-force, authorized use only)

```bash
hydra -L users.txt -P passwords.txt 192.168.1.1 ssh
```

> **Operator Note:** Nmap can identify default credentials passively, Hydra is active/password guessing and should only be used with explicit authorization.

## References

[Nessus Plugins](https://community.tenable.com/s/article/What-are-the-plugins-that-test-for-default-accounts)  
[Nessus Default Credentials](https://www.tenable.com/blog/scanning-for-default-common-credentials-using-nessus)  
[Nmap NSE Default Credentials](https://nmap.org/nsedoc/scripts/default-credentials.html)

---

## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Confirm authorization to scan for weak/default credentials.
- [ ] Configure Nessus with proper plugin selection.
- [ ] Run scan and export findings.
- [ ] Review results carefully with asset/system owners.
- [ ] Propose remediation (disable default accounts, enforce strong passwords).

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | Nessus | Primary tool for known/default password scans |
| Linux/Windows | Nmap NSE scripts | Optional passive scan for embedded device defaults |
| Linux/Windows | Hydra | Advanced password brute-forcing (authorized use only) |

### Alternate Commands and Examples

#### Nmap Default Credentials (quick test):

```bash
nmap --script=default-credentials -p 22,23,80,443 192.168.1.0/24
```

#### Hydra (authorized use only):

```bash
hydra -L users.txt -P passwords.txt 192.168.1.50 ssh
```

### Best Practices

- Always notify and coordinate with system owners prior to scanning.
- Prioritize critical infrastructure (routers, switches, remote management interfaces).
- Document all findings and mitigation plans.
- Avoid brute-force techniques unless part of authorized red team engagement.

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched Nessus + alternative tooling + operator recommendations | Leo |
