# CSN 2026 Network Lab

This repository documents the Packet Tracer implementation of our CSN 2026 network lab. The goal is to simulate a multi-site enterprise network with a New York site, a Brussels site, a WAN/provider layer, dynamic routing, firewall segmentation, centralized DHCP, DHCP relay, BGP routing, and site-to-site IPsec VPN.

![Printscreen of Packet Tracer](/Configs_PT/packet_tracer.png)

## Project Goal

The original logical design used Palo Alto firewalls at both sites. Because Packet Tracer does not provide Palo Alto firewalls, and because the available ASA firewall does not support BGP in our Packet Tracer environment, each Palo Alto firewall was replaced by two devices:

```text
LAN/Core <--> ASA Firewall <--> Cisco BGP Router <--> WAN Provider Router
```

This splits the Palo Alto functionality into two parts:

| Device | Role |
|---|---|
| ASA Firewall | Firewall policies, routed inside/outside segmentation, site-to-site IPsec VPN |
| Cisco Router | BGP peering with the WAN/provider router |
| WAN Router | Simulated provider network between New York and Brussels |

## Current Topology Overview

### New York Site

```text
SWITCH-A-NY --- ROUTER-A-NY ----\
                                ASA-NY ---- BGP_ROUTER_NY ---- R_WAN
SWITCH-B-NY --- ROUTER-B-NY ----/
```

NY uses router-on-a-stick on `ROUTER-A-NY` and `ROUTER-B-NY` for VLAN routing.

### Brussels Site

```text
ACCESS SWITCHES ---- CORE-SWITCH-A-BRU ----\
                                             ASA-BRU ---- BGP_ROUTER_BRU ---- R_WAN
ACCESS SWITCHES ---- CORE-SWITCH-B-BRU ----/
```

BRU uses multilayer core switches with HSRP for the VLAN gateways.

## VLANs and Subnets

| VLAN | Name | NY Subnets | BRU Subnets |
|---:|---|---|---|
| 10 | USERS | `10.101.0.0/24`, `10.101.3.0/24` | `10.102.0.0/24` |
| 20 | WIRELESS | `10.101.1.0/24`, `10.101.4.0/24` | `10.102.1.0/24` |
| 100 | MANAGEMENT | `10.101.2.64/26`, `10.101.5.64/26` | `10.102.2.64/26` |
| 200 | SERVERS | `10.101.2.0/26`, `10.101.5.0/26` | `10.102.2.0/26` |

## Important Transit Links

### NY

| Link | Subnet | Devices |
|---|---|---|
| ROUTER-A-NY ↔ ASA-NY | `10.101.6.4/30` | `10.101.6.5` ↔ `10.101.6.6` |
| ROUTER-B-NY ↔ ASA-NY | `10.101.6.8/30` | `10.101.6.9` ↔ `10.101.6.10` |
| ASA-NY ↔ BGP_ROUTER_NY | `10.101.6.16/30` | `10.101.6.17` ↔ `10.101.6.18` |
| BGP_ROUTER_NY ↔ R_WAN | `10.101.6.12/30` | `10.101.6.13` ↔ `10.101.6.14` |

### BRU

| Link | Subnet | Devices |
|---|---|---|
| ASA-BRU ↔ CORE-SWITCH-A-BRU | `10.102.3.4/30` | `10.102.3.5` ↔ `10.102.3.6` |
| ASA-BRU ↔ CORE-SWITCH-B-BRU | `10.102.3.8/30` | `10.102.3.9` ↔ `10.102.3.10` |
| ASA-BRU ↔ BGP_ROUTER_BRU | `10.102.3.12/30` | `10.102.3.13` ↔ `10.102.3.14` |
| BGP_ROUTER_BRU ↔ R_WAN | `10.102.3.0/30` | `10.102.3.1` ↔ `10.102.3.2` |

## Routing Design

### Inside Each Site

OSPF is used internally between the local routing layer and the ASA firewalls:

```text
NY routers <--> ASA-NY
BRU core switches <--> ASA-BRU
```

### WAN / Provider Layer

BGP is used between the site BGP routers and the WAN provider router:

```text
BGP_ROUTER_NY <--> R_WAN <--> BGP_ROUTER_BRU
```

BGP AS numbers:

| Device | AS Number |
|---|---:|
| BGP_ROUTER_NY | 65100 |
| R_WAN | 65200 |
| BGP_ROUTER_BRU | 65300 |

The ASA-to-BGP-router transit networks were also advertised through BGP so the ASA outside interfaces can reach each other for IPsec:

```text
ASA-NY outside:  10.101.6.17
ASA-BRU outside: 10.102.3.13
```

## Firewall Design

Packet Tracer ASA does not support BGP in this lab, so the ASA devices are used for firewalling, routing between inside/outside segments, and IPsec.

Current ASA roles:

