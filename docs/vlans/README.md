# VLANs (Virtual Local Area Networks)

## Overview

A VLAN is a logical grouping of devices in the same broadcast domain, regardless of their physical location. VLANs segment networks at Layer 2, improving performance, security, and management.

## What is a VLAN?

**Traditional LAN:**
```
All devices in same broadcast domain
[PC1]---[PC2]---[PC3]---[Switch]---[PC4]---[PC5]---[PC6]
        Single broadcast domain
```

**With VLANs:**
```
Logically separated broadcast domains
[PC1]---[PC2]        VLAN 10 (Sales)
            \       /
             [Switch]
            /       \
[PC3]---[PC4]        VLAN 20 (IT)
```

## Benefits of VLANs

| Benefit | Description |
|---------|-------------|
| **Security** | Isolate sensitive data in separate VLANs |
| **Performance** | Smaller broadcast domains reduce traffic |
| **Flexibility** | Move users logically without rewiring |
| **Cost Savings** | No need for additional switches |
| **Management** | Organized network structure |
| **Scalability** | Easy to add/modify departments |

## VLAN Types

### 1. Data VLAN (User VLAN)

Carries user-generated traffic (workstations, servers).

**Example:**
```
VLAN 10: Sales Department
VLAN 20: Engineering Department
VLAN 30: HR Department
```

### 2. Default VLAN (VLAN 1)

- All ports are in VLAN 1 by default
- Cannot be deleted
- Used for management (not recommended)
- Best practice: Don't use VLAN 1 for user traffic

### 3. Native VLAN

- Used on 802.1Q trunk links
- Untagged frames on trunk
- Default: VLAN 1 (should be changed)
- Must match on both ends of trunk

### 4. Management VLAN

- Used for switch management (SSH, Telnet, SNMP)
- Should be separate from user VLANs
- Enhanced security

**Example Configuration:**
```
Switch(config)# interface vlan 99
Switch(config-if)# ip address 192.168.99.1 255.255.255.0
Switch(config-if)# no shutdown
```

### 5. Voice VLAN

- Dedicated VLAN for VoIP traffic
- Quality of Service (QoS) priority
- Separate from data VLAN

**Configuration:**
```
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# switchport voice vlan 50
```

## VLAN Ranges

| VLAN Range | Type | Description |
|------------|------|-------------|
| 0, 4095 | Reserved | Cannot be used |
| 1 | Normal | Default VLAN |
| 2-1001 | Normal | Standard VLANs, stored in vlan.dat |
| 1002-1005 | Normal | Reserved for legacy (Token Ring, FDDI) |
| 1006-4094 | Extended | Require VTP transparent mode |

## VLAN Configuration

### Creating VLANs

**Method 1: VLAN Database Mode**
```
Switch(config)# vlan 10
Switch(config-vlan)# name SALES
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name ENGINEERING
Switch(config-vlan)# exit
```

**Method 2: Global Configuration**
```
Switch(config)# vlan 30
Switch(config-vlan)# name HR
```

### Assigning Ports to VLANs

**Access Port (single VLAN):**
```
Switch(config)# interface FastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

**Range Configuration:**
```
Switch(config)# interface range FastEthernet 0/1-10
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
```

### Verification Commands

```
! View all VLANs
show vlan brief

! View specific VLAN
show vlan id 10

! View VLAN by interface
show vlan name SALES

! Interface VLAN assignment
show interfaces FastEthernet 0/1 switchport

! Trunk configuration
show interfaces trunk
```

## VLAN Trunking

### What is a Trunk?

A trunk is a link that carries traffic for multiple VLANs between switches.

```
Switch A                          Switch B
VLAN 10 ----\                /---- VLAN 10
VLAN 20 -----[Trunk Link]--------- VLAN 20
VLAN 30 ----/                \---- VLAN 30
```

### Trunk Protocols

**802.1Q (Dot1Q):**
- Industry standard
- Inserts 4-byte tag into Ethernet frame
- Supports up to 4,096 VLANs
- Native VLAN is untagged

**ISL (Inter-Switch Link):**
- Cisco proprietary (deprecated)
- Encapsulates entire frame
- 30-byte overhead

**Modern Standard: 802.1Q**

### 802.1Q Frame Tagging

**Original Ethernet Frame:**
```
[Dest MAC][Src MAC][Type][Data][FCS]
```

**802.1Q Tagged Frame:**
```
[Dest MAC][Src MAC][802.1Q Tag][Type][Data][FCS]
                    └─ 4 bytes ─┘
