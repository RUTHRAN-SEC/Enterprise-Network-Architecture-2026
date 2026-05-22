# Enterprise-Network-Architecture-2026

A fully implemented enterprise-grade network simulation designed and built entirely in Cisco Packet Tracer. This project models a realistic 2026 enterprise network incorporating Zero Trust architecture, layered defense strategy, VLAN-based segmentation, redundant core infrastructure, DMZ isolation, and centralized security monitoring.

---

## Table of Contents

- Project Overview
- Architecture Diagram Image
- VLAN Design
- Security Controls
- Technologies Used
- Repository Structure
- What to Add to This Repository
- How to Open the Project
- Validation Tests
- Author

---

## Project Overview

This project simulates a complete modern enterprise network as it would be deployed in a real-world corporate environment. Every component was designed, configured, and validated individually using Cisco Packet Tracer.

The goal was to build an enterprise network that reflects the security principles used in banking, healthcare, government, and technology sectors — combining network engineering with cybersecurity best practices into a single cohesive lab.

This was a solo project. Every design decision, configuration, and security control was planned and implemented independently, including:

- Full VLAN architecture design from scratch
- Manual configuration of all Layer 2 and Layer 3 devices
- ACL policy design and enforcement across all segments
- Firewall zone configuration and NAT implementation
- HSRP redundancy setup across dual core switches
- Layer 2 attack protection on all access ports
- DMZ architecture and static NAT for public services
- SIEM and syslog integration for monitoring visibility
- AAA authentication and SSH hardening across all infrastructure

###  Fully Guide for the Project


---

## Architecture Diagram Image 

#### Image Made by the ChatGPT for the good understanding of the topology
<img width="1536" height="1024" alt="b7ebc59d-d102-4a0a-aed3-7e996c5d5a15" src="https://github.com/user-attachments/assets/9f1de40d-ce5c-48cc-ad24-fd5a857189bf" />

### Key Zones

| Zone           | Description                                                  |
|----------------|--------------------------------------------------------------|
| Internet Edge  | ISP Router + Cisco ASA Firewall                              |
| Core Layer     | CORE1 and CORE2 with HSRP and inter-VLAN routing             |
| Access Layer   | ACC1, ACC2, ACC3 connecting endpoints                        |
| DMZ            | Public Web Server, Public DNS Server, Mail Server            |
| Server Farm    | Active Directory, Internal DNS, DHCP, File, SIEM, Backup, NTP|
| Management Zone| Admin PC, Monitoring PC, AAA Server (isolated VLAN)          |
| Wireless       | Corporate SSID and Guest SSID on separate VLANs              |

---

## VLAN Design

| VLAN | Name        | Subnet           | Gateway       |
|------|-------------|------------------|---------------|
| 10   | HR          | 10.10.10.0/24    | 10.10.10.1    |
| 20   | Finance     | 10.10.20.0/24    | 10.10.20.1    |
| 30   | IT          | 10.10.30.0/24    | 10.10.30.1    |
| 40   | SOC         | 10.10.40.0/24    | 10.10.40.1    |
| 50   | Developers  | 10.10.50.0/24    | 10.10.50.1    |
| 80   | Guest       | 10.10.80.0/24    | 10.10.80.1    |
| 90   | Wireless    | 10.10.90.0/24    | 10.10.90.1    |
| 100  | Server Farm | 10.10.100.0/24   | 10.10.100.1   |
| 110  | DMZ         | 10.10.110.0/24   | 10.10.110.1   |
| 120  | Management  | 10.10.120.0/24   | 10.10.120.1   |

---

## Security Controls

### Perimeter Security
- Cisco ASA Firewall with Outside, Inside, and DMZ zones
- PAT for internal users accessing the Internet
- Static NAT for DMZ public servers (Web, DNS, Mail)
- ACL-based traffic filtering at the perimeter

### Layer 2 Security
- Port Security — prevents unauthorized endpoint connections
- BPDU Guard — protects against rogue switch insertion and STP manipulation
- DHCP Snooping — blocks rogue DHCP servers on untrusted ports
- Dynamic ARP Inspection (DAI) — prevents ARP spoofing and MITM attacks
- Unused port shutdown across all access switches

### Access Control
- Extended ACLs enforcing Zero Trust between VLANs
- Guest VLAN restricted to Internet access only
- Wireless VLAN blocked from Management VLAN
- HR blocked from Finance VLAN
- SOC VLAN permitted access to SIEM Server

### Management Security
- Telnet disabled on all devices
- SSH configured as the sole remote management protocol
- AAA authentication using TACACS+ model
- Management VLAN isolated from all user VLANs

### Redundancy
- HSRP configured on CORE1 (Active) and CORE2 (Standby)
- Virtual gateways per VLAN for transparent failover
- Dual core switch design eliminates single points of failure

### Monitoring
- Syslog configured and forwarded to SIEM Server
- SNMP monitoring for interface, CPU, and uptime visibility
- NTP synchronization for accurate log timestamps across all devices

