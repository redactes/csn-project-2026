enable
conf t
hostname ACCESS-SW2

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
description USER-PC
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable

interface GigabitEthernet0/1
description Uplink-to-CORE1
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface GigabitEthernet0/2
description Uplink-to-CORE2
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface Vlan1
no ip address
shutdown

line con 0
logging synchronous

line vty 0 4
login
line vty 5 15
login

end
write memory