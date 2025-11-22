# Network Topologies

## Overview

Network topology refers to the arrangement of different elements (nodes, links, etc.) in a computer network. Understanding topologies is essential for network design, troubleshooting, and optimization.

## Types of Topologies

Topologies are classified into two main categories:
1. **Physical Topology** - The actual physical layout of cables and devices
2. **Logical Topology** - How data flows through the network

## Physical vs. Logical Topology

| Physical Topology | Logical Topology |
|-------------------|------------------|
| Physical arrangement of cables and devices | Path that data travels |
| Hardware placement | Data flow pattern |
| Visible structure | Functional design |
| Example: Star cabling | Example: Token passing in star |

**Example:**
```
Physical: Star topology (all devices connected to central switch)
Logical: Bus topology (data broadcast to all devices via switch)
```

## Common Network Topologies

### 1. Bus Topology

**Description:** All devices connected to a single central cable (the bus).

**Diagram:**
```
Device 1 ──┬── Device 2 ──┬── Device 3 ──┬── Device 4
           │              │              │
        ───┴──────────────┴──────────────┴────
               Single Communication Bus
              (Terminators on both ends)
```

**Characteristics:**
- Single backbone cable
- Terminators on both ends prevent signal reflection
- All devices share same communication medium
- Data travels in both directions

**Advantages:**
- ✅ Low cost (minimal cabling)
- ✅ Easy to install for small networks
- ✅ No specialized equipment needed
- ✅ Easy to extend

**Disadvantages:**
- ❌ Single point of failure (cable break affects all)
- ❌ Performance degrades with more devices
- ❌ Difficult to troubleshoot
- ❌ Limited cable length
- ❌ Obsolete for modern networks

**Use Cases:**
- Legacy networks (10BASE2, 10BASE5 Ethernet)
- Small temporary setups
- Rarely used today

### 2. Star Topology

**Description:** All devices connected to a central hub or switch.

**Diagram:**
```
        Device 1
            |
            |
Device 2 ───┼─── Device 3
            |
        [Switch/Hub]
            |
Device 4 ───┼─── Device 5
            |
        Device 6
```

**Characteristics:**
- Central connection point (switch/hub)
- Each device has dedicated cable to center
- Most common modern LAN topology
- Switch/hub manages all communication

**Advantages:**
- ✅ Easy to install and manage
- ✅ Easy to add/remove devices
- ✅ Failure of one device doesn't affect others
- ✅ Easy troubleshooting
- ✅ Good performance
- ✅ Centralized management

**Disadvantages:**
- ❌ Central device is single point of failure
- ❌ Requires more cable than bus
- ❌ Cost of central device (switch)
- ❌ Limited by central device capacity

**Use Cases:**
- Modern LANs (most common)
- Office networks
- Home networks
- Data centers

**Variations:**
- **Extended Star:** Multiple star topologies connected together
- **Distributed Star:** Hierarchical star arrangement

### 3. Ring Topology

**Description:** Devices connected in a closed loop, data travels in one direction.

**Diagram:**
```
        Device 1
       /        \
Device 2        Device 6
      |            |
Device 3        Device 5
       \        /
        Device 4
        
   (Data flows clockwise)
```

**Characteristics:**
- Each device connected to two neighbors
- Data travels in one direction (unidirectional)
- Token passing or other access control
- Signals regenerated at each node

**Advantages:**
- ✅ Equal access for all devices
- ✅ No collisions (token-based)
- ✅ Predictable performance
- ✅ Can cover longer distances than bus

**Disadvantages:**
- ❌ Single device failure can break the ring
- ❌ Difficult to troubleshoot
- ❌ Adding/removing devices disrupts network
- ❌ Slower than switched networks

**Use Cases:**
- Token Ring networks (legacy)
- FDDI (Fiber Distributed Data Interface) - legacy
- SONET/SDH fiber optic networks
- Rarely used in modern LANs

**Modern Variant:**
- **Dual Ring:** Two counter-rotating rings for redundancy

### 4. Mesh Topology

**Description:** Every device connected to every other device.