---

## Technologies Used

| Technology              | Purpose                                      |
|-------------------------|----------------------------------------------|
| Cisco Packet Tracer     | Network simulation platform                  |
| Cisco ASA Firewall      | Perimeter security, NAT, DMZ                 |
| Cisco Catalyst Switches | Layer 2/3 switching, VLANs, STP              |
| HSRP                    | Gateway redundancy and high availability     |
| VLAN / 802.1Q Trunking  | Network segmentation                         |
| ACLs                    | Traffic filtering and Zero Trust enforcement |
| DHCP                    | Automated IP addressing                      |
| SSH                     | Secure remote management                     |
| AAA / TACACS+           | Centralized authentication and accounting    |
| SIEM / Syslog           | Centralized logging and SOC visibility       |
| SNMP                    | Network device monitoring                    |
| NAT / PAT               | Address translation and IP conservation      |
| DHCP Snooping / DAI     | Layer 2 attack mitigation                    |
| BPDU Guard              | Spanning Tree protection                     |
| NTP                     | Time synchronization                         |

---

## Repository Structure

```
Enterprise-Network-Architecture-2026/
|
|-- packet-tracer/
|   |-- Enterprise-Network-2026.pkt          # Main Packet Tracer project file (ADD HERE)
|
|-- diagrams/
|   |-- network-topology.png                 # Full network topology diagram
|   |-- vlan-design.png                      # VLAN segmentation diagram
|   |-- dmz-architecture.png                 # DMZ zone diagram
|   |-- firewall-zones.png                   # ASA firewall zone diagram
|
|-- configs/
|   |-- CORE1-config.txt                     # Running configuration for CORE1
|   |-- CORE2-config.txt                     # Running configuration for CORE2
|   |-- ACC1-config.txt                      # Running configuration for ACC1
|   |-- ACC2-config.txt                      # Running configuration for ACC2
|   |-- ACC3-config.txt                      # Running configuration for ACC3
|   |-- ASA-Firewall-config.txt              # ASA firewall configuration
|   |-- ISP-Router-config.txt                # ISP Router configuration
|
|-- documentation/
|   |-- Enterprise-Network-Documentation.md  # Full project documentation
|   |-- acl-policy.md                        # ACL rules and segmentation logic
|   |-- vlan-table.md                        # VLAN table with subnets and gateways
|   |-- hsrp-design.md                       # HSRP configuration notes
|   |-- nat-design.md                        # NAT/PAT design documentation
|
|-- screenshots/
|   |-- vlan-connectivity-test.png           # Ping test results between VLANs
|   |-- acl-deny-test.png                    # ACL block verification
|   |-- hsrp-failover-test.png               # HSRP failover demonstration
|   |-- dhcp-test.png                        # DHCP lease verification
|   |-- ssh-management-test.png              # SSH access verification
|   |-- guest-isolation-test.png             # Guest VLAN Internet-only test
|
|-- README.md
```

---

## What to Add to This Repository

The following items should be added to complete the repository properly.

### Required

- **Packet Tracer File** — Place your `.pkt` file inside the `packet-tracer/` folder. This is the core deliverable of the project.
- **Network Topology Diagram** — Export or screenshot your full topology from Packet Tracer and add it to `diagrams/`. This is the first thing a viewer looks at.

### Strongly Recommended

- **Device Configurations** — Use `show running-config` on each device in Packet Tracer and save the output as `.txt` files in `configs/`. This demonstrates your actual CLI work.
- **Validation Screenshots** — Capture ping tests, ACL deny results, DHCP leases, and HSRP failover inside Packet Tracer. These prove the network functions as designed.
- **ACL Policy Document** — Write out each ACL rule with the reasoning behind it. This shows security design thinking.

### Optional but Professional

- **Additional Diagrams** — Separate diagrams for the DMZ, firewall zones, and VLAN segmentation make the repository easier to understand at a glance.
- **Attack Defense Table** — A markdown table mapping each attack vector to the specific control implemented in this project.

---

## How to Open the Project

1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (version 8.x or later recommended).
2. Clone or download this repository.
3. Open the file located at `packet-tracer/Enterprise-Network-2026.pkt` using Packet Tracer.
4. The full topology will load with all configurations intact.

---

## Validation Tests

The following tests were completed and verified within Packet Tracer:

| Test                         | Result |
|------------------------------|--------|
| VLAN connectivity            | Pass   |
| Inter-VLAN routing           | Pass   |
| ACL filtering enforcement    | Pass   |
| Guest VLAN isolation         | Pass   |
| Wireless VLAN restrictions   | Pass   |
| HSRP failover                | Pass   |
| NAT / PAT functionality      | Pass   |
| DHCP address assignment      | Pass   |
| Syslog forwarding to SIEM    | Pass   |
| SSH remote management        | Pass   |
| Port security enforcement    | Pass   |

---

## Author

### RUTHRAN-SEC
