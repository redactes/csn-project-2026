enable
conf t
no logging console

hostname BGP-ROUTER-BRU

no ip domain-lookup
ip routing

interface GigabitEthernet0/0/0
description TO_ASA_BRU
ip address 10.102.3.14 255.255.255.252
no shutdown

interface GigabitEthernet0/0/1
description TO_R_WAN
ip address 10.102.3.1 255.255.255.252
no shutdown

ip route 10.102.0.0 255.255.255.0 10.102.3.13
ip route 10.102.1.0 255.255.255.0 10.102.3.13
ip route 10.102.2.0 255.255.255.128 10.102.3.13
ip route 10.102.3.4 255.255.255.252 10.102.3.13
ip route 10.102.3.8 255.255.255.252 10.102.3.13
ip route 10.102.4.1 255.255.255.255 10.102.3.13
ip route 10.102.4.2 255.255.255.255 10.102.3.13
ip route 10.102.4.3 255.255.255.255 10.102.3.13

ip route 0.0.0.0 0.0.0.0 10.102.3.2

router bgp 65300
bgp router-id 10.102.4.1
bgp log-neighbor-changes
no synchronization
neighbor 10.102.3.2 remote-as 65200
network 10.102.0.0 mask 255.255.255.0
network 10.102.1.0 mask 255.255.255.0
network 10.102.2.0 mask 255.255.255.128
network 10.102.3.0 mask 255.255.255.252
network 10.102.3.4 mask 255.255.255.252
network 10.102.3.8 mask 255.255.255.252
network 10.102.4.1 mask 255.255.255.255
network 10.102.4.2 mask 255.255.255.255
network 10.102.4.3 mask 255.255.255.255

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