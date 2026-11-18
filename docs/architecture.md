# Homelab architecture

> Public, deliberately sanitized reference architecture. No real addresses, credentials, host-specific firewall rules, or authentication material are published here.

## Why this layout?

This lab combines **authorized security testing** with the everyday operational work of running services reliably on low-power, repurposed hardware.

The two always-on Linux nodes have different roles:

| Component | Role | State |
|---|---|---|
| RK322x / Armbian node | Lightweight service host: AdGuard Home, Uptime Kuma, Discord automations, beta application workloads | Running |
| Ubuntu Server node | Samba file-sharing lab target, SSH administration, DNS/DHCP for a dedicated lab-facing interface, on-demand controller | Running |
| Kali Linux workstation | Authorized reconnaissance, enumeration, packet inspection and security exercises | Used as needed |
| Windows/Linux clients | Access and testing endpoints; some boot Linux via persistent live media | Used as needed |
| Tailscale | Private overlay connectivity for remote management and monitoring | Running |
| Backblaze B2 | Off-site destination for selected encrypted service backups | Running |

Other machines and temporary lab projects are not represented as 24/7 services.

## Logical topology

~~~mermaid
flowchart TD
    K["Kali Linux / test workstation"]
    C["Windows, Linux and other lab clients"]
    U["Ubuntu Server<br/>Samba · SSH · lab DNS/DHCP"]
    R["Armbian RK322x<br/>AdGuard Home · Uptime Kuma<br/>Discord and beta automations"]
    T["Tailscale private overlay"]
    A["Discord alert destination"]
    B["Backblaze B2<br/>encrypted selected backups"]
    H["Healthchecks.io<br/>external RK322x heartbeat"]

    K -->|"Authorized enumeration and traffic experiments"| U
    C -->|"LAN and lab traffic"| U
    R --- T
    U --- T
    K --- T
    R -->|"Uptime Kuma alerts"| A
    R -->|"rclone crypt"| B
    R -->|"Outbound heartbeat"| H
~~~

This diagram is **logical**, not a full Layer 2/Layer 3 map. In particular, it deliberately omits exact addressing, network membership, ports, and external endpoints.

## Lab network concepts practiced

- **Multiple interfaces and subnets:** a server participates in more than one network; dnsmasq serves DNS and DHCP for its dedicated wired lab interface.
- **Private overlay networking:** Tailscale enables management and checks without presenting the lab topology as an Internet-facing inventory.
- **DNS control:** AdGuard Home is configured for selected devices using the private network; this does **not** imply that all household DNS traffic is filtered.
- **Switched Ethernet versus traffic visibility:** observing traffic from one endpoint does not automatically reveal unicast conversations between other devices.
- **Service reachability:** a host responding to ICMP does not prove that SSH, Samba or DNS is healthy; separate checks answer separate questions.

## Security boundaries and caveats

The lab is owned/authorized. Scanning and interception exercises are scoped to explicitly controlled endpoints.

Monitoring traffic and administrative access use private networking where appropriate, but a VPN alone is not a complete access-control strategy. Host firewall policy, identity, service binding, authentication, and patching still matter.

A monitoring system running on one node can lose visibility if that node itself fails. An **external heartbeat exists for the Armbian node**; the Ubuntu node is monitored by Uptime Kuma, without claiming an additional independent cloud check.

## What is intentionally excluded

- Real internal or overlay IPs, DNS zones and host-specific network maps
- SSH port numbers and exact remote-access commands
- Secrets, webhooks, API credentials and private keys
- Production backup archives, databases and personal/client data
- Detailed topology of unrelated third-party environments

## Next improvements

- Add carefully redacted architecture screenshots and test evidence
- Document an end-to-end restore exercise once one has been performed
- Keep current operating-state documentation in a separate private infrastructure repository

[Back to the project overview](../README.md)
