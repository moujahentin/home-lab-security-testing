# Hands-on security lab notes

These are **completed learning activities**, not claims of professional penetration-test engagements. Everything described here was carried out on owned/authorized lab systems or in purpose-built training environments. Examples are intentionally sanitized.

## 1. Network reconnaissance and SMB enumeration

**Environment:** Kali Linux against an Ubuntu Server with Samba inside the home lab.

**What I did**
- Discovered live systems and mapped exposed services with Nmap.
- Identified SSH and SMB on the lab server, including service/version information.
- Used enum4linux to inspect SMB information; an anonymous session and an accessible test share were observed in that configuration.
- Compared enumeration results with Samba-side logs instead of treating a scanner result as proof of a vulnerability.
- Investigated results from SMB-related Nmap scripts and distinguished Windows-specific checks from the Linux/Samba target.

**What I learned**
- An open port is evidence of a service, not evidence of successful exploitation.
- SMB enumeration can reveal configuration and permissions that merit review.
- Script output must be checked against the operating system and actual service implementation.
- Correlating server logs with probes is useful for validating what really reached the target.

**Concepts:** asset discovery, service enumeration, SMB exposure, anonymous access, evidence validation.

## 2. SSH hardening and Fail2ban validation

**Environment:** the Armbian node and authorized lab test clients.

**What I did**
- Configured hardened SSH access, including disabled root login and disabled password authentication on the Armbian node.
- Enabled a Fail2ban SSH jail backed by systemd journal events.
- Performed controlled failed-login tests from a lab client.
- Confirmed that repeated failures appeared in the logs and triggered a Fail2ban ban.

**What I learned**
- Enforcement must be **tested**, not inferred from a configuration file.
- Logs, authentication settings and ban decisions are separate verification points.
- Fail2ban is a defensive layer; it does not replace sound SSH authentication or network access controls.

**Concepts:** Linux hardening, SSH, systemd journal, detection engineering, defensive validation.

## 3. LAN discovery and controlled traffic experiments

**Environment:** Kali Linux, Bettercap and owned LAN endpoints.

**What I did**
- Ran local host discovery and inspected discovered devices with Bettercap.
- Performed an authorized, target-scoped ARP-spoofing experiment inside the lab.
- Investigated why some traffic was visible and other traffic was not, including the behavior of switched networks and Tailscale overlay traffic.
- Used network-capture and inspection concepts to reason about observable versus non-observable flows.

**What I learned**
- Switching, routing and VPN encapsulation determine where packets can be observed.
- Host discovery is not the same as full traffic capture.
- Target scoping is essential for ethical and controlled packet interception exercises.

**Concepts:** ARP, LAN switching, MITM testing, network visibility, packet analysis.

## 4. TryHackMe: Blue

**Environment:** a dedicated TryHackMe training room, **not** a real-world target.

**What I practiced**
- Reconnaissance of a deliberately vulnerable Windows machine.
- Exploitation of the MS17-010 vulnerability in the lab.
- Post-exploitation activities using a Meterpreter session.
- Privilege and process inspection, credential-hash access and completion of the room objectives.

**What I learned**
- Why exploitation depends on the target's actual operating system and patch state.
- How to separate initial access, privilege level and post-exploitation evidence.
- Why offensive skills must be exercised only within an explicitly authorized scope.

This was a training exercise, not evidence that the same technique works against arbitrary modern systems.

## 5. Suspicious website / phishing-flow investigation

**Environment:** browser developer tools and network-level observations of a suspicious web flow.

**What I examined**
- DNS resolution and HTTP redirection behavior.
- TLS handshake failures and responses from web infrastructure.
- Browser network requests to API endpoints during the flow.

**Important evidence limitation:** observing API requests or redirects does **not** by itself prove what data was submitted or that credential theft occurred. No unverified victim information or tokens are published here.

**Concepts:** browser DevTools, HTTP, redirects, TLS, DNS, evidence handling.

## Practical lessons across the exercises

1. **Define scope first.** Use owned devices, explicit permission, or purpose-built labs.
2. **Collect observations.** Nmap findings, server logs and packet captures have different levels of evidentiary value.
3. **Avoid overclaiming.** A detected service, a script match or a suspicious request does not automatically establish a vulnerability or compromise.
4. **Test defensive controls.** A working ban, alert or backup check is stronger evidence than a feature merely being installed.
5. **Document reproducibly.** Commands, expected results and redacted evidence make learning easier to review.

### Evidence and publication status

Detailed raw logs, real network identifiers, account names, tokens and packet captures are **not published**. Future screenshots will be reviewed for sensitive metadata before inclusion.

[See architecture](architecture.md) · [See monitoring](monitoring.md) · [Back to README](../README.md)
