enable
Conf t
hostname SW_NY1

! Disable DNS lookup delays
no ip domain-lookup

spanning-tree mode pvst
spanning-tree extend system-id

vlan 10
name USERS
vlan 100
name MANAGEMENT

interface FastEthernet0/1
description PC_NY1
switchport mode access
switchport access vlan 10
spanning-tree portfast

interface GigabitEthernet0/1
description Trunk-to-R_NY1
switchport mode trunk


interface Vlan100
description Management
ip address 10.101.2.65 255.255.255.192
no shutdown


ip default-gateway 10.101.2.66


line con 0
logging synchronous


line vty 0 4
password cisco
login
transport input telnet
line vty 5 15
login

end
write memory