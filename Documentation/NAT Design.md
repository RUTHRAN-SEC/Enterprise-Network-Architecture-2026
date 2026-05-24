# NAT/PAT Design — Enterprise Network Architecture 2026

## Overview

Network Address Translation (NAT) is implemented on the Cisco ASA Firewall to serve two purposes in this network: allowing all internal users to access the Internet using a single public IP address, and making specific DMZ servers reachable from the Internet using permanent public IP mappings.

Two NAT types are used: PAT for internal user Internet access, and Static NAT for DMZ public servers.

---

## NAT Types Implemented

| NAT Type | Used For | Direction |
|----------|---------|-----------|
| PAT (Dynamic NAT with Overload) | Internal users accessing Internet | Inside to Outside |
| Static NAT | DMZ servers receiving Internet connections | Outside to DMZ |

---

## Part 1 — PAT (Port Address Translation)

### What It Does

PAT allows many internal devices across all VLANs to share a single public IP address when accessing the Internet. The ASA tracks each session using unique port numbers so return traffic is delivered to the correct internal device.

### Why It Is Used

The enterprise has thousands of potential internal devices across ten VLANs. Assigning a unique public IP to each device is not feasible. PAT conserves public IP addresses while also hiding all internal addressing from the Internet — no external party can determine what internal IPs exist.

### Addressing

| Element | Value |
|---------|-------|
| Internal Source Range | 10.10.0.0/16 (all internal VLANs) |
| Translated Public IP | 203.0.113.2 (ASA outside interface IP) |
| Translation Method | Dynamic PAT using outside interface IP |

### ASA Configuration

```
object network INTERNAL-USERS
 subnet 10.10.0.0 255.255.0.0
 nat (inside,outside) dynamic interface
```

The `dynamic interface` keyword tells the ASA to translate all traffic from the 10.10.0.0/16 range to the IP address currently assigned to the outside interface (203.0.113.2). Port numbers differentiate sessions from different internal hosts.

### Packet Flow — Internal User Browsing the Internet

```
Step 1: HR-PC sends packet
        Source: 10.10.10.50:49201  Destination: 8.8.8.8:80

Step 2: Packet reaches ASA inside interface
        ASA checks NAT policy — matches INTERNAL-USERS object

Step 3: ASA translates source IP and creates NAT session
        Source: 203.0.113.2:51001  Destination: 8.8.8.8:80

Step 4: Packet exits ASA outside interface to ISP Router

Step 5: Return traffic arrives from Internet
        Source: 8.8.8.8:80  Destination: 203.0.113.2:51001

Step 6: ASA matches return traffic to NAT session table
        Translates back: Destination: 10.10.10.50:49201

Step 7: ASA forwards packet to CORE1, which routes to HR-PC
```

---

## Part 2 — Static NAT (DMZ Public Servers)

### What It Does

Static NAT creates a permanent one-to-one mapping between a public IP address and a specific DMZ server's private IP. This mapping persists at all times, allowing Internet users to reliably reach the public services hosted in the DMZ.

### Why It Is Used

Unlike internal users who only initiate outbound connections, DMZ servers must accept inbound connections initiated from the Internet. For this to work, a fixed and predictable public IP must always resolve to the correct server. Static NAT provides this permanent mapping.

### Static NAT Mapping Table

| Server | Internal IP (DMZ) | Public IP (Internet-Facing) | Service |
|--------|--------------------|------------------------------|---------|
| Public Web Server | 10.10.110.10 | 203.0.113.10 | HTTP (80), HTTPS (443) |
| Public DNS Server | 10.10.110.11 | 203.0.113.11 | DNS (UDP 53) |
| Mail Server | 10.10.110.12 | 203.0.113.12 | SMTP (25) |

### ASA Configuration

```
! Web Server Static NAT
object network DMZ-WEB-SERVER
 host 10.10.110.10
 nat (dmz,outside) static 203.0.113.10

! Public DNS Server Static NAT
object network DMZ-DNS-SERVER
 host 10.10.110.11
 nat (dmz,outside) static 203.0.113.11

! Mail Server Static NAT
object network DMZ-MAIL-SERVER
 host 10.10.110.12
 nat (dmz,outside) static 203.0.113.12
```

### Packet Flow — Internet User Accessing Web Server

```
Step 1: Internet client sends HTTP request
        Source: 1.2.3.4:54321  Destination: 203.0.113.10:80

Step 2: ISP Router forwards to ASA outside interface

Step 3: ASA checks OUTSIDE-IN ACL
        Rule matches: permit tcp any host 203.0.113.10 eq 80 — ALLOWED

Step 4: ASA checks Static NAT table
        203.0.113.10 maps to DMZ host 10.10.110.10

Step 5: ASA translates destination and forwards to DMZ
        Source: 1.2.3.4:54321  Destination: 10.10.110.10:80

Step 6: Web Server processes request and responds
        Source: 10.10.110.10:80  Destination: 1.2.3.4:54321

Step 7: Return traffic passes through ASA
        ASA translates source back to 203.0.113.10:80

Step 8: Internet client receives the response
```

---

## Public IP Address Allocation

| Public IP | Assigned To | NAT Type |
|-----------|------------|---------|
| 203.0.113.1 | ISP Router (Inside facing ASA) | N/A |
| 203.0.113.2 | ASA Outside Interface (PAT address) | Dynamic PAT |
| 203.0.113.10 | Public Web Server (Static NAT) | Static NAT |
| 203.0.113.11 | Public DNS Server (Static NAT) | Static NAT |
| 203.0.113.12 | Mail Server (Static NAT) | Static NAT |

---

## Security Impact of NAT

PAT hides the internal addressing scheme from the Internet. An external attacker scanning the network will only ever see the public-facing IP addresses. They cannot determine how many internal devices exist, what subnets are in use, or what the internal topology looks like. This significantly reduces information available to an attacker performing reconnaissance.

Static NAT for DMZ servers exposes only the specific servers that need to be reachable publicly. All other internal systems remain completely invisible to the Internet.
