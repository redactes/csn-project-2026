# S1 – New York Switch Configuration

## Overview

| Setting | Value |
|---|---|
| **Hostname** | S1 |
| **Location** | New York |
| **Domain Name** | safeus.local |
| **SSH Version** | 2 |
| **RSA Key Size** | 1024 bits |
| **Management VLAN** | 100 |
| **Default Gateway** | 10.101.2.126 (R1 VLAN 100) |

---

## Basic Setup

```
hostname S1
no ip domain-lookup
enable secret cisco
username admin secret cisco
```

- `no ip domain-lookup` — Prevents the switch from trying to DNS-resolve mistyped commands.
- `enable secret cisco` — Sets the encrypted privileged EXEC password.
- `username admin secret cisco` — Creates a local user account for SSH login.

---

## SSH Configuration

```
ip domain-name safeus.local
crypto key generate rsa 1024
ip ssh version 2
```

- RSA keys are required to enable SSH.
- SSHv2 is more secure than SSHv1 and is the recommended standard.

---

## Line Configuration

### Console Line

```
line console 0
password cisco
login
```

### VTY Lines (Remote Access)

```
line vty 0 4
login local
transport input ssh
```

- `login local` — Uses the local username/password database (admin/cisco).
- `transport input ssh` — Restricts remote access to SSH only (no Telnet).

---

## VLANs

```
vlan 10
name DATA
vlan 20
name WIRELESS
vlan 100
name MGMT
vlan 200
name SERVERS
```

| VLAN ID | Name | Purpose |
|---|---|---|
| 10 | DATA | Wired end-user devices |
| 20 | WIRELESS | Wireless clients |
| 100 | MGMT | Switch & network device management |
| 200 | SERVERS | Server segment |

---

## Interfaces

### Fa0/1 — Access Port (PC1)

```
interface fa0/1
switchport mode access
switchport access vlan 10
spanning-tree portfast
```

| | Value |
|---|---|
| **Mode** | Access |
| **VLAN** | 10 (DATA) |
| **PortFast** | Enabled |

- `spanning-tree portfast` — Skips STP listening/learning states for faster connectivity on end-device ports.

---

### G0/1 — Trunk to R1

```
interface g0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,100,200
```

| | Value |
|---|---|
| **Mode** | Trunk |
| **Allowed VLANs** | 10, 20, 100, 200 |
| **Connected to** | R1 G0/0/0 |

- Carries all VLANs to R1 for inter-VLAN routing (Router-on-a-Stick).

---

### VLAN 100 — Management Interface

```
interface vlan 100
ip address 10.101.2.65 255.255.255.192
no shutdown
```

| | Value |
|---|---|
| **IP Address** | 10.101.2.65 |
| **Subnet Mask** | 255.255.255.192 (/26) |
| **Subnet** | 10.101.2.64/26 |

---

## Default Gateway

```
ip default-gateway 10.101.2.126
```

- Points to R1's VLAN 100 sub-interface (`10.101.2.126`) for management traffic routing.

---

## Full Running Configuration

<details><summary>Full Running Configuration</summary>

    hostname S1
    no ip domain-lookup

    enable secret cisco
    username admin secret cisco

    ip domain-name safeus.local
    crypto key generate rsa
    1024
    ip ssh version 2

    line console 0
    password cisco
    login

    line vty 0 4
    login local
    transport input ssh

    vlan 10
    name DATA
    vlan 20
    name WIRELESS
    vlan 100
    name MGMT
    vlan 200
    name SERVERS

    interface fa0/1
    switchport mode access
    switchport access vlan 10
    spanning-tree portfast

    interface g0/1
    switchport mode trunk
    switchport trunk allowed vlan 10,20,100,200

    interface vlan 100
    ip address 10.101.2.65 255.255.255.192
    no shutdown

    ip default-gateway 10.101.2.126

</details>