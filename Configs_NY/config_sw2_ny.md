enable
conf t

hostname SW2_NY

no ip domain-lookup

spanning-tree mode pvst
spanning-tree extend system-id

vlan 10
name USERS
vlan 20
name WIRELESS
vlan 100
name MANAGEMENT
vlan 200
name SERVERS

interface FastEthernet0/1
description TO_PC2_NY
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown

interface GigabitEthernet0/1
description TO_R2_NY
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
no shutdown

interface Vlan100
description MANAGEMENT
ip address 10.101.5.66 255.255.255.192
no shutdown

ip default-gateway 10.101.5.65

line con 0
logging synchronous

ip domain-name csn.local
username admin privilege 15 secret cisco
crypto key generate rsa modulus 2048
ip ssh version 2

line vty 0 4
login local
transport input ssh

end
write memory
