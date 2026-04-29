enable
conf t

hostname FW_NY

no ip domain-lookup
ip cef

interface Serial0/1/0
description TO_R_NY1
ip address 10.101.6.6 255.255.255.252
no shutdown

interface Serial0/1/1
description TO_R_NY2
ip address 10.101.6.10 255.255.255.252
no shutdown

interface Serial0/2/0
description TO_PROVIDER
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
router-id 3.3.3.3
log-adjacency-changes
network 10.101.6.0 0.0.0.15 area 0

ip route 0.0.0.0 0.0.0.0 10.101.6.14

line con 0
logging synchronous


line vty 0 4
login

end
write memory
