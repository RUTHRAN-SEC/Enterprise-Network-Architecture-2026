##  CABLE AND PORT CONNECTION REFERENCE

#### This file documents every cable connection in the network. Each table lists the exact port-to-port wiring for every device. Use this as a reference when building the topology in Packet Tracer.

**Cable Type :**
- COPPER STRAIGHT-THROUGH : PC/Server to Switch, Router to Switch
- COPPER CROSS-OVER       : Switch to Switch, Switch to Router (uplink)
- AUTOMATIC               : Use when unsure of cable type in Packet Tracer


**DEVICE 1: ISP-Router  (Cisco 2911)**

| Local Port         | Cable Type              | Remote Device | Remote Port |
|--------------------|--------------------------|----------------|-------------|
| GigabitEthernet0/0 | Copper Straight-Through | ASA-Firewall   | GigabitEthernet1/1 |
| GigabitEthernet0/1 | Copper Straight-Through | Internet (Sim) | - |

### Notes:
- Gi0/0 IP : 203.0.113.1 connects to ASA Outside IP 203.0.113.2
- Gi0/1 IP : 8.8.8.1 simulates Internet-facing interface


**DEVICE 2: ASA-Firewall  (Cisco ASA 5506-X)**

| Local Port         | Cable Type              | Remote Device | Remote Port |
|--------------------|--------------------------|----------------|-------------|
| GigabitEthernet1/1 | Copper Straight-Through | ISP-Router | GigabitEthernet0/0 |
| GigabitEthernet1/2 | Copper Straight-Through | CORE1 | GigabitEthernet1/0/24 |
| GigabitEthernet1/3 | Copper Straight-Through | ACC3 | GigabitEthernet0/2 (DMZ) |

### Notes:
- Gi1/1 Outside zone Security-Level 0 IP 203.0.113.2
- Gi1/2 Inside zone Security-Level 100 IP 10.10.0.1
- Gi1/3 DMZ zone Security-Level 50 IP 10.10.110.1



**DEVICE 3: CORE1  (Cisco Catalyst 3650 / 3560 - Layer 3 Switch)**

| Local Port | Cable Type | Remote Device | Remote Port |
|-------------|-------------|---------------|-------------|
| GigabitEthernet1/0/24 | Copper Straight-Through | ASA-Firewall | GigabitEthernet1/2 |
| GigabitEthernet1/0/23 | Copper Cross-Over | CORE2 | GigabitEthernet1/0/23 |
| GigabitEthernet1/0/1 | Copper Cross-Over | ACC1 | GigabitEthernet0/1 |
| GigabitEthernet1/0/2 | Copper Cross-Over | ACC2 | GigabitEthernet0/1 |
| GigabitEthernet1/0/3 | Copper Cross-Over | ACC3 | GigabitEthernet0/1 |

### Notes:
- Gi1/0/24 Routed port (no switchport) IP 10.10.0.2
- Gi1/0/23 Inter-core trunk VLANs 10,20,30,40,50,80,90,100,110,120
- Gi1/0/1 Trunk to ACC1 VLANs 10,20
- Gi1/0/2 Trunk to ACC2 VLANs 30,40,50
- Gi1/0/3 Trunk to ACC3 VLANs 80,90,100,110,120


**DEVICE 4: CORE2  (Cisco Catalyst 3650 / 3560 - Layer 3 Switch)**

| Local Port | Cable Type | Remote Device | Remote Port |
|-------------|-------------|---------------|-------------|
| GigabitEthernet1/0/24 | Copper Straight-Through | ASA-Firewall | GigabitEthernet1/2 |
| GigabitEthernet1/0/23 | Copper Cross-Over | CORE1 | GigabitEthernet1/0/23 |
| GigabitEthernet1/0/1 | Copper Cross-Over | ACC1 | GigabitEthernet0/2 |
| GigabitEthernet1/0/2 | Copper Cross-Over | ACC2 | GigabitEthernet0/2 |
| GigabitEthernet1/0/3 | Copper Cross-Over | ACC3 | GigabitEthernet0/2 |

### Notes:
- Gi1/0/24 Routed port (no switchport) IP 10.10.0.3
- Gi1/0/23 Inter-core trunk VLANs 10,20,30,40,50,80,90,100,110,120
- Gi1/0/1 Redundant trunk to ACC1 VLANs 10,20
- Gi1/0/2 Redundant trunk to ACC2 VLANs 30,40,50
- Gi1/0/3 Redundant trunk to ACC3 VLANs 80,90,100,110,120


**DEVICE 5: ACC1  (Cisco Catalyst 2960 - Layer 2 Switch)**  
Serves : VLAN 10 (HR)  |  VLAN 20 (Finance)

