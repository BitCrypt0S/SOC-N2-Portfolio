# Lab Environment

## Overview

This project documents the setup of a virtualized cybersecurity laboratory designed for security analysis and investigation activities.
The laboratory provides a controlled environment for analyzing security artifacts, network traffic, logs, and potentially malicious files.

## Virtualization Platform

- VirtualBox

## Virtual Machines

- REMnux
- Windows
- Kali Linux

## Security Considerations

The laboratory is designed with isolation and controlled execution in mind. Virtual machines are used to separate analysis activities from the host environment.
Snapshots are used to preserve clean states before performing potentially risky analysis.

## Objective

The objective of this laboratory is to provide a controlled and reproducible environment for practical cybersecurity investigations and security analysis.

---

## Progress

### Phase 1 — Virtual Machines Setup ✅
- [x] VirtualBox installed and configured
- [x] REMnux, Kali Linux, and Windows VMs created
- [x] VMs updated

### Phase 2 — Network Architecture ✅

Each virtual machine is configured with two network interfaces:

- **Adapter 1 (NAT):** Provides internet access for updates and tool installation.
- **Adapter 2 (Internal Network — "SOC-LAB"):** Connects all VMs to an isolated internal network named `SOC-LAB`, allowing them to communicate with each other without exposing traffic to the host network or the internet.

This dual-interface setup allows the lab to simulate realistic network interactions (e.g., traffic between an attacker machine and a target machine) while keeping the environment fully isolated from the host and external networks.

- [x] Second network adapter configured on all VMs
- [x] Internal network created (`SOC-LAB`)
- [x] Dual-interface setup: NAT (internet access) + Internal Network (isolated inter-VM communication)

| VM | Role | Adapter 1 (NAT) | Adapter 2 (Internal Network: SOC-LAB) |
|---|---|---|---|
| Kali Linux | Attack/analysis machine | ✅ | ✅ |
| Windows | Target/victim machine | ✅ | ✅ |
| REMnux | Malware analysis machine | ✅ | ✅ |

### Phase 3 — Connectivity Validation ✅

With static IP addresses assigned on the `SOC-LAB` internal network, connectivity between all virtual machines was tested and confirmed.

**Note:** Since VirtualBox Internal Networks do not provide DHCP, static IP addresses had to be manually assigned on each VM.

**Assigned IP addresses:**

| VM | Interface | IP Address |
|---|---|---|
| Kali Linux | eth1 | 192.168.100.10 |
| Windows | Ethernet 2 | 192.168.100.11 |
| REMnux | enp0s8 | 192.168.100.12 |

**Troubleshooting note 1 — Windows Firewall:** Initial ping tests from Kali/REMnux to Windows failed, while Windows could successfully ping the Linux machines. This was caused by the Windows Defender Firewall blocking inbound ICMP Echo Requests by default. The issue was resolved by enabling the *"File and Printer Sharing (Echo Request - ICMPv4-In)"* inbound rule in Windows Firewall with Advanced Security.

**Troubleshooting note 2 — Persisting static IPs:** Initial IP configuration was applied temporarily via `ip addr add`, which does not survive a reboot. To make the configuration persistent, each system required a different approach depending on its network management stack:

| VM | Network manager | Method used |
|---|---|---|
| Kali Linux | NetworkManager | `nmcli connection modify` |
| REMnux | Netplan | YAML configuration in `/etc/netplan/` |
| Windows | Native TCP/IPv4 settings | Static IP set via GUI (persistent by default) |

Even though Kali and REMnux are both Debian-based distributions, they use different network management stacks — a detail confirmed through direct troubleshooting rather than assumption.

**Validation results:**
- [x] Kali Linux ↔ Windows — successful
- [x] REMnux ↔ Windows — successful
- [x] Kali Linux ↔ REMnux — successful
- [x] Static IP configuration persists after reboot (all VMs)

![Kali - ip addr](./evidence/kali-ip-addr.png)
![REMnux - ip addr](./evidence/remnux-ip-addr.png)
![Windows - ipconfig](./evidence/windows-ipconfig.png)
![Kali to Windows ping](./evidence/kali-ping-windows.png)
![Windows to REMnux ping](./evidence/windows-ping-remnux.png)
![REMnux to Kali ping](./evidence/remnux-ping-kali.png)
