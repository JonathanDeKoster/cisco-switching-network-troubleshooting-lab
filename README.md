# Cisco Switching and Network Troubleshooting Lab

## Overview

Built and configured a small Cisco network in Cisco Packet Tracer to practice fundamental switching, VLANs, trunking, inter-VLAN routing, device discovery, and network troubleshooting.

The lab included a router, two switches, a workstation, and a printer endpoint. The network was configured with separate VLANs for users and printers and used router-on-a-stick for inter-VLAN routing.

The lab also included a troubleshooting scenario where connectivity issues were investigated using Cisco IOS commands and endpoint testing.

## Topology

```text
             R1
              |
             SW1
              |
             SW2
            /   \
          PC1  Printer1
```

## IP Addressing

| Device | Role | IP Address | VLAN |
|---|---|---|---|
| R1 | User VLAN Gateway | 192.168.10.1/24 | 10 |
| R1 | Printer VLAN Gateway | 192.168.20.1/24 | 20 |
| PC1 | User Workstation | 192.168.10.10/24 | 10 |
| Printer1 | Printer Endpoint | 192.168.20.50/24 | 20 |

## Configuration & Concepts

### 1. Basic Access & Configuration

Practiced fundamental Cisco IOS navigation and configuration, including:

- User EXEC and Privileged EXEC modes
- Global and Interface Configuration modes
- Hostname configuration
- Running vs. startup configuration
- Saving configurations with `copy running-config startup-config`
- Reviewing configurations with `show running-config` and `show startup-config`

### 2. Ports, Interfaces & VLANs

Configured and verified switch ports using:

- Access ports
- Trunk ports
- VLAN 10 — User Network
- VLAN 20 — Printer Network
- `show interfaces status`
- `show vlan brief`

### 3. Inter-VLAN Routing

Configured R1 using router-on-a-stick:

- 802.1Q encapsulation
- Router subinterfaces
- VLAN 10 gateway
- VLAN 20 gateway
- Inter-VLAN connectivity

### 4. Device Discovery & Layer 2/3 Mapping

Practiced tracing an endpoint through the network using:

- `ping`
- `show arp`
- `show mac address-table`
- `show interfaces status`

This demonstrated the relationship between an IP address, its MAC address, and the physical switch port where the device was connected.

### 5. Neighbors, Trunks & Spanning Tree

Verified network infrastructure and Layer 2 behavior using:

- `show interfaces trunk`
- `show cdp neighbors detail`
- `show spanning-tree vlan 20`
- `show etherchannel summary`

Practiced identifying switch uplinks, verifying trunk VLANs, discovering neighboring Cisco devices, and reviewing spanning-tree state.

### 6. Interface Health, Logs & Connectivity

Used Cisco IOS verification commands to evaluate network health:

- `show interfaces fa0/3`
- `show logging`
- `show version`
- `ping`
- `traceroute`

Reviewed interface status, error counters, logs, device information, and end-to-end connectivity.

### 7. Configuration Verification

Compared the switch configuration against the expected known-good state before making changes.

Key configuration relationships included:

- SW2 Fa0/1 → Trunk to SW1
- SW2 Fa0/2 → PC1 / VLAN 10
- SW2 Fa0/3 → Printer1 / VLAN 20
- SW1 uplinks → Trunks carrying VLANs 10 and 20

## Troubleshooting Scenario

### Problem

PC1 initially failed to reach its default gateway and the printer despite having a functioning physical connection.

### Investigation

The issue was investigated systematically rather than immediately changing the configuration.

1. Tested PC1 connectivity with `ping`.
2. Verified PC1's switch port and VLAN assignment.
3. Checked SW2's VLAN and interface status.
4. Verified the trunk connections between SW1 and SW2.
5. Checked SW1's VLAN configuration.
6. Discovered that VLANs 10 and 20 were missing from SW1.
7. Created VLANs 10 and 20 on SW1.
8. Re-verified VLAN status and connectivity.
9. Confirmed PC1 could reach its gateway.
10. Investigated the remaining PC1-to-Printer connectivity issue.
11. Cleared PC1's ARP cache with `arp -d`.
12. Verified successful end-to-end connectivity.

