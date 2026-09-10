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
    ↓
show logging
