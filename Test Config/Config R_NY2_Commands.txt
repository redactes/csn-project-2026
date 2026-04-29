hostname R_NY2

! Basic Settings
no ip domain-lookup
ip cef

! Router-on-a-Stick (Trunk to SW_NY2)
interface GigabitEthernet0/0/0
description Trunk-to-SW_NY2
no ip address
no shutdown

interface GigabitEthernet0/0/0.10
description Gateway-VLAN10-Data
encapsulation dot1Q 10
ip address 10.101.3.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.20
description Gateway-VLAN20-Wireless
encapsulation dot1Q 20
ip address 10.101.4.1 255.255.255.0
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.100
description Gateway-VLAN100-Management
encapsulation dot1Q 100
ip address 10.101.5.67 255.255.255.192
ip helper-address 10.102.2.10

interface GigabitEthernet0/0/0.200
description Gateway-VLAN200-Server
encapsulation dot1Q 200
ip address 10.101.5.1 255.255.255.192
ip helper-address 10.102.2.10

! P2P Link to R_NY1
interface GigabitEthernet0/0/1
description P2P-to-R_NY1
ip address 10.101.6.2 255.255.255.252
no shutdown

! Serial Link to Firewall
interface Serial0/1/0
description TO_FW_NY
ip address 10.101.6.9 255.255.255.252
clock rate 64000
no shutdown

! Shutdown unused interfaces
interface GigabitEthernet0/0/2
shutdown

interface Serial0/1/1
shutdown

! OSPF Routing
router ospf 1
router-id 2.2.2.2
log-adjacency-changes
network 10.101.0.0 0.0.255.255 area 0
network 10.101.6.0 0.0.0.15 area 0

! Default Route
ip route 0.0.0.0 0.0.0.0 10.101.6.1

! Console
line con 0
logging synchronous

! VTY Access
line vty 0 4
password cisco
login
transport input telnet

end
write memory
