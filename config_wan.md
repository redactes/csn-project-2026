enable
conf t

hostname R_WAN

no ip domain-lookup

interface GigabitEthernet0/0/0
description TO_FW_NY
ip address 10.101.6.14 255.255.255.252
no shutdown

interface GigabitEthernet0/0/1 
description TO_FW_BRU
ip address 10.102.3.2 255.255.255.252
no shutdown


interface Serial0/1/0
shutdown
interface Serial0/1/1
shutdown

router ospf 1
router-id 4.4.4.4
log-adjacency-changes
network 10.101.6.12 0.0.0.3 area 0
network 10.102.3.0 0.0.0.3 area 0

line con 0
logging synchronous

line vty 0 4
login

end
write memory
