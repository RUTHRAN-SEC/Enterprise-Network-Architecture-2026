# ASA Firewall All Configuration Commands used in the Project

#### ASA-Firewall : Cisco ASA 5506
#### Role       : Perimeter Firewall | NAT/PAT | DMZ Isolation
#### Zones      : Outside (0) | Inside (100) | DMZ (50)
#### Project    : Enterprise Network Architecture 2026

### Traffic Model

- Internet → Outside (security-level 0)
- Outside ↔ Inside : controlled by ACLs + NAT
- Inside → DMZ : permitted (higher to lower)
- Outside → DMZ : permitted for public services only
- DMZ → Inside : denied (DMZ isolation)


### Changed the hostname
```
hostname ASA-Firewall
```

### Interface Configuration

#### Outside Interface - Connected to ISP Router
```
interface GigabitEthernet1/1
nameif outside
security-level 0
ip address 203.0.113.2 255.255.255.0
no shutdown
```

#### Inside Interface - Connected to CORE1
```
interface GigabitEthernet1/2
nameif inside
security-level 100
ip address 10.10.0.1 255.255.255.0
no shutdown
```

#### DMZ Interface - Connected to ACC3
```
interface GigabitEthernet1/3
nameif dmz
security-level 50
ip address 10.10.110.1 255.255.255.0
no shutdown
```

### Default Route to ISP
```
route outside 0.0.0.0 0.0.0.0 203.0.113.1
```

### NAT Configuration

#### PAT - Internal Users Share One Public IP
```
object network INTERNAL-USERS
subnet 10.10.0.0 255.255.0.0
nat (inside,outside) dynamic interface
```

#### Static NAT - DMZ Web Server
```
object network DMZ-WEB-SERVER
host 10.10.110.10
nat (dmz,outside) static 203.0.113.10
```

#### Static NAT - DMZ Mail Server
```
object network DMZ-MAIL-SERVER
host 10.10.110.12
nat (dmz,outside) static 203.0.113.12
```

#### Static NAT - DMZ Public DNS Server
```
object network DMZ-DNS-SERVER
host 10.10.110.11
nat (dmz,outside) static 203.0.113.11
```

### Access Control Lists

#### Outside Interface ACL - Allow Public Services Only
```
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.10 eq 80
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.10 eq 443
access-list OUTSIDE-IN extended permit tcp any host 203.0.113.12 eq 25
access-list OUTSIDE-IN extended permit udp any host 203.0.113.11 eq 53
access-list OUTSIDE-IN extended deny ip any any
```

#### Apply ACL to Outside Interface
```
access-group OUTSIDE-IN in interface outside
```

### DMZ Isolation Policy

#### Block DMZ Servers from Accessing Internal Network
```
access-list DMZ-TO-INSIDE extended deny ip 10.10.110.0 0.0.0.255 10.10.0.0 0.0.255.255
access-list DMZ-TO-INSIDE extended permit ip 10.10.110.0 0.0.0.255 any
```

#### Apply DMZ ACL
```
access-group DMZ-TO-INSIDE in interface dmz
```

### Syslog to SIEM Server
```
logging host inside 10.10.100.14
logging trap informational
logging enable
service timestamps log datetime msec
```

### SNMP Monitoring
```
snmp-server community MONITOR ro
snmp-server host inside 10.10.100.14 community MONITOR version 2c
```

### NTP Configuration
```
ntp server 10.10.100.16
```

### SSH Management

#### Allow SSH Only from Management VLAN
```
ssh 10.10.120.0 255.255.255.0 inside
ssh version 2
ssh timeout 10
username admin password EnterpriseAdmin2026 privilege 15
```

### Save Configuration
```
write memory
```

### Verification Commands
```
show interface ip brief
show route
show nat
show xlate
show access-list
show conn
show logging
show ssh
show running-config
```

### Security Features Implemented

- NAT/PAT for Internet access
- Static NAT for DMZ services
- DMZ isolation
- Least privilege ACLs
- SSH-only management
- Syslog forwarding to SIEM
- SNMP monitoring
- Network segmentation
- Zero Trust principles
- Defense-in-depth architecture

### Public Services Exposed to Internet

| Service | Public IP | Internal Server |
|---|---|---|
| Web Server | 203.0.113.10 | 10.10.110.10 |
| DNS Server | 203.0.113.11 | 10.10.110.11 |
| Mail Server | 203.0.113.12 | 10.10.110.12 |

### Make sure to Save all configuration
```
end
write memory
```

**Thats all for the ASA Firewall config settings**
