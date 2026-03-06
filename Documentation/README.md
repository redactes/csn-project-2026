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
| CS1 – New York | Router | [r1-config-ny.md](configs-ny/r1-config-ny.md) |
| CS2 – New York | Router | [r2-config-ny.md](configs-ny/r2-config-ny.md) |
| S3 – New York | Switch | [s1-config-ny.md](configs-ny/s1-config-ny.md) |
| S4 – New York | Switch | [s2-config-ny.md](configs-ny/s2-config-ny.md) |
| FW - Brussels| Firewall | |
| PC1 - Brussels | Workstation | |
| PC2 - Brussels | Workstation | |
| DHCP - Brussels | Server | | |
| Zabbix - Brussels | Server | |