**Full Mesh Diagram:**
```
    Device 1 ────── Device 2
       /  \          /  \
      /    \        /    \
     /      \      /      \
Device 3 ──── Device 4

(Every device connected to every other device)
```

**Types:**

**Full Mesh:**
- Every device connected to every other device
- Maximum redundancy
- Formula: n(n-1)/2 connections for n devices

**Partial Mesh:**
- Some devices fully connected, others partially
- Balance between cost and redundancy
- Strategic redundancy placement

**Characteristics:**
- Multiple paths between devices
- High redundancy
- No single point of failure
- Complex design

**Advantages:**
- ✅ Extremely reliable (multiple paths)
- ✅ No single point of failure
- ✅ High fault tolerance
- ✅ Direct communication paths
- ✅ Easy to isolate faults
- ✅ High bandwidth availability

**Disadvantages:**
- ❌ Very expensive (many connections)
- ❌ Complex installation and configuration
- ❌ Difficult to maintain
- ❌ Requires significant cabling
- ❌ Scalability issues

**Use Cases:**
- WAN backbone networks
- Critical infrastructure
- Internet backbone
- Data center interconnections
- Military/government networks

**Connection Calculation:**
```
For n devices in full mesh:
Connections = n(n-1)/2

Example: 5 devices
Connections = 5(5-1)/2 = 10 connections
```

### 5. Hybrid Topology

**Description:** Combination of two or more different topologies.

**Example - Star-Bus Hybrid:**
```
    [Switch A]              [Switch B]
       /  \                    /  \
    PC1  PC2              PC3  PC4
       \  /                    \  /
         |                       |
         └───── Backbone ────────┘
         
   (Star topology at each site,
    Bus backbone between sites)
```

**Common Hybrid Combinations:**

**1. Star-Star (Hierarchical):**
```
          [Core Switch]
         /      |      \
[Access    [Access    [Access
Switch 1]  Switch 2]  Switch 3]
  / | \      / | \      / | \
PCs PCs    PCs PCs    PCs PCs
```

**2. Star-Ring:**
```
MAU (Token Ring) connected in ring
Each MAU has star-connected devices
```

**3. Tree Topology (Hierarchical Star):**
```
            [Root]
           /      \
       [Node]    [Node]
       /   \      /   \
    [Leaf] [Leaf] [Leaf] [Leaf]
```

**Advantages:**
- ✅ Flexible design
- ✅ Scalable
- ✅ Can optimize for specific needs
- ✅ Combines benefits of multiple topologies

**Disadvantages:**
- ❌ Complex design
- ❌ Expensive
- ❌ Difficult to maintain

**Use Cases:**
- Large enterprise networks
- Campus networks
- Multi-building organizations

### 6. Point-to-Point Topology

**Description:** Direct connection between two devices.

**Diagram:**
```
Device A ───────────────── Device B
    (Direct connection)
```

**Characteristics:**
- Simplest topology
- Direct, dedicated connection
- Used in WAN links
- High performance

**Advantages:**
- ✅ Simple and straightforward
- ✅ High-speed connection
- ✅ Dedicated bandwidth
- ✅ Easy to maintain
- ✅ Low latency

**Disadvantages:**
- ❌ Limited to two devices
- ❌ Not scalable
- ❌ Expensive for long distances

**Use Cases:**
- Router-to-router connections
- Dedicated leased lines
- Microwave links
- Point-to-point VPNs

### 7. Tree Topology (Hierarchical)

**Description:** Hybrid of bus and star, hierarchical structure.

**Diagram:**
```
              [Root Switch]
               /    |    \
              /     |     \
       [Switch]  [Switch]  [Switch]
        /  \       /  \       /  \
      PC  PC     PC  PC     PC  PC
```

**Characteristics:**
- Hierarchical structure
- Root node at top
- Branches extend downward
- Common in large organizations

**Advantages:**
- ✅ Scalable
- ✅ Organized structure
- ✅ Easy to manage and maintain
- ✅ Easy to expand
- ✅ Error detection at each level

**Disadvantages:**
- ❌ Dependent on root node
- ❌ If backbone fails, entire segment affected
- ❌ More cabling required
- ❌ More expensive

