enable
conf t
no logging console

hostname FW_NY

no ip domain-lookup
ip routing

interface Loopback0
description ROUTER_ID_FW_NY
ip address 10.101.7.3 255.255.255.255

interface Serial0/1/0
description TO_R_NY1
ip address 10.101.6.6 255.255.255.252
no shutdown

interface Serial0/1/1
description TO_R_NY2
ip address 10.101.6.10 255.255.255.252
no shutdown

interface Serial0/2/0
description TO_WAN
ip address 10.101.6.13 255.255.255.252
no shutdown

interface GigabitEthernet0/0/0
shutdown

interface GigabitEthernet0/0/1
shutdown

interface GigabitEthernet0/0/2
shutdown

interface Serial0/2/1
shutdown

router ospf 1
router-id 10.101.7.3
log-adjacency-changes
passive-interface default
no passive-interface Serial0/1/0
no passive-interface Serial0/1/1
network 10.101.6.4 0.0.0.3 area 0
network 10.101.6.8 0.0.0.3 area 0
network 10.101.7.3 0.0.0.0 area 0
default-information originate

router bgp 65100
bgp log-neighbor-changes
no synchronization
neighbor 10.101.6.14 remote-as 65200
neighbor 10.101.6.14 description R_WAN
neighbor 10.101.6.14 password Cisco
network 10.101.6.12 mask 255.255.255.252
redistribute ospf 1

ip route 0.0.0.0 0.0.0.0 10.101.6.14

line con 0
logging synchronous

line vty 0 4
password class
login
transport input telnet

end
write memory