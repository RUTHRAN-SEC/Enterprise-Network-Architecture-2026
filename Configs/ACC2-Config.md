# ACC2 All Configuration Commands used in the Project

#### ACC2 : Access Layer Switch 2
#### Role       : Layer 2 Access Switch - IT, SOC and Developers
#### VLANs      : VLAN 30 (IT) | VLAN 40 (SOC) | VLAN 50 (Developers)
#### Platform   : Cisco 2960-24TT
#### Project    : Enterprise Network Architecture 2026

### Changed the hostname
```
hostname ACC2
```

### VLAN Database
```
vlan 30
name IT

vlan 40
name SOC

vlan 50
name DEVELOPERS
```

### Access Port: IT-PC (FastEthernet0/1)
```
interface FastEthernet0/1
description ACCESS-IT-PC
switchport mode access
switchport access vlan 30
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Access Port: SOC-PC (FastEthernet0/2)
```
interface FastEthernet0/2
description ACCESS-SOC-PC
switchport mode access
switchport access vlan 40
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Access Port: Developer-PC (FastEthernet0/3)
```
interface FastEthernet0/3
description ACCESS-DEVELOPER-PC
switchport mode access
switchport access vlan 50
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Unused Ports - Shutdown for Security
```
interface range FastEthernet0/4 - 22
description UNUSED-PORT-DISABLED
switchport mode access
switchport access vlan 999
shutdown
```

### Trunk Uplink to CORE1
```
interface GigabitEthernet0/1
description TRUNK-UPLINK-TO-CORE1
switchport mode trunk
switchport trunk allowed vlan 30,40,50
no shutdown
```

### Trunk Uplink to CORE2 (Redundant Path)
```
interface GigabitEthernet0/2
description TRUNK-UPLINK-TO-CORE2
switchport mode trunk
switchport trunk allowed vlan 30,40,50
no shutdown
```

### Layer 2 Security

**DHCP Snooping - Prevents rogue DHCP servers on untrusted ports**
```
ip dhcp snooping
ip dhcp snooping vlan 30,40,50
no ip dhcp snooping information option

interface GigabitEthernet0/1
ip dhcp snooping trust

interface GigabitEthernet0/2
ip dhcp snooping trust
```

**Dynamic ARP Inspection - Prevents ARP spoofing and MITM attacks**
```
ip arp inspection vlan 30,40,50

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

**Thats all for the ACC2 config settings**