**Use Cases:**
- Large corporate networks
- University campuses
- Multi-floor buildings
- Hierarchical organizations

## Network Design Models

### Three-Tier Hierarchical Model

**Structure:**
```
┌─────────────────────────────────┐
│     CORE LAYER                  │  ← High-speed backbone
│  (Routing, Fast switching)      │     No packet manipulation
└─────────────────────────────────┘
           ↓    ↓    ↓
┌─────────────────────────────────┐
│   DISTRIBUTION LAYER            │  ← Policy enforcement
│  (Routing, Filtering, QoS)      │     VLAN routing
└─────────────────────────────────┘
           ↓    ↓    ↓
┌─────────────────────────────────┐
│     ACCESS LAYER                │  ← User connection
│  (Switching, Port security)     │     End device access
└─────────────────────────────────┘
```

**Layer Functions:**

**Core Layer:**
- High-speed packet switching
- Redundancy and fault tolerance
- Minimal latency
- No packet manipulation
- Connects distribution layers

**Distribution Layer:**
- Routing between VLANs
- Policy enforcement
- Access control lists (ACLs)
- QoS (Quality of Service)
- Aggregation point for access layer

**Access Layer:**
- End-user device connection
- Port security
- VLAN assignment
- PoE (Power over Ethernet)
- Spanning tree

### Collapsed Core (Two-Tier)

**Structure:**
```
┌─────────────────────────────────┐
│  CORE + DISTRIBUTION            │
│  (Combined layer)               │
└─────────────────────────────────┘
           ↓    ↓    ↓
┌─────────────────────────────────┐
│     ACCESS LAYER                │
└─────────────────────────────────┘
```

**Use Case:** Smaller networks where core and distribution combined

## Topology Selection Criteria

### Factors to Consider

**1. Cost:**
- Cable costs
- Equipment costs
- Installation costs
- Maintenance costs

**2. Scalability:**
- Future growth
- Adding new devices
- Expanding coverage

**3. Reliability:**
- Redundancy requirements
- Fault tolerance
- Business criticality

**4. Performance:**
- Bandwidth requirements
- Latency sensitivity
- Traffic patterns

**5. Management:**
- Complexity
- Troubleshooting ease
- Monitoring capabilities

**6. Physical Layout:**
- Building structure
- Distance limitations
- Cable accessibility

### Topology Comparison Matrix

| Topology | Cost | Scalability | Reliability | Performance | Complexity |
|----------|------|-------------|-------------|-------------|------------|
| Bus | Low | Poor | Low | Low | Low |
| Star | Medium | Good | Medium | High | Low |
| Ring | Medium | Poor | Medium | Medium | Medium |
| Mesh | Very High | Poor | Very High | Very High | High |
| Hybrid | High | Excellent | High | High | High |
| Tree | Medium-High | Excellent | Medium | High | Medium |

## Wireless Topologies

### Infrastructure Mode

**Description:** Wireless devices connect through access point.

```
    PC1 (Wi-Fi)     PC2 (Wi-Fi)
         \             /
          \           /
           \         /
        [Access Point]
              |
         [Switch/Router]
              |
         (Wired Network)
```

**Characteristics:**
- Centralized access point
- Access point connects to wired network
- Most common wireless deployment

### Ad-Hoc Mode (Peer-to-Peer)

**Description:** Devices connect directly to each other.

```
  PC1 ←─→ PC2 ←─→ PC3
   ↕               ↕
  PC4 ←─────────→ PC5
  
  (Direct wireless connections)
```

**Characteristics:**
- No access point required
- Direct device-to-device communication
- Limited range and scalability

### Mesh Wireless

**Description:** Multiple access points creating mesh network.

```
  [AP1] ←→ [AP2] ←→ [AP3]
    ↕        ↕        ↕
  [AP4] ←→ [AP5] ←→ [AP6]
  
  (All APs interconnected wirelessly)
```

**Characteristics:**
- Self-healing network
- Multiple paths
- Extended coverage
- High reliability

## Real-World Examples

### 1. Small Office (10-20 users)

