# CORE1 All Configuration Commands used in the Project

#### CORE1    : Primary Layer 3 Core Switch
#### Role     : Active HSRP Gateway | Inter-VLAN Routing | DHCP Server
#### Device   : Cisco 3560-24PS Multilayer Switch 
#### Project  : Enterprise Network Architecture 2026

### Changed the hostname
```
hostname CORE1
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

### DHCP Excluded Addresses 
#### Excludes gateway IPs and static server IPs from DHCP pools
```
ip dhcp excluded-address 10.10.10.1  10.10.10.10
ip dhcp excluded-address 10.10.20.1  10.10.20.10
ip dhcp excluded-address 10.10.30.1  10.10.30.10
ip dhcp excluded-address 10.10.40.1  10.10.40.10
ip dhcp excluded-address 10.10.50.1  10.10.50.10
ip dhcp excluded-address 10.10.80.1  10.10.80.10
ip dhcp excluded-address 10.10.90.1  10.10.90.10
ip dhcp excluded-address 10.10.100.1 10.10.100.20
ip dhcp excluded-address 10.10.110.1 10.10.110.20
ip dhcp excluded-address 10.10.120.1 10.10.120.20
```

### DHCP Pools (One Per VLAN)
```
ip dhcp pool HR-POOL
network 10.10.10.0 255.255.255.0
default-router 10.10.10.1
dns-server 10.10.100.11
lease 1

ip dhcp pool FINANCE-POOL
network 10.10.20.0 255.255.255.0
default-router 10.10.20.1
dns-server 10.10.100.11
lease 1

ip dhcp pool IT-POOL
network 10.10.30.0 255.255.255.0
default-router 10.10.30.1
dns-server 10.10.100.11
lease 1

ip dhcp pool SOC-POOL
network 10.10.40.0 255.255.255.0
default-router 10.10.40.1
dns-server 10.10.100.11
lease 1

ip dhcp pool DEVELOPERS-POOL
network 10.10.50.0 255.255.255.0
default-router 10.10.50.1
dns-server 10.10.100.11
lease 1

ip dhcp pool GUEST-POOL
network 10.10.80.0 255.255.255.0
default-router 10.10.80.1
dns-server 10.10.100.11
lease 1

ip dhcp pool WIRELESS-POOL
network 10.10.90.0 255.255.255.0
default-router 10.10.90.1
dns-server 10.10.100.11
lease 1
```
Note: SERVER-FARM, DMZ, and MANAGEMENT use static IPs only - no DHCP pools

### Switch Virtual Interfaces (SVIs) - Inter-VLAN Routing + HSRP 
**CORE1 is the ACTIVE gateway for all VLANs (priority 110)**
**HSRP virtual IPs (.1) are the default gateways used by all end devices**
```
interface Vlan10
description GATEWAY-HR
ip address 10.10.10.2 255.255.255.0
standby 10 ip 10.10.10.1
standby 10 priority 110
standby 10 preempt
ip access-group HR-ACL in
no shutdown

interface Vlan20
description GATEWAY-FINANCE
ip address 10.10.20.2 255.255.255.0
standby 20 ip 10.10.20.1
standby 20 priority 110
standby 20 preempt
no shutdown

interface Vlan30
description GATEWAY-IT
ip address 10.10.30.2 255.255.255.0
standby 30 ip 10.10.30.1
standby 30 priority 110
standby 30 preempt
no shutdown

interface Vlan40
description GATEWAY-SOC
ip address 10.10.40.2 255.255.255.0
standby 40 ip 10.10.40.1
standby 40 priority 110
standby 40 preempt
ip access-group SOC-ACL in
no shutdown

interface Vlan50
description GATEWAY-DEVELOPERS
ip address 10.10.50.2 255.255.255.0
standby 50 ip 10.10.50.1
standby 50 priority 110
standby 50 preempt
no shutdown

interface Vlan80
description GATEWAY-GUEST
ip address 10.10.80.2 255.255.255.0
standby 80 ip 10.10.80.1
standby 80 priority 110
standby 80 preempt
ip access-group GUEST-ACL in
no shutdown

interface Vlan90
description GATEWAY-WIRELESS
ip address 10.10.90.2 255.255.255.0
standby 90 ip 10.10.90.1
standby 90 priority 110
standby 90 preempt
ip access-group WIRELESS-ACL in
no shutdown

interface Vlan100
description GATEWAY-SERVER-FARM
ip address 10.10.100.2 255.255.255.0
standby 100 ip 10.10.100.1
standby 100 priority 110
standby 100 preempt
no shutdown

interface Vlan110
description GATEWAY-DMZ
ip address 10.10.110.2 255.255.255.0
standby 110 ip 10.10.110.1
standby 110 priority 110
standby 110 preempt
no shutdown

interface Vlan120
description GATEWAY-MANAGEMENT
ip address 10.10.120.2 255.255.255.0
standby 120 ip 10.10.120.1
standby 120 priority 110
standby 120 preempt
no shutdown
```
### Uplink to ASA Firewall (Routed Port) 
```
interface GigabitEthernet1/0/24
description UPLINK-TO-ASA-INSIDE
no switchport
ip address 10.10.0.2 255.255.255.0
no shutdown

### Trunk Links to Access Switches
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
### Inter-Core Trunk Link to CORE2 
```
interface GigabitEthernet1/0/23
description INTER-CORE-TRUNK-TO-CORE2
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40,50,80,90,100,110,120
no shutdown
```
### Static Default Route to ASA Firewall 
```
ip route 0.0.0.0 0.0.0.0 10.10.0.1
```
### Extended ACLs (Zero Trust Segmentation)
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

**Thats all for the CORE1 config settings**
