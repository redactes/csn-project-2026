# Routerconfiguratie – R1 (New York)

## Inhoudstabel
1. [Routerconfiguratie – R1 (New York)](#routerconfiguratie-r1-(new-york))
2.
3.
4.


# Routerconfiguratie – R1 (New York)

## Inhoudstabel
1. [Basisinstellingen](#basisinstellingen)
2. [Beveiliging & Authenticatie](#beveiliging--authenticatie)
3. [SSH Configuratie](#ssh-configuratie)
4. [Toegangslijnen (Console & VTY)](#toegangslijnen-console--vty)
5. [Interface Configuratie](#interface-configuratie)
   - [WAN-interface naar R2](#wan-interface-naar-r2)
   - [Fysieke LAN-interface](#fysieke-lan-interface)
   - [VLAN 10 – Netwerk 10.101.0.0/24](#vlan-10--netwerk-10100-024)
   - [VLAN 20 – Netwerk 10.101.1.0/24](#vlan-20--netwerk-10101-024)
   - [VLAN 100 – Netwerk 10.101.2.64/26](#vlan-100--netwerk-10102-6426)
   - [VLAN 200 – Netwerk 10.101.2.0/26](#vlan-200--netwerk-10102-026)
6. [Statische Routes](#statische-routes)
   - [Routes naar R2-netwerken](#routes-naar-r2-netwerken)
   - [Default route (naar R2)](#default-route-naar-r2)
7. [Einde configuratie](#einde-configuratie)

---

## Basisinstellingen

    enable
    conf t
    hostname R1
    no ip domain-lookup

---

## Beveiliging & Authenticatie

    enable secret cisco
    username admin secret cisco

---

## SSH Configuratie

    ip domain-name safeus.local
    crypto key generate rsa
    1024
    ip ssh version 2

---

## Toegangslijnen (Console & VTY)

    line console 0
     password cisco
     login

    line vty 0 4
     login local
     transport input ssh

---

## Interface Configuratie

### WAN-interface naar R2

    interface g0/0/1
     ip address 10.101.2.225 255.255.255.252
     no shutdown

### Fysieke LAN-interface

    interface g0/0/0
     no shutdown

### VLAN 10 – Netwerk 10.101.0.0/24

    interface g0/0/0.10
     encapsulation dot1Q 10
     ip address 10.101.0.254 255.255.255.0

### VLAN 20 – Netwerk 10.101.1.0/24

    interface g0/0/0.20
     encapsulation dot1Q 20
     ip address 10.101.1.254 255.255.255.0

### VLAN 100 – Netwerk 10.101.2.64/26

    interface g0/0/0.100
     encapsulation dot1Q 100
     ip address 10.101.2.126 255.255.255.192

### VLAN 200 – Netwerk 10.101.2.0/26

    interface g0/0/0.200
     encapsulation dot1Q 200
     ip address 10.101.2.62 255.255.255.192

---

## Statische Routes

### Routes naar R2-netwerken

    ip route 10.101.10.0 255.255.255.0 10.101.2.226
    ip route 10.101.11.0 255.255.255.0 10.101.2.226

### Default route (naar R2)

    ip route 0.0.0.0 0.0.0.0 10.101.2.226

---

## Einde configuratie

Dit document biedt een gestructureerd overzicht van alle ingestelde onderdelen op router **R1 – New York**, geschikt voor GitHub-documentatie.
