# DHCP and intersite connectivity test summary

## Goal

Validate that the DHCP server in Brussels VLAN 200 can hand out addresses to Brussels and New York clients through `ip helper-address 10.102.2.10`, and then tighten the ASA outside ACLs so they no longer use `permit ip any any`.

## Key changes made

- DHCP server placed in BRU VLAN 200 with static IP `10.102.2.10/26` and default gateway `10.102.2.62`.
- BRU access switch DHCP server port changed from trunk to access VLAN 200.
- NY routers use DHCP relay with `ip helper-address 10.102.2.10` on VLAN subinterfaces.
- ROUTER-B-NY duplicate/wrong default route via `10.101.6.6` was removed; default route remains via `10.101.6.10`.
- BGP_ROUTER_NY received static routes back to ASA inside transit links `10.101.6.4/30` and `10.101.6.8/30` via `10.101.6.17`.
- ASA-BRU interface mapping was corrected to match actual Packet Tracer cabling.
- ASA-NY and ASA-BRU outside ACLs were changed from `permit ip any any` to subnet-specific intersite rules, with ICMP still allowed for troubleshooting.

## Tests performed

| Test | Source | Destination | Result |
|---|---|---:|---|
| DHCP request in BRU | BRU PC | DHCP server `10.102.2.10` | Success |
| DHCP request in NY | NY PC | DHCP server `10.102.2.10` via relay | Success |
| DHCP renew in NY | NY PC | DHCP server `10.102.2.10` | Success, retained `10.101.0.11/24` |
| Ping local BRU gateway | BRU PC | `10.102.0.254` | Success, 4/4 replies |
| Ping BRU DHCP server | BRU PC | `10.102.2.10` | Success, 4/4 replies |
| Ping NY gateway from BRU | BRU PC | `10.101.3.1` | Success, 4/4 replies after convergence |
| Ping NY PC from BRU | BRU PC | `10.101.0.11` | Success, 4/4 replies |
| Ping DHCP server from NY | NY PC | `10.102.2.10` | Success, 4/4 replies |
| Ping BRU PC from NY | NY PC | `10.102.0.10` | Success, 4/4 replies |
| Ping BRU HSRP gateway from NY | NY PC | `10.102.0.254` | Success after initial ARP/HSRP convergence |
| BGP_NY to WAN/BRU transit | BGP_ROUTER_NY | `10.102.3.2` | Success, 5/5 replies |
| BGP adjacency NY side | BGP_ROUTER_NY | Neighbor `10.101.6.14` | Established, prefixes received |
| BGP_BRU to ASA_BRU outside | BGP_ROUTER_BRU | `10.102.3.13` | Success, 5/5 replies |

## Final observed client leases

### NY PC

```text
IPv4 Address:     10.101.0.11
Subnet Mask:      255.255.255.0
Default Gateway:  10.101.0.1
DHCP Server:      10.102.2.10
DNS Server:       8.8.8.8
```

### BRU PC

```text
IPv4 Address:     10.102.0.10
Subnet Mask:      255.255.255.0
Default Gateway:  10.102.0.254
DHCP Server:      10.102.2.10
DNS Server:       8.8.8.8
```

## Conclusion

DHCP works for both Brussels and New York clients. Intersite routing between NY and BRU works in both directions. ASA-NY and ASA-BRU no longer have `permit ip any any` on the outside ACL; they now allow only the required intersite subnets plus ICMP for troubleshooting.