| ASA | Inside Interfaces | Outside Interface |
|---|---|---|
| ASA-NY | Links to `ROUTER-A-NY` and `ROUTER-B-NY` | Link to `BGP_ROUTER_NY` |
| ASA-BRU | Links to `CORE-SWITCH-A-BRU` and `CORE-SWITCH-B-BRU` | Link to `BGP_ROUTER_BRU` |

We initially used temporary `permit ip any any` outside ACLs for troubleshooting. These were replaced with more specific inter-site ACLs:

- ASA-NY permits BRU LAN subnets to NY LAN subnets.
- ASA-BRU permits NY LAN subnets to BRU LAN subnets.
- ICMP is still broadly permitted for troubleshooting.

## DHCP Design

The DHCP server is located in the BRU server VLAN:

```text
DHCP Server:     10.102.2.10/26
Default Gateway: 10.102.2.62
```

All relevant VLAN interfaces use `ip helper-address 10.102.2.10` so DHCP requests can be relayed to the central DHCP server.

Because DHCP pools are selected based on subnet, not VLAN name, separate DHCP pools are required for each subnet. For example, NY has separate USERS pools for `10.101.0.0/24` and `10.101.3.0/24`, while BRU has its own USERS pool for `10.102.0.0/24`.

## IPsec VPN Design

A policy-based IKEv1 site-to-site IPsec tunnel is configured between the ASA outside interfaces:

```text
ASA-NY outside 10.101.6.17  === IPsec VPN ===  ASA-BRU outside 10.102.3.13
```

The VPN crypto ACL defines the interesting traffic between NY LAN subnets and BRU LAN subnets.

IKE/IPsec parameters used:

| Setting | Value |
|---|---|
| IKE version | IKEv1 |
| Authentication | Pre-shared key |
| Pre-shared key | `Cisco123` |
| Encryption | AES |
| Hash | SHA |
| DH Group | 2 |
| IPsec transform-set | `esp-aes esp-sha-hmac` |
| Tunnel type | L2L / site-to-site |

## What We Achieved

The following items are working:

- VLAN configuration on access and core switches.
- Inter-VLAN routing in NY using router-on-a-stick.
- Inter-VLAN routing in BRU using multilayer core switches and HSRP.
- OSPF between LAN routing devices and ASA firewalls.
- BGP between both site BGP routers and the WAN router.
- End-to-end routing between NY and BRU.
- Central DHCP server in BRU serving both BRU and NY clients.
- DHCP relay from NY to BRU via `ip helper-address`.
- ASA outside ACLs no longer use broad `permit ip any any` rules.
- NY and BRU hosts can successfully ping each other.
- ASA outside interfaces can reach each other.
- Site-to-site IPsec VPN between ASA-NY and ASA-BRU is established.
- IPsec encrypted/decrypted packet counters increase during host-to-host traffic.

## Verification Commands Used

### Basic Interface and Routing Checks

Used on routers, multilayer switches and BGP routers:

```cisco
show ip interface brief
show ip route
show running-config | include ip route
ping <destination-ip>
```

Used on ASA firewalls:

```cisco
show interface ip brief
show route
show arp
ping <destination-ip>
```

### BGP Verification

Used on `BGP_ROUTER_NY`, `R_WAN`, and `BGP_ROUTER_BRU`:

```cisco
show ip bgp summary
show ip bgp
show ip route bgp
show ip route <remote-subnet-or-host>
ping <bgp-neighbor-ip>
```

Successful BGP indicators:

```text
State/PfxRcd shows a prefix count instead of Idle/Active/Connect
Routes from the remote site appear in the routing table
BGP routers can ping across the WAN/provider layer
```

Example checks:

```cisco
BGP_ROUTER_NY# show ip bgp summary
BGP_ROUTER_NY# show ip route 10.102.2.10
BGP_ROUTER_NY# ping 10.102.3.2
```

### DHCP Verification

Used on Packet Tracer PCs:

```text
ipconfig /all
ipconfig /renew
ping 10.102.2.10
```

Used on routers/core switches to verify relay reachability:

```cisco
show ip route 10.102.2.10
ping 10.102.2.10
```

Successful DHCP indicators:

```text
NY PC receives an address from the correct NY pool
BRU PC receives an address from the correct BRU pool
DHCP Server is shown as 10.102.2.10
Default gateway matches the VLAN gateway
```

Example NY client result:

```text
IPv4 Address:    10.101.0.11
Subnet Mask:     255.255.255.0
Default Gateway: 10.101.0.1
DHCP Server:     10.102.2.10
DNS Server:      8.8.8.8
```

Example BRU client result:

```text
IPv4 Address:    10.102.0.10
Subnet Mask:     255.255.255.0
Default Gateway: 10.102.0.254
DHCP Server:     10.102.2.10
DNS Server:      8.8.8.8
```

### ASA ACL Verification

Used on both ASA firewalls:

```cisco
show access-list
show running-config
```

Successful ACL indicators:

```text
No `permit ip any any` remains in OUTSIDE_IN
OUTSIDE_IN contains only specific inter-site subnet rules plus ICMP for troubleshooting
ACL hit counters increase when matching traffic passes
```

### IPsec Verification