```

**802.1Q Tag Structure:**
```
┌────────────┬─────┬────────────┬──────────────┐
│ TPID (16)  │ PCP │ DEI │ VID (12 bits)      │
│ 0x8100     │(3)  │(1)  │ VLAN ID (1-4094)   │
└────────────┴─────┴─────┴────────────────────┘
```

- **TPID:** Tag Protocol Identifier (0x8100)
- **PCP:** Priority Code Point (QoS)
- **DEI:** Drop Eligible Indicator
- **VID:** VLAN Identifier (1-4094)

### Trunk Configuration

**Basic Trunk Setup:**
```
Switch(config)# interface GigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport trunk native vlan 99
```

**Allow Specific VLANs:**
```
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

**Add VLANs to Trunk:**
```
Switch(config-if)# switchport trunk allowed vlan add 40
```

**Remove VLANs from Trunk:**
```
Switch(config-if)# switchport trunk allowed vlan remove 50
```

## Access Ports vs. Trunk Ports

| Feature | Access Port | Trunk Port |
|---------|------------|------------|
| Purpose | Connect end devices | Connect switches |
| VLANs | Single VLAN | Multiple VLANs |
| Tagging | No tagging | 802.1Q tagging |
| Configuration | `switchport mode access` | `switchport mode trunk` |
| Typical Use | PC, printer, phone | Switch-to-switch |

## Inter-VLAN Routing

Devices in different VLANs cannot communicate without routing.

### Methods

**1. Router with Separate Interfaces (Legacy)**
```
[Switch]
VLAN 10 → Fa0/1 → [Router] Fa0/0 → VLAN 10
VLAN 20 → Fa0/2 → [Router] Fa0/1 → VLAN 20
```

**Limitations:**
- Limited by router interfaces
- Expensive
- Not scalable

**2. Router-on-a-Stick**

Single physical interface with subinterfaces for each VLAN.

```
[Switch]---Trunk---[Router]
VLAN 10              Fa0/0.10
VLAN 20              Fa0/0.20
VLAN 30              Fa0/0.30
```

**Configuration:**

**Switch:**
```
Switch(config)# interface GigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

**Router:**
```
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# no shutdown

Router(config)# interface GigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface GigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0

Router(config)# interface GigabitEthernet 0/0.30
Router(config-subif)# encapsulation dot1q 30
Router(config-subif)# ip address 192.168.30.1 255.255.255.0
```

**3. Layer 3 Switch (SVI - Switched Virtual Interface)**

Most common modern method.

**Configuration:**
```
! Enable IP routing
Switch(config)# ip routing

! Create VLAN interfaces
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# interface vlan 30
Switch(config-if)# ip address 192.168.30.1 255.255.255.0
Switch(config-if)# no shutdown
```

**Benefits:**
- Wire speed routing
- No external router needed
- Scalable
- Cost effective

## VTP (VLAN Trunking Protocol)

VTP propagates VLAN configuration across switches (Cisco proprietary).

### VTP Modes

| Mode | Description | Behavior |
|------|-------------|----------|
| **Server** | Default mode | Create/modify/delete VLANs, propagate changes |
| **Client** | Receives updates | Cannot create/modify VLANs |
| **Transparent** | Forwards updates | Create local VLANs, doesn't sync |
| **Off** | VTP disabled | No VTP, local VLANs only |

### VTP Configuration

```
! Set VTP domain
Switch(config)# vtp domain COMPANY

! Set VTP mode
Switch(config)# vtp mode server

! Set VTP password (optional)
Switch(config)# vtp password cisco123

! Set VTP version
Switch(config)# vtp version 2
```

### VTP Verification

```
show vtp status
show vtp password
show vtp counters
```

### VTP Cautions

⚠️ **Danger:** High revision number switch can wipe VLANs
- New switch with higher revision number
- Overwrites entire VLAN database
- Can cause network outage

**Prevention:**
1. Set to transparent mode before connecting
2. Reset revision number: change domain name twice
3. Use VTP version 3 (supports off mode)
4. Many organizations disable VTP

## DTP (Dynamic Trunking Protocol)

DTP automatically negotiates trunk links (Cisco proprietary).

### DTP Modes

```
! Dynamic Auto (default)
Switch(config-if)# switchport mode dynamic auto

! Dynamic Desirable
Switch(config-if)# switchport mode dynamic desirable

! Manual Trunk
Switch(config-if)# switchport mode trunk

! Manual Access
Switch(config-if)# switchport mode access

! Disable DTP (security)
Switch(config-if)# switchport nonegotiate
```

### DTP Negotiation Matrix

| Local Port | Remote Port | Result |
|------------|-------------|--------|
| trunk | trunk | Trunk |
| trunk | dynamic auto | Trunk |
| trunk | dynamic desirable | Trunk |
| dynamic desirable | dynamic desirable | Trunk |
| dynamic desirable | dynamic auto | Trunk |
| dynamic auto | dynamic auto | Access |
| access | * | Access |

**Best Practice:** Manually configure trunk/access (disable DTP)

## VLAN Security

### VLAN Hopping Attacks

**Type 1: Switch Spoofing**
- Attacker negotiates trunk with DTP
- Access to all VLANs

**Prevention:**
```
Switch(config-if)# switchport mode access
Switch(config-if)# switchport nonegotiate
```

**Type 2: Double Tagging**
- Attacker sends double-tagged frame
- Exploits native VLAN

**Prevention:**
```
! Change native VLAN from default
Switch(config-if)# switchport trunk native vlan 999

