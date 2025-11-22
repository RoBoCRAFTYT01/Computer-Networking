# Switching

## Overview

Switching operates at Layer 2 (Data Link Layer) of the OSI model and is responsible for forwarding frames within a local network based on MAC addresses. Switches are the backbone of modern LANs.

## What is a Switch?

A switch is a network device that:
- **Connects devices** within a LAN
- **Forwards frames** based on MAC addresses
- **Learns MAC addresses** dynamically
- **Filters traffic** to reduce collisions
- **Operates at Layer 2** (some at Layer 3)

### Switch vs. Hub

| Feature | Hub | Switch |
|---------|-----|--------|
| OSI Layer | Layer 1 (Physical) | Layer 2 (Data Link) |
| Intelligence | No intelligence | MAC address learning |
| Traffic Handling | Broadcasts to all ports | Forwards to specific port |
| Collision Domain | Single (shared) | One per port |
| Bandwidth | Shared among all ports | Dedicated per port |
| Performance | Poor | Excellent |
| Security | Low | Higher |
| Modern Use | Obsolete | Standard |

## How Switches Work

### MAC Address Table (CAM Table)

The **Content Addressable Memory (CAM)** table stores MAC addresses and their associated switch ports.

**Structure:**
```
MAC Address          Port    VLAN    Age (seconds)
----------------------------------------------------
00:1A:2B:3C:4D:5E    Fa0/1   1       120
00:5E:4D:3C:2B:1A    Fa0/5   1       45
AA:BB:CC:DD:EE:FF    Fa0/10  10      200
```

### Frame Forwarding Process

```
1. Frame arrives at switch port
     ↓
2. Switch reads source MAC address
     ↓
3. Switch updates MAC address table
   (MAC + Port association)
     ↓
4. Switch reads destination MAC address
     ↓
5. Switch looks up destination in MAC table
     ↓
6. Decision:
   ├── Known unicast → Forward to specific port
   ├── Unknown unicast → Flood to all ports (except source)
   ├── Broadcast → Flood to all ports (except source)
   └── Multicast → Forward based on configuration
```

### Learning Process Example

**Scenario: New network, empty MAC table**

```
Step 1: PC-A (MAC: AAAA) sends frame to PC-B (MAC: BBBB)

MAC Table Before:
[Empty]

Switch Action:
- Learns: AAAA on Port 1
- Destination BBBB unknown → Floods frame

MAC Table After:
AAAA    Port 1

Step 2: PC-B (MAC: BBBB) replies to PC-A (MAC: AAAA)

Switch Action:
- Learns: BBBB on Port 2
- Destination AAAA known → Forwards to Port 1 only

MAC Table After:
AAAA    Port 1
BBBB    Port 2
```

## Switching Methods

### 1. Store-and-Forward

**Process:**
- Receives entire frame
- Checks for errors (CRC)
- Forwards if error-free

**Advantages:**
- Error checking
- Reliable

**Disadvantages:**
- Higher latency
- Slower

**Best for:** Quality-sensitive environments

### 2. Cut-Through

**Process:**
- Reads destination MAC (first 6 bytes)
- Begins forwarding immediately
- No error checking

**Advantages:**
- Lower latency
- Faster

**Disadvantages:**
- May forward bad frames
- No error detection

**Best for:** Speed-critical, low-error environments

### 3. Fragment-Free

**Process:**
- Reads first 64 bytes
- Checks for collision fragments
- Then forwards

**Advantages:**
- Balance of speed and reliability
- Catches most errors

**Best for:** General use

## Switch Types

### Unmanaged Switches

**Characteristics:**
- Plug and play
- No configuration needed
- Fixed functionality
- Low cost

**Use Cases:**
- Home networks
- Small offices
- Simple connectivity

### Managed Switches

**Characteristics:**
- Full configuration capabilities
- VLAN support
- Port mirroring
- QoS (Quality of Service)
- SNMP monitoring
- CLI/Web interface

**Use Cases:**
- Enterprise networks
- Data centers
- Networks requiring control

### Layer 3 Switches

**Characteristics:**
- Combines switching and routing
- Hardware-based routing
- VLAN routing (inter-VLAN)
- Very fast forwarding

**Use Cases:**
- Core/distribution layers
- High-performance routing
- Large enterprise networks

## Spanning Tree Protocol (STP)

### Purpose

STP prevents **switching loops** in redundant topologies by:
- Blocking redundant paths
- Creating a loop-free topology
- Providing automatic failover

### The Loop Problem

```
Without STP:

   Switch A
    /    \
   /      \
Switch B - Switch C

Problem: Broadcast storms
- Frame loops infinitely
- MAC table thrashing
- Network congestion
```

### STP Operation