Used on both ASA firewalls:

```cisco
show crypto isakmp sa
show crypto ipsec sa
show access-list
```

Successful IKEv1 indicator:

```text
State: QM_IDLE
```

Successful IPsec indicators:

```text
#pkts encaps increases
#pkts encrypt increases
#pkts decaps increases
#pkts decrypt increases
```

Example successful ASA-NY state:

```text
IKE Peer: 10.102.3.13
Type    : L2L
Role    : Initiator
State   : QM_IDLE
```

Example successful ASA-BRU state:

```text
IKE Peer: 10.101.6.17
Type    : L2L
Role    : responder
State   : QM_IDLE
```

Example successful IPsec counters:

```text
#pkts encaps: 4
#pkts encrypt: 4
#pkts decaps: 6
#pkts decrypt: 6
```

## Validation Tests Performed

### DHCP Tests

| Test | Result |
|---|---|
| BRU PC receives DHCP address from `10.102.2.10` | Success |
| NY PC receives DHCP address from `10.102.2.10` via DHCP relay | Success |
| NY PC DHCP renew after ASA ACL hardening | Success |
| NY PC DHCP renew after IPsec configuration | Success |

### Connectivity Tests

| Source | Destination | Result |
|---|---|---|
| NY PC | DHCP Server `10.102.2.10` | Success |
| NY PC | BRU PC `10.102.0.10` | Success |
| NY PC | BRU HSRP Gateway `10.102.0.254` | Success |
| BRU PC | DHCP Server `10.102.2.10` | Success |
| BRU PC | NY PC `10.101.0.11` | Success |
| BRU PC | NY Router Gateway `10.101.3.1` | Success |
| ASA-NY | ASA-BRU outside `10.102.3.13` | Success |
| ASA-BRU | ASA-NY outside `10.101.6.17` | Success |

### Routing and BGP Tests

| Test | Result |
|---|---|
| BGP_NY neighbor with R_WAN established | Success |
| BGP_BRU neighbor with R_WAN established | Success |
| R_WAN receives routes from both sites | Success |
| BGP_NY can reach BRU WAN side | Success |
| NY routers can route to BRU DHCP server after ASA/BGP routing fixes | Success |
| ASA outside transit networks advertised through BGP | Success |

### IPsec Tests

| Test | Result |
|---|---|
| ASA outside interfaces can ping each other | Success |
| IKEv1 L2L tunnel reaches `QM_IDLE` | Success |
| `show crypto ipsec sa` shows active SAs | Success |
| IPsec encaps/encrypt counters increase | Success |
| IPsec decaps/decrypt counters increase | Success |
| NY ↔ BRU host traffic continues working through IPsec | Success |

## Important Issues We Fixed

### ASA-BRU Interface Mismatch

ASA-BRU initially could not reach the BRU core switches. The issue was caused by an interface/cabling mismatch. In the Packet Tracer topology, the left core switch was connected to a different ASA interface than expected.

The ASA-BRU interface configuration was adjusted to match the actual Packet Tracer cabling. After this, DHCP relay from NY to the BRU DHCP server started working.

### Missing ASA Outside Transit Reachability

Before IPsec could work, the ASA outside interfaces had to reach each other:

```text
ASA-NY outside:  10.101.6.17
ASA-BRU outside: 10.102.3.13
```

The transit networks were added to BGP:

```cisco
network 10.101.6.16 mask 255.255.255.252
network 10.102.3.12 mask 255.255.255.252
```

After that, the ASA outside-to-outside pings succeeded and IPsec could be configured.

### Packet Tracer Command Differences

Some commands from real Cisco IOS or ASA were not supported in Packet Tracer. We adjusted accordingly:

| Original Command | Packet Tracer Fix |
|---|---|
| `crypto key generate rsa modulus 2048` | `crypto key generate rsa general-keys modulus 2048` |
| `object-group network ...` on ASA | Replaced with explicit ACL entries |
| `clear configure access-list ...` | Replaced by removing specific ACL entries |
| `neighbor password` in BGP | Removed because Packet Tracer BGP did not support it |
| `switchport trunk encapsulation dot1q` | Removed where dot1q is implicit |
| `show crypto ikev1 sa` | Used `show crypto isakmp sa` |
| Filtered ASA commands like `show running-config crypto map` | Used full `show running-config` |

## Current Status

The network is operational.

```text
NY LAN <--> ASA-NY <--> BGP_ROUTER_NY <--> R_WAN <--> BGP_ROUTER_BRU <--> ASA-BRU <--> BRU LAN
```

Current status summary:

| Component | Status |
|---|---|
| VLANs | Working |
| Inter-VLAN routing | Working |
| HSRP in BRU | Working |
| OSPF inside each site | Working |
| BGP across WAN/provider | Working |
| Central DHCP server | Working |
| DHCP relay from NY to BRU | Working |
| ASA ACL hardening | Working |
| Site-to-site IPsec VPN | Working |
| Encrypted inter-site traffic | Working |

## Next Improvements

- Make more secure
