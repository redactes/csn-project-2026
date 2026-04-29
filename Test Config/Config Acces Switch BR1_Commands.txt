hostname ACCESS-SW1

! Basic Settings
no ip domain-lookup

! Spanning Tree
spanning-tree mode pvst
spanning-tree extend system-id

! VLANs
vlan 10
name USERS
vlan 20
name WIRELESS
vlan 100
name MANAGEMENT
vlan 200
name SERVERS

! Access Ports

interface FastEthernet0/1
description USER-PC
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

! Trunk Uplinks (to CORE1 / CORE2)

interface GigabitEthernet0/1
description Uplink-to-CORE1
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface GigabitEthernet0/2
description Uplink-to-CORE2
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

! Disable VLAN 1 SVI
interface Vlan1
no ip address
shutdown

! Console
line con 0
logging synchronous

! VTY
line vty 0 4
login
line vty 5 15
login

end
write memory
