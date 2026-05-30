# CSN 2026 Network Lab

This repository documents the Packet Tracer implementation of our CSN 2026 network lab. The goal is to simulate a multi-site enterprise network with a New York site, a Brussels site, a WAN/provider layer, dynamic routing, firewall segmentation, DHCP relay, and eventually site-to-site VPN/IPsec.

![Printscreen of Packet Tracer](/packet_tracer.png)

## Project Goal

The original design used Palo Alto firewalls at both sites. Because Packet Tracer does not provide a Palo Alto firewall and the available ASA firewall does not support BGP in our environment, we replaced each Palo Alto firewall with two devices:

```text
LAN/Core <--> ASA Firewall <--> Cisco BGP Router <--> WAN Provider Router
```

This allows us to split the Palo Alto functionality into two parts:

| Device | Role |
|---|---|
| ASA Firewall | Firewall policies, inside/outside segmentation, later IPsec VPN |
| Cisco Router | BGP peering with the WAN/provider router |
| WAN Router | Simulated provider network between NY and BRU |

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

## Firewall Design

Because Packet Tracer ASA does not support BGP in our lab, the ASA devices are used only for firewalling and routing between inside and outside zones.

Current ASA roles:

| ASA | Inside Interfaces | Outside Interface |
|---|---|---|
| ASA-NY | Links to `ROUTER-A-NY` and `ROUTER-B-NY` | Link to `BGP_ROUTER_NY` |
| ASA-BRU | Links to `CORE-SWITCH-A-BRU` and `CORE-SWITCH-B-BRU` | Link to `BGP_ROUTER_BRU` |

We initially used temporary `permit ip any any` outside ACLs for troubleshooting. These were later replaced with more specific inter-site ACLs:

- ASA-NY permits BRU LAN subnets to NY LAN subnets.
- ASA-BRU permits NY LAN subnets to BRU LAN subnets.
- ICMP is still broadly permitted for troubleshooting.

## DHCP Design

The DHCP server is located in the BRU server VLAN:

```text
DHCP Server: 10.102.2.10/26
Default Gateway: 10.102.2.62
```

All relevant VLAN interfaces use `ip helper-address 10.102.2.10` so DHCP requests can be relayed to the central DHCP server.

Because DHCP pools are selected based on subnet, not VLAN name, separate DHCP pools are required for each subnet. For example, NY has separate USERS pools for `10.101.0.0/24` and `10.101.3.0/24`, while BRU has its own USERS pool for `10.102.0.0/24`.

## What We Have Achieved

The following items are currently working:

- VLAN configuration on access and core switches.
- Inter-VLAN routing in NY using router-on-a-stick.
- Inter-VLAN routing in BRU using multilayer core switches and HSRP.
- OSPF between LAN routing devices and ASA firewalls.
- BGP between both site BGP routers and the WAN router.
- End-to-end routing between NY and BRU.
- Central DHCP server in BRU serving both BRU and NY clients.
- DHCP relay from NY to BRU via `ip helper-address`.
- ASA firewalls no longer use broad `permit ip any any` rules on the outside interface.
- NY and BRU hosts can successfully ping each other.

## Validation Tests Performed

### DHCP Tests

| Test | Result |
|---|---|
| BRU PC receives DHCP address from `10.102.2.10` | Success |
| NY PC receives DHCP address from `10.102.2.10` via DHCP relay | Success |
| NY PC DHCP renew after ASA ACL hardening | Success |

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

### Connectivity Tests

| Source | Destination | Result |
|---|---|---|
| NY PC | DHCP Server `10.102.2.10` | Success |
| NY PC | BRU PC `10.102.0.10` | Success |
| NY PC | BRU HSRP Gateway `10.102.0.254` | Success |
| BRU PC | DHCP Server `10.102.2.10` | Success |
| BRU PC | NY PC `10.101.0.11` | Success |
| BRU PC | NY Router Gateway `10.101.3.1` | Success |

### Routing Tests

| Test | Result |
|---|---|
| BGP_NY neighbor with R_WAN established | Success |
| BGP_BRU neighbor with R_WAN established | Success |
| BGP_NY can reach BRU WAN side | Success |
| NY routers can route to BRU DHCP server after ASA/BGP routing fixes | Success |

## Important Issues We Fixed

### ASA-BRU Interface Mismatch

ASA-BRU initially could not reach the BRU core switches. The issue was caused by an interface/cabling mismatch:

```text
CORE-SWITCH-A-BRU was connected to ASA-BRU Gi1/2
CORE-SWITCH-B-BRU was connected to ASA-BRU Gi1/1
```

The ASA configuration was adjusted to match the actual Packet Tracer cabling. After this, DHCP relay from NY to the BRU DHCP server started working.

### Packet Tracer Command Differences

Some commands from real Cisco IOS or ASA were not supported in Packet Tracer. We adjusted accordingly:

| Original Command | Packet Tracer Fix |
|---|---|
| `crypto key generate rsa modulus 2048` | `crypto key generate rsa general-keys modulus 2048` |
| `object-group network ...` on ASA | Replaced with explicit ACL entries |
| `clear configure access-list ...` | Replaced by removing specific ACL entries |
| `neighbor password` in BGP | Removed because Packet Tracer BGP did not support it |
| `switchport trunk encapsulation dot1q` | Removed where dot1q is implicit |

## Still To Do

The main remaining goal is to configure IPsec site-to-site VPN between the two ASA firewalls.

Target design:

```text
ASA-NY outside 10.101.6.17  === IPsec VPN ===  ASA-BRU outside 10.102.3.13
```

Before configuring IPsec, we need to confirm both ASA outside interfaces can reach each other:

```text
ASA-NY  -> ping 10.102.3.13
ASA-BRU -> ping 10.101.6.17
```

After that, we can build a policy-based IPsec VPN with interesting traffic between the NY and BRU LAN subnets.

## Current Status

The network is currently operational for inter-site routing and DHCP. The ASA firewalls are acting as routed firewalls with restricted outside ACLs, while BGP is handled by dedicated Cisco routers due to Packet Tracer ASA limitations.

Next milestone: configure and validate IPsec site-to-site VPN between ASA-NY and ASA-BRU.