### Root Cause

VLANs 10 and 20 were permitted on the SW1 trunk configuration but had not been created on SW1.

This demonstrated an important distinction:

> Allowing a VLAN on a trunk does not create the VLAN on the switch.

### Final Verification

After correcting the VLAN configuration and clearing the stale ARP entry, PC1 successfully reached:

- Default gateway — `192.168.10.1`
- Printer — `192.168.20.50`

Additional verification confirmed that the printer was reachable through VLAN 20 and that the switch learned its MAC address on the expected port.

## Troubleshooting Methodology

The lab reinforced a structured approach to network troubleshooting:

```text
Can I reach it?
    ↓
ping

What MAC address corresponds to the IP?
    ↓
show arp

Which switch port learned that MAC?
    ↓
show mac address-table

Is the port physically/logically healthy?
    ↓
show interfaces status
show interfaces <port>

Is the port configured correctly?
    ↓
show running-config

Is it in the correct VLAN?
    ↓
show vlan brief

Can the VLAN cross the uplink?
    ↓
show interfaces trunk

What Cisco device is upstream?
    ↓
show cdp neighbors detail

Are there link or configuration events?


## Screenshots / Lab Evidence

### Network Topology

<img width="1277" height="683" alt="Screenshot 2026-09-06 113839" src="https://github.com/user-attachments/assets/4d9c3e24-372e-451e-bf48-eae79e9e4102" />


### Initial Connectivity Issue

<img width="1274" height="688" alt="Screenshot 2026-09-06 140615" src="https://github.com/user-attachments/assets/01ebbe62-ba77-4384-a305-35f9ac7e6090" />


### Identifying the VLAN Issue

<img width="1278" height="682" alt="Screenshot 2026-09-06 140645" src="https://github.com/user-attachments/assets/ea634b70-3194-48b6-8e15-5e4ee64eaa90" />


### Correcting the VLAN Configuration

<img width="1276" height="684" alt="Screenshot 2026-09-06 143242" src="https://github.com/user-attachments/assets/05ef87ba-1d0a-4c35-883e-7a53619d4420" />

### Final Connectivity

<img width="1278" height="684" alt="Screenshot 2026-09-06 144515" src="https://github.com/user-attachments/assets/c32e4c42-33b0-4491-9e95-ac4765f09f76" />

### Device Discovery

<img width="1280" height="687" alt="Screenshot 2026-09-09 145505" src="https://github.com/user-attachments/assets/19d94d91-cc69-41c9-9b36-95f8ef9bd817" />

<img width="1278" height="683" alt="Screenshot 2026-09-09 145643" src="https://github.com/user-attachments/assets/49a04e37-ca88-48a7-bf83-a273898307a3" />

### Trunk & Neighbor Verification

<img width="623" height="602" alt="Screenshot 2026-09-10 131503" src="https://github.com/user-attachments/assets/38b8b8df-e815-4537-a8b9-bb2252ea5275" />


<!-- Add screenshot showing `show interfaces trunk` and/or CDP neighbor discovery -->

### Interface Health and Configuration

<img width="616" height="268" alt="Screenshot 2026-09-10 132713" src="https://github.com/user-attachments/assets/b01f7d44-756e-472b-8b5e-620b1c8ecc03" />

<img width="621" height="328" alt="Screenshot 2026-09-10 134453" src="https://github.com/user-attachments/assets/a37c5276-3475-4e0b-ba6e-d86d6942e75c" />

## Key Takeaways

- Configured VLANs and access ports on Cisco switches.
- Configured and verified 802.1Q trunking.
- Configured router-on-a-stick inter-VLAN routing.
- Used ARP and MAC address tables to trace an endpoint through the network.
- Used CDP and spanning tree to verify Layer 2 topology.
- Practiced interface health and log analysis.
- Used ping and traceroute for connectivity verification.
- Troubleshot a VLAN configuration issue using a structured, evidence-based process.
- Practiced verifying configurations before making changes and saving known-good configurations.
    ↓
show logging
