enable
conf t
no logging console
hostname R2_NY

no ip domain-lookup

interface GigabitEthernet0/0/1
description TO_SW_NY2
no ip address
no shutdown

interface GigabitEthernet0/0/1.10
description GW_VLAN10_DATA
encapsulation dot1Q 10
ip address 10.101.3.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/1.20
description GW_VLAN20_WIRELESS
encapsulation dot1Q 20
ip address 10.101.4.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/1.100
description GW_VLAN100_MANAGEMENT
encapsulation dot1Q 100
ip address 10.101.5.67 255.255.255.192
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/1.200
description GW_VLAN200_SERVER
encapsulation dot1Q 200
ip address 10.101.5.1 255.255.255.192
ip helper-address 10.102.2.10

interface Serial0/1/1
description TO_R_NY1
ip address 10.101.6.2 255.255.255.252
no shutdown

interface GigabitEthernet0/0/0
description TO_FW_NY
ip address 10.101.6.9 255.255.255.252
no shutdown

interface Serial0/1/0
shutdown

router ospf 1
router-id 2.2.2.2
log-adjacency-changes
network 10.101.0.0 0.0.255.255 area 0
network 10.101.6.0 0.0.0.15 area 0

ip route 0.0.0.0 0.0.0.0 10.101.6.1

line con 0
logging synchronous

line vty 0 4
password cisco
login
transport input telnet

end
write memory
