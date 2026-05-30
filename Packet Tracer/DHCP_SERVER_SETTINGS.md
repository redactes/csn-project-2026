# DHCP Server settings in Packet Tracer

Place the DHCP server in BRU VLAN 200 / SERVERS.

## Static IP on server

Desktop > IP Configuration:

```text
IP Address:      10.102.2.10
Subnet Mask:     255.255.255.192
Default Gateway: 10.102.2.62
DNS Server:      8.8.8.8
```

Services > DHCP: **On**

## DHCP pools

DHCP pools are per subnet, not per VLAN name. This is why NY has separate A/B pools.

### BRU_USERS

```text
Default Gateway: 10.102.0.254
DNS Server:      8.8.8.8
Start IP:        10.102.0.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### BRU_WIRELESS

```text
Default Gateway: 10.102.1.254
DNS Server:      8.8.8.8
Start IP:        10.102.1.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### BRU_MANAGEMENT

```text
Default Gateway: 10.102.2.126
DNS Server:      8.8.8.8
Start IP:        10.102.2.70
Subnet Mask:     255.255.255.192
Maximum Users:   50
```

### BRU_SERVERS

```text
Default Gateway: 10.102.2.62
DNS Server:      8.8.8.8
Start IP:        10.102.2.20
Subnet Mask:     255.255.255.192
Maximum Users:   40
```

### NY_A_USERS

```text
Default Gateway: 10.101.0.1
DNS Server:      8.8.8.8
Start IP:        10.101.0.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### NY_A_WIRELESS

```text
Default Gateway: 10.101.1.1
DNS Server:      8.8.8.8
Start IP:        10.101.1.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### NY_A_MANAGEMENT

```text
Default Gateway: 10.101.2.65
DNS Server:      8.8.8.8
Start IP:        10.101.2.70
Subnet Mask:     255.255.255.192
Maximum Users:   50
```

### NY_A_SERVERS

```text
Default Gateway: 10.101.2.1
DNS Server:      8.8.8.8
Start IP:        10.101.2.10
Subnet Mask:     255.255.255.192
Maximum Users:   40
```

### NY_B_USERS

```text
Default Gateway: 10.101.3.1
DNS Server:      8.8.8.8
Start IP:        10.101.3.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### NY_B_WIRELESS

```text
Default Gateway: 10.101.4.1
DNS Server:      8.8.8.8
Start IP:        10.101.4.10
Subnet Mask:     255.255.255.0
Maximum Users:   200
```

### NY_B_MANAGEMENT

```text
Default Gateway: 10.101.5.65
DNS Server:      8.8.8.8
Start IP:        10.101.5.70
Subnet Mask:     255.255.255.192
Maximum Users:   50
```

### NY_B_SERVERS

```text
Default Gateway: 10.101.5.1
DNS Server:      8.8.8.8
Start IP:        10.101.5.10
Subnet Mask:     255.255.255.192
Maximum Users:   40
```
