enable
conf t
no logging console

hostname BGP-ROUTER-NY

no ip domain-lookup
ip routing

interface GigabitEthernet0/0/0
description TO_ASA_NY
ip address 10.101.6.18 255.255.255.252
no shutdown

interface GigabitEthernet0/0/1
description TO_R_WAN
ip address 10.101.6.13 255.255.255.252
no shutdown

ip route 10.101.0.0 255.255.255.0 10.101.6.17
ip route 10.101.1.0 255.255.255.0 10.101.6.17
ip route 10.101.2.0 255.255.255.128 10.101.6.17
ip route 10.101.3.0 255.255.255.0 10.101.6.17
ip route 10.101.4.0 255.255.255.0 10.101.6.17
ip route 10.101.5.0 255.255.255.128 10.101.6.17
ip route 10.101.6.4 255.255.255.252 10.101.6.17
ip route 10.101.6.8 255.255.255.252 10.101.6.17

ip route 0.0.0.0 0.0.0.0 10.101.6.14

router bgp 65100
bgp router-id 10.101.7.3
bgp log-neighbor-changes
no synchronization
neighbor 10.101.6.14 remote-as 65200
network 10.101.0.0 mask 255.255.255.0
network 10.101.1.0 mask 255.255.255.0
network 10.101.2.0 mask 255.255.255.128
network 10.101.3.0 mask 255.255.255.0
network 10.101.4.0 mask 255.255.255.0
network 10.101.5.0 mask 255.255.255.128
network 10.101.6.4 mask 255.255.255.252
network 10.101.6.8 mask 255.255.255.252
network 10.101.6.12 mask 255.255.255.252

line con 0
logging synchronous

ip domain-name csn.local
username admin privilege 15 secret class
crypto key generate rsa general-keys modulus 2048
ip ssh version 2

line vty 0 4
login local
transport input ssh

end
write memory