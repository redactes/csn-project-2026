hostname ASA-NY

same-security-traffic permit inter-interface

interface GigabitEthernet1/1
description TO_ROUTER_A_NY
nameif inside_r1
security-level 100
ip address 10.101.6.6 255.255.255.252
no shutdown

interface GigabitEthernet1/2
description TO-ROUTER-B-NY
nameif inside_r2
security-level 100
ip address 10.101.6.10 255.255.255.252
no shutdown

interface GigabitEthernet1/3
description TO_BGP_ROUTER_NY
nameif outside
security-level 0
ip address 10.101.6.17 255.255.255.252
no shutdown

route outside 0.0.0.0 0.0.0.0 10.101.6.18 1

router ospf 1
router-id 10.101.7.3
network 10.101.6.4 255.255.255.252 area 0
network 10.101.6.8 255.255.255.252 area 0
default-information originate

access-list OUTSIDE_IN extended permit icmp any any
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.3.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.4.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.0.0 255.255.255.0 10.101.5.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.3.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.4.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.1.0 255.255.255.0 10.101.5.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.0.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.1.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.2.0 255.255.255.128
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.3.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.4.0 255.255.255.0
access-list OUTSIDE_IN extended permit ip 10.102.2.0 255.255.255.128 10.101.5.0 255.255.255.128
access-group OUTSIDE_IN in interface outside

write memory