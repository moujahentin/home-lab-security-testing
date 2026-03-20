# Home Lab – Network & Security Testing

## Overview

This repository documents my personal home lab environment, used for hands-on experimentation in networking, system analysis, and offensive security concepts.

The lab is designed to simulate real-world scenarios in a controlled environment, allowing safe testing of network behavior, traffic inspection, and attack techniques.

---

## Lab Architecture

The lab consists of multiple interconnected systems across local and remote networks.

### Core Components

- Attacker Machine: Kali Linux
- Internal Server: Ubuntu Server (Samba services)
- Monitoring Node: Armbian-based device
- Client Devices: Windows, Linux, Android TV, IoT devices
- VPN Layer: Tailscale (remote access and routing)
- DNS Control: AdGuard (filtering and monitoring)

---

## Network Design

The environment includes:

- multiple subnets
- internal routing between interfaces
- mixed wired and wireless connections
- remote access via VPN

This allows testing of both local and remote attack scenarios.

---

## Use Cases

The lab is used for:

- network scanning and service enumeration
- traffic capture and packet inspection
- DNS behavior analysis
- MITM experiments in controlled environments
- service interaction testing (e.g. SMB/Samba)
- observing real device communication patterns

---

## Tools & Technologies

- Kali Linux
- Bettercap
- tcpdump / Wireshark
- Nmap (when available)
- Samba
- Tailscale
- AdGuard

---

## Example Activities

- Mapping network devices and services
- Monitoring mDNS, DNS and broadcast traffic
- Analyzing device discovery protocols
- Testing traffic interception techniques in isolated environments
- Investigating authentication behavior between services

---

## Security Perspective

This lab is focused on understanding:

- how real networks behave
- where weaknesses appear in typical home/office setups
- how traffic can be observed, manipulated or misused
- how segmentation and architecture affect security

---

## Notes

All experiments are conducted in a controlled environment on systems I own or have permission to test.

This repository does not include any offensive scripts or exploits, only documentation and high-level descriptions of lab usage.

---

## Goal

To continuously improve practical skills in:

- network analysis
- system interaction
- attack surface identification
- real-world security thinking
