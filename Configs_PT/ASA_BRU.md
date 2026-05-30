hostname ASA-BRU

same-security-traffic permit inter-interface

interface GigabitEthernet1/1
description TO_CORE_SWITCH_A_BRU
nameif inside_cs1
security-level 100
ip address 10.102.3.5 255.255.255.252
no shutdown

interface GigabitEthernet1/2
description TO_CORE_SWITCH_B_BRU
nameif inside_cs2
security-level 100
ip address 10.102.3.9 255.255.255.252
no shutdown

interface GigabitEthernet1/3
description TO_BGP_BRU
nameif outside
security-level 0
ip address 10.102.3.13 255.255.255.252
no shutdown

route outside 0.0.0.0 0.0.0.0 10.102.3.14 1

router ospf 1
router-id 10.102.4.1
network 10.102.3.4 255.255.255.252 area 0
network 10.102.3.8 255.255.255.252 area 0
default-information originate

access-list OUTSIDE_IN extended permit ip 10.101.0.0 255.255.255.0 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.0.0 255.255.255.0 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.0.0 255.255.255.0 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.101.1.0 255.255.255.0 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.1.0 255.255.255.0 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.1.0 255.255.255.0 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.101.2.0 255.255.255.128 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.2.0 255.255.255.128 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.2.0 255.255.255.128 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.101.3.0 255.255.255.0 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.3.0 255.255.255.0 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.3.0 255.255.255.0 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.101.4.0 255.255.255.0 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.4.0 255.255.255.0 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.4.0 255.255.255.0 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.101.5.0 255.255.255.128 10.102.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.5.0 255.255.255.128 10.102.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.101.5.0 255.255.255.128 10.102.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit icmp any any
access-group OUTSIDE_IN in interface outside

write memory