**Bridge Protocol Data Units (BPDUs):**
- Hello messages between switches
- Sent every 2 seconds
- Contain bridge ID and path costs

**STP Port States:**
```
1. Blocking    → No forwarding, receives BPDUs
2. Listening   → No forwarding, sends/receives BPDUs
3. Learning    → No forwarding, learns MAC addresses
4. Forwarding  → Forwards frames, learns MACs
5. Disabled    → Administratively down
```

**Transition Times:**
- Listening: 15 seconds
- Learning: 15 seconds
- **Total convergence: 30-50 seconds**

### STP Port Roles

```
┌────────────────────────────────────┐
│  STP Port Roles                    │
├────────────────────────────────────┤
│  Root Port (RP):                   │
│    - Best path to root bridge      │
│    - One per non-root switch       │
│                                    │
│  Designated Port (DP):             │
│    - Forwarding port on segment    │
│    - One per network segment       │
│                                    │
│  Non-Designated Port:              │
│    - Blocked to prevent loops      │
│    - Backup path                   │
└────────────────────────────────────┘
```

### Root Bridge Election

**Process:**
1. Switches exchange BPDUs
2. Lowest Bridge ID becomes root
3. Bridge ID = Priority + MAC address
4. Default priority: 32768

**Example:**
```
Switch A: Priority 32768, MAC: 0000.0000.AAAA
Switch B: Priority 32768, MAC: 0000.0000.BBBB
Switch C: Priority 32768, MAC: 0000.0000.CCCC

Root Bridge: Switch A (lowest MAC)
```

### STP Variations

**Types:**
- **STP (802.1D):** Original, slow convergence (30-50s)
- **RSTP (802.1w):** Rapid STP, fast convergence (~6s)
- **MSTP (802.1s):** Multiple STP, VLAN-aware
- **PVST+:** Cisco proprietary, per-VLAN STP
- **Rapid PVST+:** Fast convergence per-VLAN

### Configuration Example

```
! Set switch as root bridge (priority to 0)
Switch(config)# spanning-tree vlan 1 root primary

! Or manually set priority
Switch(config)# spanning-tree vlan 1 priority 4096

! Enable PortFast (skip listening/learning on access ports)
Switch(config-if)# spanning-tree portfast

! Enable BPDU Guard (shutdown port if BPDU received)
Switch(config-if)# spanning-tree bpduguard enable
```

## Port Security

Port security restricts which devices can connect to a switch port based on MAC addresses.

### Configuration

```
! Enable port security
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security

! Set maximum MAC addresses (default: 1)
Switch(config-if)# switchport port-security maximum 2

! Define allowed MAC address
Switch(config-if)# switchport port-security mac-address 0000.1111.2222

! Or learn MAC addresses dynamically
Switch(config-if)# switchport port-security mac-address sticky

! Set violation action
Switch(config-if)# switchport port-security violation {shutdown|restrict|protect}
```

### Violation Modes

| Mode | Action | Log | Increment Counter |
|------|--------|-----|-------------------|
| **Shutdown** | Disables port (err-disabled) | Yes | Yes |
| **Restrict** | Drops violating frames | Yes | Yes |
| **Protect** | Drops violating frames | No | No |

### Common Use Cases

1. **Restrict port to one device**
   ```
   maximum 1
   violation shutdown
   ```

2. **IP Phone + PC**
   ```
   maximum 2
   violation restrict
   ```

3. **Conference room (learn 5 devices)**
   ```
   maximum 5
   mac-address sticky
   ```

## Switch Features

### 1. Port Mirroring (SPAN)

Copies traffic from one port to another for monitoring.

**Configuration:**
```
! Configure SPAN session
Switch(config)# monitor session 1 source interface Fa0/1
Switch(config)# monitor session 1 destination interface Fa0/24

! Monitor multiple source ports
Switch(config)# monitor session 1 source interface Fa0/1 - 10
```

**Use Cases:**
- Wireshark packet capture
- IDS/IPS monitoring
- Network analysis

### 2. Link Aggregation (EtherChannel)

Combines multiple physical links into one logical link.

**Benefits:**
- Increased bandwidth
- Load balancing
- Redundancy
- No STP blocking

**Protocols:**
- **PAgP:** Cisco proprietary
- **LACP:** Industry standard (802.3ad)

**Configuration Example:**
```
! Using LACP
Switch(config)# interface range Fa0/1-2
Switch(config-if-range)# channel-group 1 mode active
Switch(config-if-range)# exit

Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
```

### 3. Storm Control

Prevents broadcast, multicast, or unicast storms.

**Configuration:**
```
Switch(config-if)# storm-control broadcast level 50
Switch(config-if)# storm-control multicast level 40
Switch(config-if)# storm-control action shutdown
```

### 4. DHCP Snooping

Security feature that prevents rogue DHCP servers.

