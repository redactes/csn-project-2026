enable
conf t
no logging console
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

! Provider phase: use BGP between firewalls and provider.
! Remove OSPF on provider-facing links to avoid mixing OSPF and BGP for the same WAN transit.
no router ospf 1

router bgp 65200
bgp log-neighbor-changes
no synchronization
neighbor 10.101.6.13 remote-as 65100
neighbor 10.101.6.13 description FW_NY
neighbor 10.101.6.13 password Cisco
neighbor 10.102.3.1 remote-as 65300
neighbor 10.102.3.1 description FW_BRU
neighbor 10.102.3.1 password Cisco
network 10.101.6.12 mask 255.255.255.252
network 10.102.3.0 mask 255.255.255.252

line con 0
logging synchronous

ip domain-name csn.local
username admin privilege 15 secret class
crypto key generate rsa modulus 2048
ip ssh version 2

line vty 0 4
login local
transport input ssh

end
write memory
