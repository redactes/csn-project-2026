enable
conf t

hostname SW1_NY

no ip domain-lookup

spanning-tree mode pvst
spanning-tree extend system-id

vlan 10
name USERS
vlan 100
name MANAGEMENT

interface FastEthernet0/1
description TO_PC1_NY
switchport mode access
switchport access vlan 10
spanning-tree portfast

interface GigabitEthernet0/1
description TO_R1_NY
switchport mode trunk

<<<<<<< Updated upstream:config_sw1_ny.md

interface Vlan100
description MANAGEMENT
=======
interface vlan 10
description users
ip address 10.101.0.1 255.255.255.0
interface Vlan 100
description Management
>>>>>>> Stashed changes:Test Config/Config SW_NY1_Commands.txt
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