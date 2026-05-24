# HSRP Design — Enterprise Network Architecture 2026

## Overview

HSRP (Hot Standby Router Protocol) provides gateway redundancy for all VLANs in the enterprise network. It ensures that if CORE1 (the primary Layer 3 switch) fails, CORE2 automatically assumes the active gateway role without any manual intervention and without disrupting connected users.

HSRP eliminates the single point of failure that would otherwise exist if only one core switch performed inter-VLAN routing.

---

## How HSRP Works in This Network

Two core switches share a virtual IP address per VLAN. End devices are configured to use the virtual IP as their default gateway. They do not know or care which physical switch is handling traffic at any given time.

- **CORE1** is the Active router for all VLANs (higher priority).
- **CORE2** is the Standby router for all VLANs (lower priority).
- The **Virtual IP** (.1 in each VLAN subnet) is the address all devices use as their gateway.
- If CORE1 fails, CORE2 sends gratuitous ARP and takes ownership of the virtual IP within seconds.
- When CORE1 recovers, it preempts CORE2 and reclaims the Active role.

---

## HSRP Group and IP Assignments

One HSRP group is configured per VLAN. The group number matches the VLAN ID for clarity.

| VLAN | VLAN Name | CORE1 Real IP | CORE2 Real IP | Virtual IP (Gateway) | HSRP Group | CORE1 Priority | CORE2 Priority |
|------|-----------|--------------|--------------|----------------------|------------|----------------|----------------|
| 10 | HR | 10.10.10.2 | 10.10.10.3 | 10.10.10.1 | 10 | 110 | 100 |
| 20 | FINANCE | 10.10.20.2 | 10.10.20.3 | 10.10.20.1 | 20 | 110 | 100 |
| 30 | IT | 10.10.30.2 | 10.10.30.3 | 10.10.30.1 | 30 | 110 | 100 |
| 40 | SOC | 10.10.40.2 | 10.10.40.3 | 10.10.40.1 | 40 | 110 | 100 |
| 50 | DEVELOPERS | 10.10.50.2 | 10.10.50.3 | 10.10.50.1 | 50 | 110 | 100 |
| 80 | GUEST | 10.10.80.2 | 10.10.80.3 | 10.10.80.1 | 80 | 110 | 100 |
| 90 | WIRELESS | 10.10.90.2 | 10.10.90.3 | 10.10.90.1 | 90 | 110 | 100 |
| 100 | SERVER-FARM | 10.10.100.2 | 10.10.100.3 | 10.10.100.1 | 100 | 110 | 100 |
| 110 | DMZ | 10.10.110.2 | 10.10.110.3 | 10.10.110.1 | 110 | 110 | 100 |
| 120 | MANAGEMENT | 10.10.120.2 | 10.10.120.3 | 10.10.120.1 | 120 | 110 | 100 |

---

## Configuration — CORE1 (Active)

Example shown for VLAN 10. The same pattern applies to all other VLANs.

```
interface Vlan10
 ip address 10.10.10.2 255.255.255.0
 standby 10 ip 10.10.10.1
 standby 10 priority 110
 standby 10 preempt
```

The **preempt** command is critical. Without it, CORE1 would not automatically reclaim the Active role after recovering from a failure. With preempt, CORE1 takes back control as soon as it comes back online and its priority is confirmed higher than CORE2.

---

## Configuration — CORE2 (Standby)

```
interface Vlan10
 ip address 10.10.10.3 255.255.255.0
 standby 10 ip 10.10.10.1
 standby 10 priority 100
```

CORE2 does not have the preempt command. If CORE2 were ever to become Active (because CORE1 failed), it will not try to take back the Active role when CORE1 recovers. CORE1 with preempt handles the failback automatically.

---

## Failover Sequence

| Event | What Happens |
|-------|-------------|
| Normal operation | CORE1 is Active for all VLANs. CORE2 monitors via hello messages. |
| CORE1 loses power or link | CORE2 detects missing hello messages after the dead interval (10 seconds default). |
| CORE2 takes over | CORE2 sends a gratuitous ARP claiming the virtual MAC address. All devices update their ARP tables. |
| Traffic continues | End devices continue sending traffic to the same virtual IP. CORE2 now handles routing. |
| CORE1 recovers | CORE1 comes back online, detects CORE2 is Active, and preempts it due to higher priority. |
| CORE1 reclaims Active | CORE1 is Active again. Normal operation resumes. Total downtime was seconds. |

---

## Verification Commands

To verify HSRP status on either core switch:

```
show standby
show standby brief
show standby vlan 10
```

Expected output on CORE1 (Active state):
```
Vlan10 - Group 10
  State is Active
  Virtual IP address is 10.10.10.1
  Active virtual MAC address is 0000.0c07.ac0a
  Local virtual MAC address is 0000.0c07.ac0a
  Hello time 3 sec, hold time 10 sec
  Preemption enabled
  Active router is local
  Standby router is 10.10.10.3
  Priority 110
```

---

## Why HSRP Is Critical for Enterprise Networks

Without HSRP, if CORE1 goes down every device using 10.10.10.2 as their gateway loses all routing capability. Network connectivity stops for the entire VLAN until CORE1 is manually restored. In a business environment this causes direct operational and financial impact.

With HSRP, the failover is automatic and transparent. The network continues to function within seconds of a core switch failure, which is the expected behavior in any production enterprise network.
