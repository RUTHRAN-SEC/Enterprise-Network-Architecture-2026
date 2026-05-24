# ACC3 All Configuration Commands used in the Project

#### ACC3 : Access Layer Switch 3
#### Role       : Layer 2 Access Switch - Servers, DMZ, Wireless and Management
#### VLANs      : VLAN 80 (Guest) | VLAN 90 (Wireless) | VLAN 100 (Server Farm) | VLAN 110 (DMZ) | VLAN 120 (Management)
#### Platform   : Cisco 2960-24TT
#### Project    : Enterprise Network Architecture 2026

### Changed the hostname
```
hostname ACC3
```

### VLAN Database
```
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

### Server Farm Ports - VLAN 100

#### Active Directory Server
```
interface FastEthernet0/1
description SERVER-ACTIVE-DIRECTORY
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### Internal DNS Server
```
interface FastEthernet0/2
description SERVER-INTERNAL-DNS
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### DHCP Server
```
interface FastEthernet0/3
description SERVER-DHCP
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### File Server
```
interface FastEthernet0/4
description SERVER-FILE
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### SIEM Server
```
interface FastEthernet0/5
description SERVER-SIEM
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### Backup Server
```
interface FastEthernet0/6
description SERVER-BACKUP
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### NTP Server
```
interface FastEthernet0/7
description SERVER-NTP
switchport mode access
switchport access vlan 100
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### DMZ Server Ports - VLAN 110

#### Public Web Server
```
interface FastEthernet0/8
description SERVER-DMZ-WEB
switchport mode access
switchport access vlan 110
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### Public DNS Server
```
interface FastEthernet0/9
description SERVER-DMZ-DNS
switchport mode access
switchport access vlan 110
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### Mail Server
```
interface FastEthernet0/10
description SERVER-DMZ-MAIL
switchport mode access
switchport access vlan 110
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Management Zone Ports - VLAN 120

#### Admin PC
```
interface FastEthernet0/11
description MGMT-ADMIN-PC
switchport mode access
switchport access vlan 120
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### Monitoring PC
```
interface FastEthernet0/12
description MGMT-MONITOR-PC
switchport mode access
switchport access vlan 120
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

#### AAA Server
```
interface FastEthernet0/13
description MGMT-AAA-SERVER
switchport mode access
switchport access vlan 120
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Wireless and Guest Ports

#### Corporate Wireless AP
```
interface FastEthernet0/14
description WIRELESS-AP-CORPORATE
switchport mode access
switchport access vlan 90
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
```

#### Guest Wireless AP
```
interface FastEthernet0/15
description WIRELESS-GUEST-AP
switchport mode access
switchport access vlan 80
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
```

### Unused Ports - Shutdown
```
interface range FastEthernet0/16 - 24
description UNUSED-PORT-DISABLED
switchport mode access
switchport access vlan 999
shutdown
```

### Trunk Uplinks to Core Switches

#### Trunk to CORE1
```
interface GigabitEthernet0/1
description TRUNK-UPLINK-TO-CORE1
switchport mode trunk
switchport trunk allowed vlan 80,90,100,110,120
no shutdown
```

#### Trunk to CORE2
```
interface GigabitEthernet0/2
description TRUNK-UPLINK-TO-CORE2
switchport mode trunk
switchport trunk allowed vlan 80,90,100,110,120
no shutdown
```

### Layer 2 Security

#### DHCP Snooping - Prevents Rogue DHCP Servers
```
ip dhcp snooping
ip dhcp snooping vlan 80,90,100,110,120
no ip dhcp snooping information option

interface GigabitEthernet0/1
ip dhcp snooping trust

interface GigabitEthernet0/2
ip dhcp snooping trust
```

#### Dynamic ARP Inspection - Prevents ARP Spoofing and MITM Attacks
```
ip arp inspection vlan 80,90,100,110,120

interface GigabitEthernet0/1
ip arp inspection trust

interface GigabitEthernet0/2
ip arp inspection trust
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
```

### NTP
```
ntp server 10.10.100.16
```

### SSH Management
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

**Thats all for the ACC3 config settings**
