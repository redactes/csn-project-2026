# Documentation

#### Final Report — CSN Network Project
This document is our full project report, walking through every step we took to design, build, and validate our network from start to finish. From initial setup to final testing, everything is documented here — making it the go-to reference for understanding how the project came together.

#### UAT Test Plan — CSN Network Project
This file contains the User Acceptance Testing (UAT) plan for the CSN network project, documenting all test cases across 7 categories including local connectivity, routing tables, and remote connectivity. Each test case tracks the criteria, expected results, test dates, and pass/fail outcomes — providing full traceability of the network validation process from simulation to final verification.

# Configurations

#### How to Use the Configs
1. Open **Cisco Packet Tracer** and load your topology file.
2. Click on the device you want to configure and open the **CLI** tab.
3. Open the corresponding configuration file from the list below.
4. Expand the **Full Running Configuration** section at the bottom of the file.
5. Copy the configuration and paste it into the Packet Tracer CLI.
  > **Note:** When prompted for the RSA key size, enter `1024`.

##### Device Configuration Files for NY Office
| Device | Type | Location |
|---|---|---|
| R1 – New York | Router | [r1-config-ny.md](configs-ny/r1-config-ny.md) |
| R2 – New York | Router | [r2-config-ny.md](configs-ny/r2-config-ny.md) |
| S1 – New York | Switch | [s1-config-ny.md](configs-ny/s1-config-ny.md) |
| S2 – New York | Switch | [s2-config-ny.md](configs-ny/s2-config-ny.md) |
| FW - New York | Firewall | |
| PC1 - New York | Workstation | |
| PC2 - New York | Workstation | |

##### Device Configuration Files for BRU Office
| Device | Type | Location |
|---|---|---|
| CS1 – New York | Router | |
| CS2 – New York | Router | |
| S3 – New York | Switch | |
| S4 – New York | Switch | |
| FW - Brussels| Firewall | |
| PC1 - Brussels | Workstation | |
| PC2 - Brussels | Workstation | |
| DHCP - Brussels | Server | | |
| Zabbix - Brussels | Server | |

# 🌐 IP Addressing
#### Network IP Plan: POD-1

##### New York (10.101.0.0/16)
| Subnet | Mask | First IP | Last IP | Hosts | Network | Broadcast | VLAN | Name |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Data | 255.255.255.0 | 10.101.0.1 | 10.101.0.254 | 254 | 10.101.0.0 | 10.101.0.255 | Vlan10 | Data |
| Wireless | 255.255.255.0 | 10.101.1.1 | 10.101.1.254 | 254 | 10.101.1.0 | 10.101.1.255 | Vlan20 | Wireless |
| Server | 255.255.255.192 | 10.101.2.1 | 10.101.2.62 | 62 | 10.101.2.0 | 10.101.2.63 | Vlan200 | Server |
| Management | 255.255.255.192 | 10.101.2.65 | 10.101.2.126 | 62 | 10.101.2.64 | 10.101.2.127 | Vlan100 | Management |
| Free vlan | 255.255.255.192 | 10.101.2.129 | 10.101.2.190 | 62 | 10.101.2.128 | 10.101.2.191 | - | - |
| Free vlan | 255.255.255.224 | 10.101.2.193 | 10.101.2.222 | 30 | 10.101.2.192 | 10.101.2.223 | - | - |
| P2P 1 | 255.255.255.252 | 10.101.2.225 | 10.101.2.226 | 2 | 10.101.2.224 | 10.101.2.227 | - | - |
| P2P 2 | 255.255.255.252 | 10.101.2.229 | 10.101.2.230 | 2 | 10.101.2.228 | 10.101.2.231 | - | - |
| P2P 3 | 255.255.255.252 | 10.101.2.233 | 10.101.2.234 | 2 | 10.101.2.232 | 10.101.2.235 | - | - |
| P2P 4 | 255.255.255.252 | 10.101.2.237 | 10.101.2.238 | 2 | 10.101.2.236 | 10.101.2.239 | - | - |
| P2P 5 | 255.255.255.252 | 10.101.2.241 | 10.101.2.242 | 2 | 10.101.2.240 | 10.101.2.243 | - | - |
| P2P 6 | 255.255.255.252 | 10.101.2.245 | 10.101.2.246 | 2 | 10.101.2.244 | 10.101.2.247 | - | - |

