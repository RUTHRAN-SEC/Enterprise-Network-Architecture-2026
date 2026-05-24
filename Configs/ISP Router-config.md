# ISP Router All Configuration Commands used in the Project

#### ISP-Router : Cisco 2911 Router
#### Role       : Simulates Internet Edge / ISP Connection
#### Project    : Enterprise Network Architecture 2026

### About this Device

In a real enterprise environment, the ISP Router is managed by the Internet Service Provider.

In this Packet Tracer lab, this router simulates:
- The Internet edge
- Public Internet routing
- Upstream connectivity
- External network communication

This router connects directly to the ASA Firewall outside interface.

### Changed the hostname
```
hostname ISP-Router
```

### Outside Interface - Simulated Internet
```
interface GigabitEthernet0/0
description LINK-TO-INTERNET-SIMULATED
ip address 8.8.8.1 255.255.255.0
no shutdown
```

### Inside Interface - Connected to ASA Firewall
```
interface GigabitEthernet0/1
description LINK-TO-ASA-OUTSIDE
ip address 203.0.113.1 255.255.255.0
no shutdown
```

### Default Route to Simulated Internet
```
ip route 0.0.0.0 0.0.0.0 8.8.8.1
```

### Route Back to Enterprise Network

#### Routes public NAT addresses back toward the ASA Firewall
```
ip route 203.0.113.0 255.255.255.0 203.0.113.2
```

### Syslog Configuration
```
logging host 10.10.100.14
logging trap informational
service timestamps log datetime msec
```

### NTP Configuration
```
ntp server 10.10.100.16
```

### SSH Management
```
ip domain-name isp.local
crypto key generate rsa modulus 2048
ip ssh version 2
username admin privilege 15 secret EnterpriseAdmin2026
```

### Secure Remote Access
```
line vty 0 4
transport input ssh
login local
exec-timeout 10 0
```

### Console Access Security
```
line con 0
exec-timeout 5 0
login local
```

### Save Configuration
```
write memory
```

### Verification Commands
```
show ip interface brief
show ip route
show running-config
show logging
show ssh
```

### Security Features Implemented

- SSH-only management
- Secure administrative access
- Syslog logging
- NTP synchronization
- Routed Internet edge simulation
- Enterprise perimeter routing

### Enterprise Concepts Demonstrated

| Feature | Purpose |
|---|---|
| Public IP Addressing | Simulates real Internet connectivity |
| Static Routing | Directs enterprise traffic properly |
| Internet Edge Routing | Represents ISP infrastructure |
| SSH Management | Secure administration |
| Logging | SOC visibility and monitoring |
| Time Synchronization | Accurate log correlation |

### Packet Flow Example

#### Internal User Accessing Internet

```text
HR-PC
↓
ACC1
↓
CORE1
↓
ASA Firewall
↓
ISP-Router
↓
Internet
```

### Return Traffic Flow

```text
Internet
↓
ISP-Router
↓
ASA Firewall
↓
CORE1
↓
Internal VLAN
↓
User Device
```

### Real Enterprise Usage

In real organizations:
- ISP routers are usually provider-managed
- Multiple ISPs may exist for redundancy
- BGP is often used instead of static routes
- DDoS protection may exist upstream
- MPLS or SD-WAN connectivity may be integrated

### Packet Tracer Limitations

Packet Tracer cannot fully simulate:
- Real Internet behavior
- BGP routing
- ISP cloud infrastructure
- Real carrier-grade NAT
- Internet-scale routing tables
- Advanced WAN optimization

### Make sure to Save all configuration
```
end
write memory
```

**Thats all for the ISP Router config settings**
