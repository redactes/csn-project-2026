enable
conf t

hostname AS1-BRU

no ip domain-lookup

spanning-tree mode pvst
spanning-tree extend system-id

vlan 10
name USERS
vlan 20
name WIRELESS
vlan 100
name MANAGEMENT
vlan 200
name SERVERS

interface FastEthernet0/1
description TO_USER_PC
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable

interface FastEthernet0/2
description SERVER
switchport mode access
switchport access vlan 200
spanning-tree portfast
spanning-tree bpduguard enable

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
password class
login
line vty 5 15
password class
login

end
write memory