| Local Port | Cable Type | Remote Device | Remote Port |
|-------------|-------------|---------------|-------------|
| GigabitEthernet0/1 | Copper Cross-Over | CORE1 | GigabitEthernet1/0/1 |
| GigabitEthernet0/2 | Copper Cross-Over | CORE2 | GigabitEthernet1/0/1 |
| FastEthernet0/1 | Copper Straight-Through | HR-PC | FastEthernet0 |
| FastEthernet0/2 | Copper Straight-Through | Finance-PC | FastEthernet0 |
| FastEthernet0/3-0/24 | - | UNUSED | Shutdown |

### Notes:
- Gi0/1 Primary trunk to CORE1 DHCP Snooping trusted
- Gi0/2 Redundant trunk to CORE2 DHCP Snooping trusted
- Fa0/1 Access port VLAN 10 Port Security + BPDU Guard + PortFast
- Fa0/2 Access port VLAN 20 Port Security + BPDU Guard + PortFast


**DEVICE 6: ACC2  (Cisco Catalyst 2960 - Layer 2 Switch)**
Serves : VLAN 30 (IT)  |  VLAN 40 (SOC)  |  VLAN 50 (Developers)

| Local Port | Cable Type | Remote Device | Remote Port |
|-------------|-------------|---------------|-------------|
| GigabitEthernet0/1 | Copper Cross-Over | CORE1 | GigabitEthernet1/0/2 |
| GigabitEthernet0/2 | Copper Cross-Over | CORE2 | GigabitEthernet1/0/2 |
| FastEthernet0/1 | Copper Straight-Through | IT-PC | FastEthernet0 |
| FastEthernet0/2 | Copper Straight-Through | SOC-PC | FastEthernet0 |
| FastEthernet0/3 | Copper Straight-Through | Developer-PC | FastEthernet0 |
| FastEthernet0/4-0/24 | - | UNUSED | Shutdown |

### Notes:
- Gi0/1 Primary trunk to CORE1 DHCP Snooping trusted
- Gi0/2 Redundant trunk to CORE2 DHCP Snooping trusted
- Fa0/1 Access port VLAN 30 Port Security + BPDU Guard + PortFast
- Fa0/2 Access port VLAN 40 Port Security + BPDU Guard + PortFast
- Fa0/3 Access port VLAN 50 Port Security + BPDU Guard + PortFast


**DEVICE 7: ACC3  (Cisco Catalyst 2960 - Layer 2 Switch)**
Serves : VLAN 80 (Guest) | VLAN 90 (Wireless) | VLAN 100 (Server Farm) | VLAN 110 (DMZ)  | VLAN 120 (Management)


| Local Port | Cable Type | Remote Device | Remote Port |
|-------------|-------------|---------------|-------------|
| GigabitEthernet0/1 | Copper Cross-Over | CORE1 | GigabitEthernet1/0/3 |
| GigabitEthernet0/2 | Copper Cross-Over | CORE2 | GigabitEthernet1/0/3 |
| FastEthernet0/1 | Copper Straight-Through | AD-Server | FastEthernet0 |
| FastEthernet0/2 | Copper Straight-Through | DNS-Server | FastEthernet0 |
| FastEthernet0/3 | Copper Straight-Through | DHCP-Server | FastEthernet0 |
| FastEthernet0/4 | Copper Straight-Through | File-Server | FastEthernet0 |
| FastEthernet0/5 | Copper Straight-Through | SIEM-Server | FastEthernet0 |
| FastEthernet0/6 | Copper Straight-Through | Backup-Server | FastEthernet0 |
| FastEthernet0/7 | Copper Straight-Through | NTP-Server | FastEthernet0 |
| FastEthernet0/8 | Copper Straight-Through | DMZ-Web-Server | FastEthernet0 |
| FastEthernet0/9 | Copper Straight-Through | DMZ-DNS-Server | FastEthernet0 |
| FastEthernet0/10 | Copper Straight-Through | DMZ-Mail-Server | FastEthernet0 |
| FastEthernet0/11 | Copper Straight-Through | Admin-PC | FastEthernet0 |
| FastEthernet0/12 | Copper Straight-Through | Monitor-PC | FastEthernet0 |
| FastEthernet0/13 | Copper Straight-Through | AAA-Server | FastEthernet0 |
| FastEthernet0/14 | Copper Straight-Through | Corporate-AP | FastEthernet0 |
| FastEthernet0/15 | Copper Straight-Through | Guest-AP | FastEthernet0 |
| FastEthernet0/16-24 | - | UNUSED | Shutdown |

### Notes:
- Gi0/1 Primary trunk to CORE1 DHCP Snooping trusted
- Gi0/2 Redundant trunk to CORE2 DHCP Snooping trusted
- Fa0/1-7 VLAN 100 Server Farm Port Security + BPDU Guard
- Fa0/8-10 VLAN 110 DMZ Servers Port Security + BPDU Guard
- Fa0/11-13 VLAN 120 Management Zone Port Security + BPDU Guard
- Fa0/14 VLAN 90 Corporate WiFi AP BPDU Guard + PortFast
- Fa0/15 VLAN 80 Guest WiFi AP BPDU Guard + PortFast
