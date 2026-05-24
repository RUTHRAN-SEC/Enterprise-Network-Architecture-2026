# ACL Policy — Enterprise Network Architecture 2026

## Overview

This document defines every Access Control List (ACL) rule implemented in the Enterprise Network Architecture 2026 project. All ACLs enforce a Zero Trust posture: traffic between VLANs is denied by default and only explicitly permitted where required.

ACLs are applied on **CORE1 and CORE2** at the SVI (Switch Virtual Interface) level in the **inbound direction**. This means ACLs filter traffic as it enters the core switch from each VLAN, before it is routed to another segment.

---

## ACL Design Principles

- **Default Deny**: All inter-VLAN traffic not explicitly permitted is dropped.
- **Least Privilege**: Each VLAN is granted only the minimum access needed.
- **Inbound Application**: ACLs are applied inbound on the source VLAN SVI.
- **Identical on CORE1 and CORE2**: Both core switches carry the same ACL rules to maintain enforcement during HSRP failover.

---

## ACL 1 — GUEST-ACL

**Applied To:** VLAN 80 (Guest) SVI — Inbound

**Intent:** Guest users are visitors with no business requirement to access internal systems. This ACL restricts Guest to Internet-only access. Any attempt to reach any internal subnet is dropped.

```
ip access-list extended GUEST-ACL
 remark Block Guest from all internal networks (10.10.0.0/16)
 deny   ip 10.10.80.0 0.0.0.255 10.10.0.0 0.0.255.255
 remark Permit Guest outbound to Internet
 permit ip 10.10.80.0 0.0.0.255 any
```

**Application:**
```
interface Vlan80
 ip access-group GUEST-ACL in
```

| Rule | Action | Source | Destination | Reason |
|------|--------|--------|-------------|--------|
| 1 | DENY | 10.10.80.0/24 | 10.10.0.0/16 | Blocks Guest from all internal VLANs |
| 2 | PERMIT | 10.10.80.0/24 | any | Allows Internet-only access |

---

## ACL 2 — WIRELESS-ACL

**Applied To:** VLAN 90 (Wireless) SVI — Inbound

**Intent:** Wireless corporate clients are allowed to reach internal resources but must never be able to access the Management VLAN (VLAN 120). Management infrastructure must remain isolated from wireless-connected devices.

```
ip access-list extended WIRELESS-ACL
 remark Block Wireless clients from reaching Management VLAN
 deny   ip 10.10.90.0 0.0.0.255 10.10.120.0 0.0.0.255
 remark Permit Wireless to all other resources
 permit ip 10.10.90.0 0.0.0.255 any
```

**Application:**
```
interface Vlan90
 ip access-group WIRELESS-ACL in
```

| Rule | Action | Source | Destination | Reason |
|------|--------|--------|-------------|--------|
| 1 | DENY | 10.10.90.0/24 | 10.10.120.0/24 | Blocks Wireless from Management VLAN |
| 2 | PERMIT | 10.10.90.0/24 | any | Allows access to all other segments |

---

## ACL 3 — HR-ACL

**Applied To:** VLAN 10 (HR) SVI — Inbound

**Intent:** Finance data is confidential. HR personnel have no business requirement to directly access Finance systems. This ACL prevents HR from initiating any connection to the Finance VLAN.

```
ip access-list extended HR-ACL
 remark Block HR from accessing Finance VLAN directly
 deny   ip 10.10.10.0 0.0.0.255 10.10.20.0 0.0.0.255
 remark Permit HR to all other resources
 permit ip 10.10.10.0 0.0.0.255 any
```

**Application:**
```
interface Vlan10
 ip access-group HR-ACL in
```

| Rule | Action | Source | Destination | Reason |
|------|--------|--------|-------------|--------|
| 1 | DENY | 10.10.10.0/24 | 10.10.20.0/24 | Blocks HR from Finance VLAN |
| 2 | PERMIT | 10.10.10.0/24 | any | Allows HR to servers and Internet |

---

## ACL 4 — SOC-ACL

**Applied To:** VLAN 40 (SOC) SVI — Inbound

**Intent:** The Security Operations Center requires access to the SIEM Server (10.10.100.14) and the full Server Farm to perform monitoring, investigation, and incident response. This ACL explicitly permits SOC to the Server Farm and allows general connectivity.

```
ip access-list extended SOC-ACL
 remark Allow SOC full access to Server Farm (includes SIEM at .14)
 permit ip 10.10.40.0 0.0.0.255 10.10.100.0 0.0.0.255
 remark Allow SOC general connectivity
 permit ip 10.10.40.0 0.0.0.255 any
```

**Application:**
```
interface Vlan40
 ip access-group SOC-ACL in
```

| Rule | Action | Source | Destination | Reason |
|------|--------|--------|-------------|--------|
| 1 | PERMIT | 10.10.40.0/24 | 10.10.100.0/24 | SOC access to full Server Farm |
| 2 | PERMIT | 10.10.40.0/24 | any | General SOC connectivity |

---

## ACL 5 — DMZ-TO-INSIDE (ASA Firewall)

**Applied To:** ASA DMZ Interface — Inbound

**Intent:** DMZ servers must never be able to initiate connections toward the internal network. If a DMZ server is compromised, this ACL prevents lateral movement into the enterprise.

```
access-list DMZ-TO-INSIDE extended deny   ip 10.10.110.0 0.0.0.255 10.10.0.0 0.0.255.255
access-list DMZ-TO-INSIDE extended permit ip 10.10.110.0 0.0.0.255 any
access-group DMZ-TO-INSIDE in interface dmz
```

---

## ACL 6 — OUTSIDE-IN (ASA Firewall)

**Applied To:** ASA Outside Interface — Inbound

**Intent:** No unsolicited Internet traffic is permitted inbound. Only specific protocols to specific static NAT public IPs for public services are allowed.

```
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.10 eq 80
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.10 eq 443
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.12 eq 25
access-list OUTSIDE-IN extended permit udp any host 203.0.113.11 eq 53
access-list OUTSIDE-IN extended deny   ip  any any
access-group OUTSIDE-IN in interface outside
```

| Rule | Action | Protocol | Destination | Port | Reason |
|------|--------|----------|-------------|------|--------|
| 1 | PERMIT | TCP | 203.0.113.10 | 80 | HTTP to Web Server |
| 2 | PERMIT | TCP | 203.0.113.10 | 443 | HTTPS to Web Server |
| 3 | PERMIT | TCP | 203.0.113.12 | 25 | SMTP to Mail Server |
| 4 | PERMIT | UDP | 203.0.113.11 | 53 | DNS to Public DNS Server |
| 5 | DENY | IP | any | any | Default deny all other inbound |

---

## Summary Table — All ACLs

| ACL Name | Applied On | Direction | Protects Against |
|----------|-----------|-----------|-----------------|
| GUEST-ACL | VLAN 80 SVI | Inbound | Guest accessing internal network |
| WIRELESS-ACL | VLAN 90 SVI | Inbound | Wireless clients reaching Management VLAN |
| HR-ACL | VLAN 10 SVI | Inbound | HR accessing Finance data directly |
| SOC-ACL | VLAN 40 SVI | Inbound | SOC blocked from SIEM (permits access) |
| DMZ-TO-INSIDE | ASA DMZ | Inbound | Compromised DMZ server pivoting inward |
| OUTSIDE-IN | ASA Outside | Inbound | Unsolicited Internet traffic entering network |
