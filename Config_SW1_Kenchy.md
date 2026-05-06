enable
configure terminal
hostname NY-SW1

vlan 10
name DATA

vlan 20
name WIRELESS

vlan 100
name MGMT

vlan 200
name SERVERS

interface gigabitEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
no shutdown

interface fa0/1
switchport mode access
switchport access vlan 10
no shutdown

line con 0
logging synchronous

line vty 0 4
password cisco
login
transport input telnet

end
write memory