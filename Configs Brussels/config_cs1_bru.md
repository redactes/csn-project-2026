enable
conf t

hostname CS1-BRU

no ip domain-lookup
ip routing

spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,100,200 priority 24576

vlan 10
name USERS
vlan 20
name WIRELESS
vlan 100
name MANAGEMENT
vlan 200
name SERVERS

interface Loopback0
ip address 10.102.4.2 255.255.255.255

interface GigabitEthernet1/0/1
description TO_FW_BRU
no switchport
ip address 10.102.3.6 255.255.255.252
no shutdown

interface Port-channel1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200

interface GigabitEthernet1/0/2
description Etherchannel
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
channel-group 1 mode active

interface GigabitEthernet1/0/3
description Etherchannel
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
channel-group 1 mode active

interface GigabitEthernet1/0/4
description TO_ACCESS_SWITCH
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
no shutdown

interface GigabitEthernet1/0/5
description TO_ACCESS_SWITCH
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
no shutdown

interface Vlan10
ip address 10.102.0.1 255.255.255.0
ip helper-address 10.102.2.10
standby 10 ip 10.102.0.254
standby 10 priority 110
standby 10 preempt

interface Vlan20
ip address 10.102.1.1 255.255.255.0
ip helper-address 10.102.2.10
standby 20 ip 10.102.1.254
standby 20 priority 110
standby 20 preempt

interface Vlan100
ip address 10.102.2.65 255.255.255.192
ip helper-address 10.102.2.10
standby 100 ip 10.102.2.126
standby 100 priority 110
standby 100 preempt

interface Vlan200
ip address 10.102.2.1 255.255.255.192
ip helper-address 10.102.2.10
standby 200 ip 10.102.2.62
standby 200 priority 110
standby 200 preempt

router ospf 1
default-information originate
router-id 6.6.6.6
log-adjacency-changes
passive-interface default
no passive-interface GigabitEthernet1/0/1
network 10.102.3.4 0.0.0.3 area 0
network 10.102.4.2 0.0.0.0 area 0
network 10.102.0.0 0.0.255.255 area 0

ip route 0.0.0.0 0.0.0.0 10.102.3.5

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
