# ACC1 All Configuration Commands used in the Project

#### ACC1 : Access Layer Switch 1
#### Role       : Layer 2 Access Switch - HR, Finance and IT
#### VLANs      : VLAN 10 (HR) | VLAN 20 (Finance) | VLAN 30 (IT)
#### Platform   : Cisco 2960-24TT
#### Project    : Enterprise Network Architecture 2026

### Changed the hostname
```
hostname ACC1
```

### VLAN Database
```
vlan 10
name HR

vlan 20
name FINANCE

vlan 30
name IT
```

### Access Port: HR-PC (FastEthernet0/1)
```
interface FastEthernet0/1
description ACCESS-HR-PC
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Access Port: Finance-PC (FastEthernet0/2)
```
interface FastEthernet0/2
description ACCESS-FINANCE-PC
switchport mode access
switchport access vlan 20
spanning-tree portfast
spanning-tree bpduguard enable
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
no shutdown
```

### Access Port: IT-PC (FastEthernet0/3)
```
interface FastEthernet0/3
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

### Unused Access Ports - Shutdown for Security
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
switchport trunk allowed vlan 10,20,30
no shutdown
```

### Trunk Uplink to CORE2 (Redundant Path)
```
interface GigabitEthernet0/2
description TRUNK-UPLINK-TO-CORE2
switchport mode trunk
switchport trunk allowed vlan 10,20,30
no shutdown
```

### Layer 2 Security

**DHCP Snooping - Prevents rogue DHCP servers on untrusted ports**
```
ip dhcp snooping
ip dhcp snooping vlan 10,20,30
no ip dhcp snooping information option

interface GigabitEthernet0/1
ip dhcp snooping trust

interface GigabitEthernet0/2
ip dhcp snooping trust
```

**Dynamic ARP Inspection - Prevents ARP spoofing and MITM attacks**
```
ip arp inspection vlan 10,20,30

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

**Thats all for the ACC1 config settings**
