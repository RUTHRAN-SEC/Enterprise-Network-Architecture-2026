# VLAN Table — Enterprise Network Architecture 2026

## Overview

This document defines the complete VLAN architecture for the Enterprise Network 2026 project. VLANs are the primary segmentation mechanism that separates departments, isolates sensitive systems, and enforces Zero Trust boundaries across the network.

All VLANs are defined on CORE1, CORE2, and each relevant access switch. Inter-VLAN routing is performed by CORE1 and CORE2 using Switch Virtual Interfaces (SVIs). HSRP virtual IPs serve as the default gateways for all end devices.

---

## VLAN Summary Table

| VLAN ID | Name | Subnet | CORE1 SVI | CORE2 SVI | HSRP Virtual Gateway | Connected Switch | Purpose |
|---------|------|--------|-----------|-----------|----------------------|-----------------|---------|
| 10 | HR | 10.10.10.0/24 | 10.10.10.2 | 10.10.10.3 | 10.10.10.1 | ACC1 | Human Resources workstations |
| 20 | FINANCE | 10.10.20.0/24 | 10.10.20.2 | 10.10.20.3 | 10.10.20.1 | ACC1 | Finance department workstations |
| 30 | IT | 10.10.30.0/24 | 10.10.30.2 | 10.10.30.3 | 10.10.30.1 | ACC2 | IT department workstations |
| 40 | SOC | 10.10.40.0/24 | 10.10.40.2 | 10.10.40.3 | 10.10.40.1 | ACC2 | Security Operations Center |
| 50 | DEVELOPERS | 10.10.50.0/24 | 10.10.50.2 | 10.10.50.3 | 10.10.50.1 | ACC2 | Developer workstations |
| 80 | GUEST | 10.10.80.0/24 | 10.10.80.2 | 10.10.80.3 | 10.10.80.1 | ACC3 | Visitor / Guest Internet access |
| 90 | WIRELESS | 10.10.90.0/24 | 10.10.90.2 | 10.10.90.3 | 10.10.90.1 | ACC3 | Corporate wireless clients |
| 100 | SERVER-FARM | 10.10.100.0/24 | 10.10.100.2 | 10.10.100.3 | 10.10.100.1 | ACC3 | All internal enterprise servers |
| 110 | DMZ | 10.10.110.0/24 | 10.10.110.2 | 10.10.110.3 | 10.10.110.1 | ACC3 | Public-facing servers |
| 120 | MANAGEMENT | 10.10.120.0/24 | 10.10.120.2 | 10.10.120.3 | 10.10.120.1 | ACC3 | Admin PCs, AAA Server, Monitoring |

---

## Static IP Assignments (Server Farm — VLAN 100)

These servers use static IPs. DHCP is not used for any server.

| Server | IP Address | Role |
|--------|-----------|------|
| Active Directory Server | 10.10.100.10 | Identity management and user authentication |
| Internal DNS Server | 10.10.100.11 | Hostname resolution for internal services |
| DHCP Server | 10.10.100.12 | IP address assignment for all VLANs |
| File Server | 10.10.100.13 | Centralized file storage and user access |
| SIEM Server | 10.10.100.14 | Log collection, event monitoring, SOC visibility |
| Backup Server | 10.10.100.15 | Disaster recovery and ransomware protection |
| NTP Server | 10.10.100.16 | Time synchronization for all network devices |

---

## Static IP Assignments (DMZ — VLAN 110)

| Server | Internal IP | Public NAT IP | Role |
|--------|------------|---------------|------|
| Public Web Server | 10.10.110.10 | 203.0.113.10 | HTTP / HTTPS public website |
| Public DNS Server | 10.10.110.11 | 203.0.113.11 | External DNS resolution |
| Mail Server | 10.10.110.12 | 203.0.113.12 | Inbound and outbound email (SMTP) |

---

## Static IP Assignments (Management — VLAN 120)

