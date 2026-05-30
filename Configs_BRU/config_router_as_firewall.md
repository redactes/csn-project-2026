enable
conf t
no logging console

hostname FW_BRU

no ip domain-lookup
ip routing

interface Loopback0
description ROUTER_ID_FW_BRU
ip address 10.102.4.1 255.255.255.255

interface GigabitEthernet0/0/0
description TO_CS1_BRU
ip address 10.102.3.5 255.255.255.252
no shutdown

interface GigabitEthernet0/0/1
description TO_CS2_BRU
ip address 10.102.3.9 255.255.255.252
no shutdown

interface Serial0/1/0
description TO_WAN
ip address 10.102.3.1 255.255.255.252
clock rate 2000000
no shutdown

interface GigabitEthernet0/0/2
shutdown

interface Serial0/1/1
shutdown

interface Serial0/2/0
shutdown

interface Serial0/2/1
shutdown

router ospf 1
router-id 10.102.4.1
log-adjacency-changes
passive-interface default
no passive-interface GigabitEthernet0/0/0
no passive-interface GigabitEthernet0/0/1
network 10.102.3.4 0.0.0.3 area 0
network 10.102.3.8 0.0.0.3 area 0
network 10.102.4.1 0.0.0.0 area 0
default-information originate

router bgp 65300
bgp log-neighbor-changes
no synchronization
neighbor 10.102.3.2 remote-as 65200
neighbor 10.102.3.2 description R_WAN
neighbor 10.102.3.2 password Cisco
network 10.102.3.0 mask 255.255.255.252
redistribute ospf 1

ip route 0.0.0.0 0.0.0.0 10.102.3.2

line con 0
logging synchronous

line vty 0 4
password class
login
transport input telnet

end
write memory