**Topology:** Star
```
Structure:
- 1 Central switch (24-port)
- All PCs connected to switch
- Router connected to switch
- Access point for wireless

Benefits:
- Simple management
- Cost-effective
- Easy troubleshooting
```

### 2. Multi-Floor Building (100-500 users)

**Topology:** Tree (Hierarchical)
```
Structure:
Floor 3: Access switches → PCs
           ↓
Floor 2: Access switches → PCs
           ↓
Floor 1: Distribution switch
           ↓
Basement: Core switch → Router → Internet

Benefits:
- Scalable design
- Organized structure
- Efficient traffic flow
```

### 3. Enterprise Campus (1000+ users)

**Topology:** Hybrid (Star-Mesh)
```
Structure:
- Core layer: Mesh (redundant core switches)
- Distribution layer: Star connections to core
- Access layer: Star connections to distribution
- Wireless mesh for outdoor coverage

Benefits:
- High availability
- Redundancy
- Performance
- Scalability
```

### 4. Data Center

**Topology:** Mesh (Spine-Leaf)
```
Structure:
    [Spine 1] ←→ [Spine 2] ←→ [Spine 3]
       ↕ ↕ ↕       ↕ ↕ ↕       ↕ ↕ ↕
    [Leaf] [Leaf] [Leaf] [Leaf] [Leaf]
      ↓      ↓      ↓      ↓      ↓
    Servers Servers Servers Servers Servers

Benefits:
- Equal latency to all servers
- High bandwidth
- Scalable
- Predictable performance
```

## Troubleshooting by Topology

### Star Topology Issues

**Problem:** Multiple devices can't connect
- **Check:** Central switch/hub
- **Solution:** Replace/restart central device

**Problem:** Single device can't connect
- **Check:** Cable, port, NIC
- **Solution:** Test cable, check port, verify NIC

### Bus Topology Issues

**Problem:** Entire network down
- **Check:** Cable break, missing terminator
- **Solution:** Trace cable, check terminators

**Problem:** Performance degradation
- **Check:** Too many devices, collisions
- **Solution:** Reduce devices, upgrade to switch

### Ring Topology Issues

**Problem:** Network fails
- **Check:** Broken device/link in ring
- **Solution:** Bypass failed device, repair link

### Mesh Topology Issues

**Problem:** Degraded performance
- **Check:** Multiple path failures
- **Solution:** Repair failed links, verify routing

## Best Practices

1. **Choose appropriate topology for needs**
   - Size of network
   - Budget constraints
   - Reliability requirements

2. **Plan for growth**
   - Scalable design
   - Extra capacity
   - Modular approach

3. **Implement redundancy**
   - Critical systems need backup paths
   - Consider partial mesh for key connections

4. **Document topology**
   - Physical layout diagrams
   - Logical flow diagrams
   - Label all connections

5. **Use hierarchical design for large networks**
   - Easier to manage
   - Predictable behavior
   - Scalable architecture

6. **Consider wireless needs**
   - Infrastructure mode for most cases
   - Mesh for extended coverage
   - Plan for interference

7. **Implement proper cable management**
   - Labeled cables
   - Organized routing
   - Easy to trace

## Summary

- **Physical topology** describes physical layout
- **Logical topology** describes data flow
- **Star topology** is most common in modern LANs
- **Mesh topology** provides highest redundancy
- **Hybrid topologies** combine multiple types
- **Hierarchical design** suits large networks
- **Topology selection** depends on requirements (cost, reliability, scalability)
- **Proper planning** is essential for network success

## Practice Questions

1. What is the most common LAN topology today? (Answer: Star)
2. Which topology has no single point of failure? (Answer: Mesh)
3. How many connections in a 6-device full mesh? (Answer: 15)
4. What are the three layers in hierarchical design? (Answer: Core, Distribution, Access)
5. Which topology is obsolete for modern networks? (Answer: Bus)

## Next Steps

- Study [Switching](../switching/README.md) for star topology implementation
- Review [Routing](../routing/README.md) for connecting topologies
- Explore [VLANs](../vlans/README.md) for logical network segmentation