| Device | IP Address | Role |
|--------|-----------|------|
| AAA Server | 10.10.120.10 | Centralized RADIUS authentication |
| Admin PC | 10.10.120.11 | Network administration |
| Monitoring PC | 10.10.120.12 | SOC monitoring and alerting |

---

## VLAN-to-Switch Port Mapping

### ACC1 — HR and Finance

| Port | VLAN | Connected Device |
|------|------|-----------------|
| Fa0/1 | 10 | HR-PC |
| Fa0/2 | 20 | Finance-PC |
| Fa0/3–22 | 999 | Unused (shutdown) |
| Gi0/1 | Trunk (10,20) | CORE1 Uplink |
| Gi0/2 | Trunk (10,20) | CORE2 Uplink |

### ACC2 — IT, SOC, Developers

| Port | VLAN | Connected Device |
|------|------|-----------------|
| Fa0/1 | 30 | IT-PC |
| Fa0/2 | 40 | SOC-PC |
| Fa0/3 | 50 | Developer-PC |
| Fa0/4–22 | 999 | Unused (shutdown) |
| Gi0/1 | Trunk (30,40,50) | CORE1 Uplink |
| Gi0/2 | Trunk (30,40,50) | CORE2 Uplink |

### ACC3 — Servers, DMZ, Wireless, Management

| Port | VLAN | Connected Device |
|------|------|-----------------|
| Fa0/1 | 100 | Active Directory Server |
| Fa0/2 | 100 | Internal DNS Server |
| Fa0/3 | 100 | DHCP Server |
| Fa0/4 | 100 | File Server |
| Fa0/5 | 100 | SIEM Server |
| Fa0/6 | 100 | Backup Server |
| Fa0/7 | 100 | NTP Server |
| Fa0/8 | 110 | DMZ Web Server |
| Fa0/9 | 110 | DMZ Public DNS |
| Fa0/10 | 110 | DMZ Mail Server |
| Fa0/11 | 120 | Admin PC |
| Fa0/12 | 120 | Monitoring PC |
| Fa0/13 | 120 | AAA Server |
| Fa0/14 | 90 | Corporate Wireless AP |
| Fa0/15 | 80 | Guest Wireless AP |
| Fa0/16–24 | 999 | Unused (shutdown) |
| Gi0/1 | Trunk (80,90,100,110,120) | CORE1 Uplink |
| Gi0/2 | Trunk (80,90,100,110,120) | CORE2 Uplink |

---

## Why Each VLAN Exists

**VLAN 10 — HR**: Isolates human resources systems from finance, IT, and developer VLANs. HR personnel should not have direct network-layer access to financial data systems.

**VLAN 20 — Finance**: Finance systems require strict isolation due to the sensitivity of financial data. Separated from all other user VLANs.

**VLAN 30 — IT**: IT personnel require access to management and server systems. Kept separate from general user VLANs.

**VLAN 40 — SOC**: The Security Operations Center needs visibility into the SIEM and server farm. Isolated to prevent SOC tools from being reached by unauthorized users.

**VLAN 50 — Developers**: Developer workstations are isolated because development environments often run tools and scripts that should not interact with production systems.

**VLAN 80 — Guest**: Guest users are untrusted. This VLAN is ACL-restricted to Internet-only access with no path to any internal subnet.

**VLAN 90 — Wireless**: Corporate wireless clients are given a separate VLAN because the wireless medium is inherently less secure than wired. Wireless is blocked from the Management VLAN.

**VLAN 100 — Server Farm**: All internal servers are grouped into a dedicated VLAN, making it simple to apply consistent access control policies.

**VLAN 110 — DMZ**: Public-facing servers sit in the DMZ, isolated from the internal network. If a DMZ server is compromised, the attacker cannot reach the internal Server Farm.

**VLAN 120 — Management**: The management VLAN carries admin traffic for network devices. It is the most sensitive VLAN and is isolated from all user and wireless VLANs.
