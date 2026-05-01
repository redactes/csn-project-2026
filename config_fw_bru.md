enable
conf t

hostname FW_BRU

no ip domain-lookup
ip cef

interface Loopback0
ip address 10.102.4.1 255.255.255.255

interface GigabitEthernet0/0/0
description TO_CORE1
ip address 10.102.3.5 255.255.255.252
no shutdown

description TO_CORE2
ip address 10.102.3.9 255.255.255.252
no shutdown

interface Serial0/1/0
description TO_PROVIDER
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
router-id 5.5.5.5
log-adjacency-changes
network 10.102.3.0 0.0.0.15 area 0
network 10.102.4.1 0.0.0.0 area 0
default-information originate

ip route 10.102.0.0 255.255.255.0 10.102.3.10
ip route 10.102.1.0 255.255.255.0 10.102.3.10
ip route 10.102.2.0 255.255.255.192 10.102.3.10
ip route 10.102.2.64 255.255.255.192 10.102.3.10

ip route 0.0.0.0 0.0.0.0 10.102.3.2

line con 0
logging synchronous

line vty 0 4
login

end
write memory
