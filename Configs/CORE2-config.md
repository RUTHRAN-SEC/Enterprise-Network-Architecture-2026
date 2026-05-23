# CORE2 All Configuration Commands used in the Project

#### CORE2 : Secondary Layer 3 Core Switch
#### Role       : Standby HSRP Gateway | Inter-VLAN Routing | Redundancy
#### Platform   : Cisco 3560-24PS Multilayer Switch
#### Project    : Enterprise Network Architecture 2026
Note       : CORE2 mirrors CORE1 with lower HSRP priority.
              Takes over all VLANs automatically if CORE1 fails.

### Changed the hostname
```
hostname CORE2
```
### IP Routing (Enables Layer 3 Switching) 
```
ip routing
```

### VLAN Database
```
vlan 10
name HR
vlan 20
name FINANCE
vlan 30
name IT
vlan 40
name SOC
vlan 50
name DEVELOPERS
vlan 80
name GUEST
vlan 90
name WIRELESS
vlan 100
name SERVER-FARM
vlan 110
name DMZ
vlan 120
name MANAGEMENT
```

### Switch Virtual Interfaces (SVIs) - Internel VLAN Routing + HSRP 
- CORE2 is the STANDBY gateway for all VLANs (priority 100)
- If CORE1 goes down, CORE2 automatically becomes Active for all VLANs
- HSRP virtual IPs remain the same as CORE1 - end devices notice no change
```
interface Vlan10
description GATEWAY-HR-STANDBY
ip address 10.10.10.3 255.255.255.0
standby 10 ip 10.10.10.1
standby 10 priority 100
ip access-group HR-ACL in
no shutdown

interface Vlan20
description GATEWAY-FINANCE-STANDBY
ip address 10.10.20.3 255.255.255.0
standby 20 ip 10.10.20.1
standby 20 priority 100
no shutdown

interface Vlan30
description GATEWAY-IT-STANDBY
ip address 10.10.30.3 255.255.255.0
standby 30 ip 10.10.30.1
standby 30 priority 100
no shutdown

interface Vlan40
description GATEWAY-SOC-STANDBY
ip address 10.10.40.3 255.255.255.0
standby 40 ip 10.10.40.1
standby 40 priority 100
ip access-group SOC-ACL in
no shutdown

interface Vlan50
description GATEWAY-DEVELOPERS-STANDBY
ip address 10.10.50.3 255.255.255.0
standby 50 ip 10.10.50.1
standby 50 priority 100
no shutdown

interface Vlan80
description GATEWAY-GUEST-STANDBY
ip address 10.10.80.3 255.255.255.0
standby 80 ip 10.10.80.1
standby 80 priority 100
ip access-group GUEST-ACL in
no shutdown

interface Vlan90
description GATEWAY-WIRELESS-STANDBY
ip address 10.10.90.3 255.255.255.0
standby 90 ip 10.10.90.1
standby 90 priority 100
ip access-group WIRELESS-ACL in
no shutdown

interface Vlan100
description GATEWAY-SERVER-FARM-STANDBY
ip address 10.10.100.3 255.255.255.0
standby 100 ip 10.10.100.1
standby 100 priority 100
no shutdown

interface Vlan110
description GATEWAY-DMZ-STANDBY
ip address 10.10.110.3 255.255.255.0
standby 110 ip 10.10.110.1
standby 110 priority 100
no shutdown

interface Vlan120
description GATEWAY-MANAGEMENT-STANDBY
ip address 10.10.120.3 255.255.255.0
standby 120 ip 10.10.120.1
standby 120 priority 100
no shutdown
```

### Uplink to ASA Firewall (Routed Port) 
```
interface GigabitEthernet1/0/24
description UPLINK-TO-ASA-INSIDE-SECONDARY
no switchport
ip address 10.10.0.3 255.255.255.0
no shutdown
```

### Trunk Links to Access Switches
```
interface GigabitEthernet1/0/1
description TRUNK-TO-ACC1
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown

interface GigabitEthernet1/0/2
description TRUNK-TO-ACC2
switchport mode trunk
switchport trunk allowed vlan 30,40,50
no shutdown

interface GigabitEthernet1/0/3
description TRUNK-TO-ACC3
switchport mode trunk
switchport trunk allowed vlan 80,90,100,110,120
no shutdown
```

### Inter-Core Trunk Link to CORE1
```
interface GigabitEthernet1/0/23
description INTER-CORE-TRUNK-TO-CORE1
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40,50,80,90,100,110,120
no shutdown
```

### Static Default Route to ASA Firewall
```
ip route 0.0.0.0 0.0.0.0 10.10.0.1
```
### Extended ACLs (Identical to CORE1 - Required for HSRP Failover) 
```
ip access-list extended GUEST-ACL
```

#### GUEST VLAN - Internet Only, No Internal Access
```
deny   ip 10.10.80.0 0.0.0.255 10.10.0.0 0.0.255.255
permit ip 10.10.80.0 0.0.0.255 any
ip access-list extended WIRELESS-ACL
```

#### WIRELESS VLAN - Block Access to Management VLAN 
```
deny   ip 10.10.90.0 0.0.0.255 10.10.120.0 0.0.0.255
permit ip 10.10.90.0 0.0.0.255 any
ip access-list extended HR-ACL
```

#### HR VLAN - Blocked from Finance VLAN 
```
deny   ip 10.10.10.0 0.0.0.255 10.10.20.0 0.0.0.255
permit ip 10.10.10.0 0.0.0.255 any
ip access-list extended SOC-ACL
```
#### SOC VLAN - Allowed to Server Farm including SIEM
```
permit ip 10.10.40.0 0.0.0.255 10.10.100.0 0.0.0.255
permit ip 10.10.40.0 0.0.0.255 any
```

### Syslog to SIEM Server
```
logging host 10.10.100.14
logging trap informational
service timestamps log datetime msec
```
### SNMP Monitoring
```
snmp-server community MONITOR ro
snmp-server host 10.10.100.14 version 2c MONITOR
snmp-server enable traps
```

### NTP Time Synchronization 
```
ntp server 10.10.100.16
```

### AAA Authentication
```
aaa new-model
aaa authentication login default group radius local
radius-server host 10.10.120.10 key EnterpriseKey2026
```
### SSH Management (Telnet Disabled)
```
ip domain-name company.local
crypto key generate rsa modulus 2048
ip ssh version 2
username admin privilege 15 secret EnterpriseAdmin2026

line vty 0 4
transport input ssh
login local
exec-timeout 10 0

line con 0
exec-timeout 5 0
login local
```
### Make sure to Save all configuration
```
end
write memory
```
**Thats all for the CORE2 config settings**
