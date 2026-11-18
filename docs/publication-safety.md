# Public documentation and evidence policy

This repository is a **public technical portfolio**, not the operational source of truth for private homelab configuration.

## Safe to publish

- Logical topology diagrams with invented labels
- High-level descriptions of tools, controls and observed outcomes
- Commands shown with example or RFC 5737 documentation IP ranges
- Screenshots with identifiers, usernames, domains and access tokens redacted
- Sanitized configuration fragments that cannot authenticate or expose private endpoints
- Lessons learned, evidence limitations and future work

## Keep private

- Real LAN / Tailscale addresses and inventories linking users to machines
- SSH access details, non-public service endpoints and sensitive firewall rules
- API keys, webhook URLs, cookies, session data, access tokens, passwords
- Private keys, recovery material, backup archives and live application databases
- Personal data, client information and details of systems outside the lab
- Full, unreviewed log exports or packet captures

## Screenshot review checklist

Before adding a screenshot:

1. Crop to the narrowest useful area.
2. Redact browser address bars, terminal prompts, hostnames, usernames and IP addresses when they could reveal the live environment.
3. Check notification payloads and console output for tokens, webhook URLs, file paths, identifiers and account details.
4. Remove unrelated channels, personal chats, device names and private network topology.
5. Re-review the actual committed image after export; cropping a screenshot is not the same as securely redacting it.

## Repository practices

- Keep the authoritative live infrastructure inventory in a **separate private repository**.
- Use a password manager or a suitable secret-management system for credentials; a private Git repo is not a secret vault.
- Ignore local secrets and database files, but do not rely on .gitignore as the only safety mechanism.
- Review staged changes before pushing; use secret scanning where available.
- If a secret is committed accidentally, revoke/rotate it. Deleting a later commit is not sufficient by itself.

## Evidence standard

I distinguish three things throughout this portfolio:

- **Implemented** — a change was applied and observed running.
- **Verified** — a specific behavior was tested or checked, with a clear scope.
- **Planned** — an idea or next step, not represented as delivered work.

This avoids presenting learning exercises, diagrams, or untested designs as production guarantees.

[Back to README](../README.md)
