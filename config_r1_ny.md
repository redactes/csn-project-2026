enable
conf t

hostname R_NY1

no ip domain-lookup
ip cef

interface GigabitEthernet0/0/0
description Trunk-to-SW_NY1
no ip address
no shutdown

interface GigabitEthernet0/0/0.10
description Gateway-VLAN10-Data
encapsulation dot1Q 10
ip address 10.101.0.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.20
description Gateway-VLAN20-Wireless
encapsulation dot1Q 20
ip address 10.101.1.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.100
description Gateway-VLAN100-Management
encapsulation dot1Q 100
ip address 10.101.2.67 255.255.255.192
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.200
description Gateway-VLAN200-Server
encapsulation dot1Q 200
ip address 10.101.2.1 255.255.255.192
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/1
description P2P-to-R_NY2
ip address 10.101.6.1 255.255.255.252
no shutdown

interface Serial0/1/0
description TO_FW_NY
ip address 10.101.6.5 255.255.255.252
clock rate 64000
no shutdown

interface GigabitEthernet0/0/2
shutdown

interface Serial0/1/1
shutdown

router ospf 1
router-id 1.1.1.1
log-adjacency-changes
network 10.101.0.0 0.0.255.255 area 0
network 10.101.6.0 0.0.0.15 area 0

ip route 0.0.0.0 0.0.0.0 10.101.6.2

line con 0
logging synchronous

line vty 0 4
password cisco
login
transport input telnet

end
write memory
