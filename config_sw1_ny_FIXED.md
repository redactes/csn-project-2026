enable
conf t

hostname SW1_NY

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
description TO_PC1_NY
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown

interface GigabitEthernet0/1
description TO_R1_NY
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
no shutdown

interface Vlan100
description MANAGEMENT
ip address 10.101.2.66 255.255.255.192
no shutdown

ip default-gateway 10.101.2.65

line con 0
logging synchronous

line vty 0 4
password cisco
login
transport input telnet
line vty 5 15
password cisco
login
transport input telnet

end
write memory
