# R1 – New York Router Configuration

## Overview

| Setting | Value |
|---|---|
| **Hostname** | R1 |
| **Location** | New York |
| **Domain Name** | safeus.local |
| **SSH Version** | 2 |
| **RSA Key Size** | 1024 bits |

---

## Basic Setup

```
hostname R1
no ip domain-lookup
enable secret cisco
username admin secret cisco
```

- `no ip domain-lookup` — Prevents the router from trying to DNS-resolve mistyped commands.
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

## Interfaces

### G0/0/1 — WAN Link to R2

```
interface g0/0/1
ip address 10.101.2.225 255.255.255.252
no shutdown
```

| | Value |
|---|---|
| **IP Address** | 10.101.2.225 |
| **Subnet Mask** | 255.255.255.252 (/30) |
| **Purpose** | Point-to-point WAN link toward R2 |

---

### G0/0/0 — Trunk Interface (Router-on-a-Stick)

```
interface g0/0/0
no shutdown
```

The physical interface is brought up with no IP address. Sub-interfaces handle inter-VLAN routing.

#### Sub-interface: VLAN 10

```
interface g0/0/0.10
encapsulation dot1Q 10
ip address 10.101.0.254 255.255.255.0
```

| | Value |
|---|---|
| **VLAN** | 10 |
| **Gateway IP** | 10.101.0.254 |
| **Subnet** | 10.101.0.0/24 |

#### Sub-interface: VLAN 20

```
interface g0/0/0.20
encapsulation dot1Q 20
ip address 10.101.1.254 255.255.255.0
```

| | Value |
|---|---|
| **VLAN** | 20 |
| **Gateway IP** | 10.101.1.254 |
| **Subnet** | 10.101.1.0/24 |

#### Sub-interface: VLAN 100

```
interface g0/0/0.100
encapsulation dot1Q 100
ip address 10.101.2.126 255.255.255.192
```

| | Value |
|---|---|
| **VLAN** | 100 |
| **Gateway IP** | 10.101.2.126 |
| **Subnet** | 10.101.2.64/26 |

#### Sub-interface: VLAN 200

```
interface g0/0/0.200
encapsulation dot1Q 200
ip address 10.101.2.62 255.255.255.192
```

| | Value |
|---|---|
| **VLAN** | 200 |
| **Gateway IP** | 10.101.2.62 |
| **Subnet** | 10.101.2.0/26 |

---

## Static Routes

```
! Routes to R2 networks
ip route 10.101.10.0 255.255.255.0 10.101.2.226
ip route 10.101.11.0 255.255.255.0 10.101.2.226

! Default route
ip route 0.0.0.0 0.0.0.0 10.101.2.226
```

| Destination | Mask | Next Hop | Purpose |
|---|---|---|---|
| 10.101.10.0 | /24 | 10.101.2.226 | Route to R2 network 1 |
| 10.101.11.0 | /24 | 10.101.2.226 | Route to R2 network 2 |
| 0.0.0.0 | /0 | 10.101.2.226 | Default route (internet / unknown traffic) |

- The next hop `10.101.2.226` is R2's IP on the WAN link.

---

## Full Running Configuration
<details><summary>Expand and copy me</summary>

    hostname R1
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

    interface g0/0/1
    ip address 10.101.2.225 255.255.255.252
    no shutdown

    interface g0/0/0
    no shutdown

    interface g0/0/0.10
    encapsulation dot1Q 10
    ip address 10.101.0.254 255.255.255.0

    interface g0/0/0.20
    encapsulation dot1Q 20
    ip address 10.101.1.254 255.255.255.0

    interface g0/0/0.100
    encapsulation dot1Q 100
    ip address 10.101.2.126 255.255.255.192

    interface g0/0/0.200
    encapsulation dot1Q 200
    ip address 10.101.2.62 255.255.255.192

    ip route 10.101.10.0 255.255.255.0 10.101.2.226
    ip route 10.101.11.0 255.255.255.0 10.101.2.226
    ip route 0.0.0.0 0.0.0.0 10.101.2.226
    
</details>