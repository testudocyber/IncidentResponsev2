# 2.01 Review Organizational Processes and Conduct a Site Survey

## Purpose

Identify the organization's network architecture, key systems, and security controls through interviews and site surveys. This information is foundational for effective incident detection.

The IR Team should gather information from the mission owner (and Network owner) about its domain, organizational processes, method of patch configuration management, privilege rights, training, cross-domain transfers, use of removable devices etc., prior to the site survey. The CPT needs to receive, for example, a complete list of all workstations, servers, services, required applications, and trust requirements of the Domain(s) supported. Equally as important, the CPT needs to know the required services needed to run within the Domain and where these services can run. (Example: a Domain Controller can run several rolls and services such as DHCP, DNS, and Active Directory.) It is important to remember that there may be workstations that reside in workgroups not jointed to the domain. A site survey is recommended, even though the mission owner theoretically can provide all information remotely. The site survey helps to establish the working relationships with the mission owner and can also result in the identifcation of devices and connections not previously disclosed.

## Standards

- Interview mission owner and network owner.
- Understand domain architecture, trust relationships, patch management, and removable media policy.
- Conduct site survey to verify network layout and identify undocumented devices.

## Procedural Steps (Checklist)

### Prepare for Site Survey

- [ ] Obtain preliminary network documentation.
- [ ] Prepare interview questions focused on:
  - Domain structure and trust
  - Patch and configuration management
  - Privileged account management
  - Allowed devices and media

### Conduct Interviews

- [ ] Meet with mission owner and network owner.
- [ ] Capture responses in interview notes.
- [ ] Identify gaps or discrepancies in information.

### Conduct Site Survey

- [ ] Physically inspect network and server rooms.
- [ ] Document:
  - Connected devices
  - Wireless access points
  - Unauthorized systems

### Validate and Update Network Information

- [ ] Reconcile site survey findings with existing documentation.
- [ ] Update asset inventory.

## Tools and Resources

| Platform | Tool | Purpose |
|----------|------|---------|
| Windows/Linux/macOS | Nmap | Active host discovery |
| Windows | Advanced IP Scanner | Local network discovery |
| macOS/Linux | arp-scan | Network device discovery |
| Mobile | Fing App | Network discovery on mobile device |

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.0 | Operator-level expanded version | Leo |
