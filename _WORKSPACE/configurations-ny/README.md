# Routerconfiguratie – R1 (New York)

Dit bestand documenteert de configuratie van **R1 – New York**.  
Alle belangrijke instellingen zijn voorzien van uitleg en structuur.

---

## 📌 Basisinstellingen

    enable
    conf t
    hostname R1               ! Toekennen van hostname voor herkenbaarheid
    no ip domain-lookup       ! Voorkomt dat verkeerd getypte commando’s DNS-lookups triggeren

---

## 🔐 Beveiliging & Authenticatie

    enable secret cisco            ! Versleuteld enable-wachtwoord
    username admin secret cisco    ! Lokale admin-gebruiker

---

## 🔑 SSH Configuratie

    ip domain-name safeus.local    ! Nodig voor RSA-sleutelgeneratie
    crypto key generate rsa        ! Aanmaken RSA-sleutels
    1024                           ! Sleutelgrootte (1024 bits)
    ip ssh version 2               ! Gebruik SSHv2 voor beveiligde toegang

---

## 🖥️ Toegangslijnen (Console & VTY)

    line console 0
     password cisco                ! Console-wachtwoord
     login                         ! Verplicht inloggen via console

    line vty 0 4
     login local                   ! Login via lokale gebruikersdatabase
     transport input ssh           ! Alleen SSH toegestaan (geen Telnet)

---

## 🌐 Interface Configuratie

### 🔹 WAN-interface naar R2

    interface g0/0/1
     ip address 10.101.2.225 255.255.255.252   ! /30 WAN-link naar R2
     no shutdown

### 🔹 Fysieke LAN-interface

    interface g0/0/0
     no shutdown

### 🔸 VLAN 10 – Netwerk 10.101.0.0/24

    interface g0/0/0.10
     encapsulation dot1Q 10
     ip address 10.101.0.254 255.255.255.0

### 🔸 VLAN 20 – Netwerk 10.101.1.0/24

    interface g0/0/0.20
     encapsulation dot1Q 20
     ip address 10.101.1.254 255.255.255.0

### 🔸 VLAN 100 – Netwerk 10.101.2.64/26

    interface g0/0/0.100
     encapsulation dot1Q 100
     ip address 10.101.2.126 255.255.255.192

### 🔸 VLAN 200 – Netwerk 10.101.2.0/26

    interface g0/0/0.200
     encapsulation dot1Q 200
     ip address 10.101.2.62 255.255.255.192

---

## 🛣️ Statische Routes

### 🔹 Routes naar R2-netwerken

    ip route 10.101.10.0 255.255.255.0 10.101.2.226
    ip route 10.101.11.0 255.255.255.0 10.101.2.226

### 🔹 Default route (naar R2)

    ip route 0.0.0.0 0.0.0.0 10.101.2.226   ! Default gateway voor alle onbekende bestemmingen

---

## ✅ Einde configuratie

Dit document biedt een gestructureerd overzicht van alle ingestelde onderdelen op router **R1 – New York**, geschikt voor GitHub-documentatie.