! Prune unused VLANs
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

### Best Practices

1. **Change native VLAN from VLAN 1**
   ```
   switchport trunk native vlan 999
   ```

2. **Disable DTP on access ports**
   ```
   switchport mode access
   switchport nonegotiate
   ```

3. **Disable unused ports**
   ```
   shutdown
   ```

4. **Assign unused ports to unused VLAN**
   ```
   switchport access vlan 999
   ```

5. **Prune unnecessary VLANs from trunks**
   ```
   switchport trunk allowed vlan 10,20,30
   ```

6. **Implement port security**
   ```
   switchport port-security
   ```

7. **Use separate management VLAN**
   - Not VLAN 1
   - Restricted access

## Practical VLAN Design Example

### Scenario: Small Office Network

**Requirements:**
- 3 departments: Sales, IT, Guest
- 1 switch, 1 router
- Secure guest network

**Design:**
```
VLAN 10: Sales (192.168.10.0/24)
VLAN 20: IT (192.168.20.0/24)
VLAN 30: Guest (192.168.30.0/24)
VLAN 99: Management (192.168.99.0/24)
VLAN 100: Native (unused)
```

**Switch Configuration:**
```
! Create VLANs
vlan 10
 name SALES
vlan 20
 name IT
vlan 30
 name GUEST
vlan 99
 name MANAGEMENT
vlan 100
 name NATIVE

! Configure management
interface vlan 99
 ip address 192.168.99.10 255.255.255.0
 no shutdown

! Access ports for Sales
interface range Fa0/1-10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast

! Access ports for IT
interface range Fa0/11-20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

! Trunk to router
interface Gi0/1
 switchport mode trunk
 switchport trunk native vlan 100
 switchport trunk allowed vlan 10,20,30,99
 switchport nonegotiate
```

**Router Configuration (Router-on-a-Stick):**
```
interface Gi0/0
 no shutdown

interface Gi0/0.10
 encapsulation dot1q 10
 ip address 192.168.10.1 255.255.255.0

interface Gi0/0.20
 encapsulation dot1q 20
 ip address 192.168.20.1 255.255.255.0

interface Gi0/0.30
 encapsulation dot1q 30
 ip address 192.168.30.1 255.255.255.0

interface Gi0/0.99
 encapsulation dot1q 99
 ip address 192.168.99.1 255.255.255.0
```

## Troubleshooting VLANs

### Common Issues

**1. Devices in same VLAN cannot communicate**
- Check VLAN assignment: `show vlan brief`
- Verify physical connectivity
- Check port status: `show interface status`

**2. Devices in different VLANs cannot communicate**
- Verify inter-VLAN routing configured
- Check default gateway on devices
- Verify routing table: `show ip route`

**3. Trunk not working**
- Verify trunk status: `show interfaces trunk`
- Check allowed VLANs
- Verify native VLAN matches on both ends
- Check encapsulation (dot1q)

**4. Native VLAN mismatch**
- CDP warning: "native VLAN mismatch"
- Verify: `show interfaces trunk`
- Must match on both sides

### Diagnostic Commands

```
! VLAN information
show vlan brief
show vlan id 10
show interfaces vlan 10

! Trunk verification
show interfaces trunk
show interfaces Gi0/1 switchport
show interfaces Gi0/1 trunk

! MAC addresses per VLAN
show mac address-table vlan 10

! VTP status
show vtp status

! Spanning-tree per VLAN
show spanning-tree vlan 10
```

## Summary

- **VLANs** logically segment networks at Layer 2
- **Benefits:** Security, performance, flexibility, cost savings
- **Types:** Data, voice, management, native VLANs
- **Trunking:** 802.1Q carries multiple VLANs over single link
- **Inter-VLAN routing:** Required for VLAN communication
- **VTP:** Propagates VLAN configuration (use with caution)
- **Security:** Change native VLAN, disable DTP, prune VLANs

## Practice Questions

1. What is the default VLAN? (Answer: VLAN 1)
2. What protocol tags VLAN frames? (Answer: 802.1Q)
3. How many bytes is the 802.1Q tag? (Answer: 4 bytes)
4. What is the VLAN range for normal VLANs? (Answer: 1-1001)
5. What command creates VLAN 10? (Answer: vlan 10)

## Next Steps

- Study [Inter-VLAN Routing](../routing/README.md) in detail
- Learn about [Switching](../switching/README.md) fundamentals
- Explore [Packet Flow](../packet-flow/README.md) across VLANs