##### New York Loopbacks
| Interface | IP Address |
|:---|:---|
| LB 1 | 10.101.4.1 |
| LB 2 | 10.101.4.2 |
| LB 3 | 10.101.4.3 |
| LB 4 | 10.101.4.4 |
| LB 5 | 10.101.4.5 |
| LB 6 | 10.101.4.6 |
| LB 7 | 10.101.4.7 |
| LB 8 | 10.101.4.8 |

---

##### Brussels (10.102.0.0/16)
| Subnet | Mask | First IP | Last IP | Hosts | Network | Broadcast | VLAN | Name |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Data | 255.255.255.0 | 10.102.0.1 | 10.102.0.254 | 254 | 10.102.0.0 | 10.102.0.255 | Vlan10 | Data |
| Wireless | 255.255.255.0 | 10.102.1.1 | 10.102.1.254 | 254 | 10.102.1.0 | 10.102.1.255 | Vlan20 | Wireless |
| Server | 255.255.255.192 | 10.102.2.1 | 10.102.2.62 | 62 | 10.102.2.0 | 10.102.2.63 | Vlan200 | Server |
| Management | 255.255.255.192 | 10.102.2.65 | 10.102.2.126 | 62 | 10.102.2.64 | 10.102.2.127 | Vlan100 | Management |
| Free vlan | 255.255.255.192 | 10.102.2.129 | 10.102.2.190 | 62 | 10.102.2.128 | 10.102.2.191 | - | - |
| Free vlan | 255.255.255.224 | 10.102.2.193 | 10.102.2.222 | 30 | 10.102.2.192 | 10.102.2.223 | - | - |
| P2P 1 | 255.255.255.252 | 10.102.2.225 | 10.102.2.226 | 2 | 10.102.2.224 | 10.102.2.227 | - | - |
| P2P 2 | 255.255.255.252 | 10.102.2.229 | 10.102.2.230 | 2 | 10.102.2.228 | 10.102.2.231 | - | - |
| P2P 3 | 255.255.255.252 | 10.102.2.233 | 10.102.2.234 | 2 | 10.102.2.232 | 10.102.2.235 | - | - |
| P2P 4 | 255.255.255.252 | 10.102.2.237 | 10.102.2.238 | 2 | 10.102.2.236 | 10.102.2.239 | - | - |
| P2P 5 | 255.255.255.252 | 10.102.2.241 | 10.102.2.242 | 2 | 10.102.2.240 | 10.102.2.243 | - | - |
| P2P 6 | 255.255.255.252 | 10.102.2.245 | 10.102.2.246 | 2 | 10.102.2.244 | 10.102.2.247 | - | - |

##### Brussels Loopbacks
| Interface | IP Address |
|:---|:---|
| LB 1 | 10.102.4.1 |
| LB 2 | 10.102.4.2 |
| LB 3 | 10.102.4.3 |
| LB 4 | 10.102.4.4 |
| LB 5 | 10.102.4.5 |
| LB 6 | 10.102.4.6 |
| LB 7 | 10.102.4.7 |
| LB 8 | 10.102.4.8 |

# 🔌 Physical Topology
<!-- ![Physical Topology](topologies/Physical_Topology.png) -->
<img src="../Phase-01/topologies/Physical_Topology.png" width="100%">

# 🔗 Logical Topology
<!-- ![Logical Topology](topologies/Logical_Topology.png) -->
<img src="../Phase-01/topologies/Logical_Topology.png" width="100%">

