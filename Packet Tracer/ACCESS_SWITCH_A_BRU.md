enable
conf t

hostname SWITCH-A-BRU

no ip domain-lookup

spanning-tree mode pvst

vlan 10
name USERS
vlan 20
name WIRELESS
vlan 100
name MANAGEMENT
vlan 200
name SERVERS

ip domain-name csn.local
username admin privilege 15 secret class
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

interface Vlan100
description MANAGEMENT
ip address 10.102.2.67 255.255.255.192
no shutdown

ip default-gateway 10.102.2.126

interface FastEthernet0/1
description VLAN_USERS
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable

interface FastEthernet 0/2
description DHCP-SERVER
switchport mode access
switchport access vlan 200
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown

interface GigabitEthernet0/1
description TO_CS1_BRU
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface GigabitEthernet0/2
description TO_CS2_BRU
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface Vlan1
no ip address
shutdown

line con 0
logging synchronous

line vty 0 4
login local
transport input ssh

end
write memory