**Configuration:**
```
! Enable globally
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 1,10,20

! Trust uplink to legitimate DHCP server
Switch(config-if)# ip dhcp snooping trust

! Limit DHCP rate on access ports
Switch(config-if)# ip dhcp snooping limit rate 10
```

**How It Works:**
- Builds binding table (MAC, IP, VLAN, Port)
- Blocks DHCP responses from untrusted ports
- Prevents DHCP starvation attacks

### 5. Dynamic ARP Inspection (DAI)

Prevents ARP spoofing attacks.

**Configuration:**
```
! Enable DAI on VLANs (requires DHCP snooping)
Switch(config)# ip arp inspection vlan 1,10,20

! Trust uplink ports
Switch(config-if)# ip arp inspection trust

! Rate limiting
Switch(config-if)# ip arp inspection limit rate 15
```

## Common Switch Commands

### Cisco IOS Commands

```
! View MAC address table
show mac address-table
show mac address-table interface Fa0/1
show mac address-table dynamic

! Clear MAC table
clear mac address-table dynamic

! View interface status
show interfaces status
show interfaces Fa0/1
show interfaces trunk

! Spanning Tree
show spanning-tree
show spanning-tree summary
show spanning-tree interface Fa0/1

! Port Security
show port-security
show port-security interface Fa0/1
show port-security address

! EtherChannel
show etherchannel summary
show etherchannel port-channel

! VLAN information
show vlan brief
show vlan id 10
```

## Switching Best Practices

1. **Disable unused ports**
   ```
   Switch(config-if)# shutdown
   ```

2. **Assign unused ports to unused VLAN**
   ```
   Switch(config)# vlan 999
   Switch(config-vlan)# name UNUSED
   Switch(config-if-range)# switchport access vlan 999
   ```

3. **Enable port security on access ports**
   - Prevent unauthorized devices
   - Use sticky MAC learning

4. **Implement BPDU Guard on access ports**
   - Prevents STP manipulation
   - Protects against rogue switches

5. **Use Rapid PVST+ or MSTP**
   - Faster convergence than legacy STP
   - Better for modern networks

6. **Enable DHCP snooping and DAI**
   - Prevents man-in-the-middle attacks
   - Builds trusted device database

7. **Monitor switch resources**
   - CPU utilization
   - Memory usage
   - MAC table utilization

8. **Regular firmware updates**
   - Security patches
   - Bug fixes
   - New features

## Troubleshooting Switch Issues

### Common Problems

**1. Switching Loop (Broadcast Storm):**
- **Symptoms:** High CPU, slow network, timeouts
- **Cause:** STP disabled or misconfigured
- **Solution:** Enable/verify STP configuration

**2. MAC Address Flapping:**
- **Symptoms:** MAC moves between ports rapidly
- **Cause:** Loop, bad cable, duplex mismatch
- **Solution:** Check physical connections, verify STP

**3. Port Err-Disabled:**
- **Symptoms:** Port shuts down automatically
- **Cause:** Port security violation, BPDU guard, others
- **Solution:** 
  ```
  show interfaces status err-disabled
  errdisable recovery cause [reason]
  ```

**4. Duplex Mismatch:**
- **Symptoms:** Slow performance, CRC errors
- **Cause:** One side auto, one side forced
- **Solution:** Match duplex settings on both ends

**5. Port Utilization:**
- **Symptoms:** Slow traffic on specific port
- **Cause:** Bandwidth saturation
- **Solution:** Monitor with `show interfaces`, upgrade link

### Diagnostic Commands

```
! Interface errors
show interfaces Fa0/1 | include error

! Interface counters
show interfaces Fa0/1 counters

! CPU utilization
show processes cpu sorted

! Memory usage
show memory

! System logs
show logging

! Port security violations
show port-security interface Fa0/1
```

## Summary

- **Switches** forward frames based on MAC addresses (Layer 2)
- **MAC address table** learns source addresses dynamically
- **Switching methods:** Store-and-forward, cut-through, fragment-free
- **STP** prevents loops in redundant topologies
- **Port security** restricts device access to switch ports
- **Advanced features:** SPAN, EtherChannel, storm control, DHCP snooping
- **Best practices** improve security and performance

## Practice Questions

1. What OSI layer do switches operate at? (Answer: Layer 2)
2. What table stores MAC address to port mappings? (Answer: CAM/MAC address table)
3. What protocol prevents switching loops? (Answer: STP)
4. What is the default STP convergence time? (Answer: 30-50 seconds)
5. What violation mode shuts down a port? (Answer: Shutdown)

## Next Steps

- Learn about [VLANs](../vlans/README.md) for network segmentation
- Study [Routing](../routing/README.md) for inter-network communication
- Explore [Packet Flow](../packet-flow/README.md) to see switching in action
