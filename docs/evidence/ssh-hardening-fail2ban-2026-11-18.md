# Evidence: SSH hardening and Fail2ban configuration — two Linux hosts

**Observed:** 2026-11-18  
**Environment:** two always-on, personally administered Linux homelab systems  
**Evidence type:** sanitized transcript of live configuration and service-status checks

This case study documents an observed configuration issue, its correction, and what was actually verified. It does **not** claim a completed end-to-end ban test.

## 1. Configuration comparison

| Check | Armbian monitoring node | Ubuntu Server node |
|---|---|---|
| Root SSH login | Disabled | Disabled |
| Public-key authentication | Enabled | Enabled |
| Password authentication | Disabled | Enabled intentionally for mobile terminal access |
| Fail2ban service | Active | Active following installation |
| SSH jail | Present | Present |
| Bans recorded at inspection | 0 | 0 |

**Why the settings differ:** the Ubuntu host retained password-based SSH authentication to preserve an established mobile-terminal workflow. The Armbian node permits key authentication without password login. Password login remains a separate risk that benefits from strong credentials and network restrictions.

## 2. Finding: wrong port in the Fail2ban firewall action

The Ubuntu SSH daemon listens on a **non-default port**. After Fail2ban installation, the effective nftables action initially used the default SSH service alias:

```text
# Relevant lines transcribed and sanitized from the initial effective configuration:
['set', 'sshd', 'addaction', 'nftables']
['port', 'ssh']
```

The action configuration was inconsistent with the SSH listener's custom port. The practical risk was that a future ban would affect the wrong destination port, leaving the actual SSH listener unaffected.

## 3. Remediation

A local jail override was created, setting `[sshd]` to the same **custom port** as the running SSH listener. The configuration check passed, and the jail was reloaded.

```text
# Sanitized observation from the configuration test and reload:
OK: configuration test is successful
OK
```

The actual port number and administrative access endpoints are omitted from this public repository.

## 4. Post-change verification

The checks below were run after the change:

```text
$ systemctl is-active fail2ban
active

$ sudo fail2ban-client get sshd action nftables port
[REDACTED — matches the configured non-default SSH listener port]

$ sudo fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 0
|  |- Total failed:     0
|  `- Journal matches: _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 0
   |- Total banned:     0
   `- Banned IP list:
```

**Verified:** Fail2ban was running, its SSH jail was registered, and the effective `nftables` action referenced the correct configured SSH port. The SSH password setting was not changed.

**Not verified:** an actual source-IP ban, a generated nftables reject rule during a ban, or the ability to connect from every normal client after such a ban. Those outcomes require a controlled test using a separate test client and an independent recovery path.

## 5. Network exposure and operational considerations

At the time of review, the Ubuntu SSH listener was bound to multiple interfaces and the host's main input policy was permissive. Tailscale was available, but its presence did not, on its own, prove that SSH access was restricted exclusively to the VPN. Internet reachability was **not assessed** in this test.

A safer future validation should verify both the relevant network access boundaries and the full chain:

1. Deliberate failed SSH authentication attempts from a disposable, authorized test source.
2. Matching authentication events in the selected journal.
3. Incrementing Fail2ban failure counters.
4. An actual IP ban and nftables enforcement on the non-default SSH listener.
5. Restoration/unban and confirmation that normal access still works.

**Safety note:** never perform the test from the only available administration session.

## What I learned

- Effective service configuration must be checked, not merely package installation state.
- A `Fail2ban active` result is not proof of traffic enforcement.
- An SSH port change can cause a mismatch between a jail and its firewall action.
- Security hardening should preserve legitimate access paths while reducing unnecessary exposure.
- Evidence should distinguish **configured**, **verified**, and **planned** controls.

[Security lab notes](../security-lab.md) · [Back to README](../../README.md)
