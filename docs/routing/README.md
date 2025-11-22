# Routing Fundamentals

## Overview

Routing is the process of selecting paths in a network along which to send network traffic. Routers use routing tables to determine the best path for forwarding packets to their destination.

## What is a Router?

A router is a network device that:
- **Connects different networks** (LANs, WANs, Internet)
- **Makes forwarding decisions** based on Layer 3 (IP) addresses
- **Maintains a routing table** with network paths
- **Determines the best path** using routing protocols and metrics

### Router Functions

```
┌─────────────────────────────────────┐
│  Router Core Functions              │
├─────────────────────────────────────┤
│  1. Path Determination              │
│  2. Packet Forwarding               │
│  3. Routing Table Maintenance       │
│  4. Network Interconnection         │
└─────────────────────────────────────┘
```

## Routing Table

A routing table contains information about:
- **Destination networks**
- **Next-hop addresses** (where to send packets)
- **Interface** to use for forwarding
- **Metric** (cost) of the route

### Sample Routing Table

```
Network Destination    Netmask          Gateway         Interface     Metric
0.0.0.0               0.0.0.0          192.168.1.1     192.168.1.10    10
10.0.0.0              255.255.255.0    10.0.0.1        10.0.0.2        1
172.16.0.0            255.255.0.0      192.168.1.254   192.168.1.10    20
192.168.1.0           255.255.255.0    On-link         192.168.1.10    1
```

**Key Fields:**
- **Network Destination:** Target network
- **Netmask:** Subnet mask for the destination
- **Gateway:** Next hop router IP (or "On-link" for directly connected)
- **Interface:** Local interface to send packets through
- **Metric:** Cost of the route (lower is better)

## Types of Routes

### 1. Directly Connected Routes

Automatically created when an interface is configured and active.

```
Example:
Router interface: 192.168.1.1/24
Directly connected: 192.168.1.0/24
```

**Characteristics:**
- **Administrative Distance (AD):** 0
- **Automatically added** when interface comes up
- **Most trusted** route type
- **No next hop** required (directly attached)

### 2. Static Routes

Manually configured by network administrator.

**Configuration Example (Cisco):**
```
Router(config)# ip route 10.2.0.0 255.255.255.0 192.168.1.2
                         [destination] [mask]  [next-hop]
```

**Advantages:**
- Predictable and secure
- No routing protocol overhead
- Complete control over paths
- Suitable for small networks

**Disadvantages:**
- Manual configuration required
- No automatic failover
- Difficult to maintain in large networks
- No dynamic adaptation to changes

### 3. Dynamic Routes

Learned automatically through routing protocols.

**Advantages:**
- Automatic network discovery
- Adapts to topology changes
- Scales well in large networks
- Provides redundancy and failover

**Disadvantages:**
- Uses bandwidth for updates
- More complex configuration
- Requires more processing power
- Potential security concerns

## Static vs. Dynamic Routing

| Aspect | Static Routing | Dynamic Routing |
|--------|---------------|-----------------|
| Configuration | Manual | Automatic |
| Scalability | Poor (small networks) | Excellent (large networks) |
| Resource Usage | Minimal | Higher (CPU, memory, bandwidth) |
| Convergence | None (manual change) | Automatic |
| Fault Tolerance | Manual intervention | Automatic rerouting |
| Security | More secure | Potentially less secure |
| Complexity | Simple | More complex |
| Best Use | Small, stable networks | Large, changing networks |

## Default Gateway

The default gateway is the router that a device uses to communicate with devices on other networks.

### How It Works

```
Host (192.168.1.100) wants to reach Internet (8.8.8.8)
      ↓
Checks if destination is on local network (it's not)
      ↓
Sends packet to default gateway (192.168.1.1)
      ↓
Gateway routes packet toward destination
```

**Configuration Example:**
```
Windows: ipconfig /all
Linux:   ip route show default
         route -n
```

## Routing Protocols

Routing protocols enable routers to dynamically share network information and calculate the best paths.

### Classification

```
Routing Protocols
├── Interior Gateway Protocols (IGP)
│   ├── Distance Vector
│   │   ├── RIP (Routing Information Protocol)
│   │   └── EIGRP (Enhanced Interior Gateway Routing Protocol)
│   └── Link State
│       └── OSPF (Open Shortest Path First)
└── Exterior Gateway Protocols (EGP)
    └── BGP (Border Gateway Protocol)
```

### Distance Vector vs. Link State

| Feature | Distance Vector | Link State |
|---------|----------------|------------|
| Algorithm | Bellman-Ford | Dijkstra (SPF) |
| Information | Distance and direction | Complete topology map |
| Updates | Periodic | Event-triggered |
| Convergence | Slower | Faster |
| Resources | Lower | Higher |
| Scalability | Limited | Better |
| Examples | RIP, EIGRP | OSPF, IS-IS |

## RIP (Routing Information Protocol)

### Overview
- **Type:** Distance Vector
- **Metric:** Hop count
- **Maximum hops:** 15 (16 = unreachable)
- **Updates:** Every 30 seconds (broadcast/multicast)
- **AD:** 120

### RIP Versions

**RIPv1:**
- Classful routing (no subnet masks)
- Broadcast updates (255.255.255.255)
- No authentication

**RIPv2:**
- Classless routing (supports VLSM/CIDR)
- Multicast updates (224.0.0.9)
- Supports authentication
- Includes subnet mask in updates

### Configuration Example

```
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.0.0
Router(config-router)# no auto-summary
```

### RIP Timers

- **Update Timer:** 30 seconds (regular updates)
- **Invalid Timer:** 180 seconds (route marked invalid)
- **Holddown Timer:** 180 seconds (prevents routing loops)
- **Flush Timer:** 240 seconds (route removed)

### Advantages and Disadvantages

**Advantages:**
- Simple configuration
- Works in small networks
- Widely supported
- Low resource usage

**Disadvantages:**
- Limited to 15 hops
- Slow convergence
- Inefficient (periodic updates)
- Poor scalability

## OSPF (Open Shortest Path First)

### Overview
- **Type:** Link State
- **Metric:** Cost (based on bandwidth)
- **Algorithm:** Dijkstra's Shortest Path First
- **Updates:** Event-triggered (Link State Advertisements)
- **AD:** 110

### OSPF Concepts

**Areas:**
- OSPF networks divided into areas
- **Area 0 (Backbone):** All areas must connect to Area 0
- Reduces routing updates and speeds convergence

**Router Types:**
```
┌──────────────────────────────────────┐
│  OSPF Router Types                   │
├──────────────────────────────────────┤
│  Internal Router: All interfaces in  │
│                   same area          │
│  Backbone Router: Interface in Area 0│
│  ABR (Area Border Router):           │
│                   Connects areas     │
│  ASBR (Autonomous System Border):    │
│                   Connects to other  │
│                   routing domains    │
└──────────────────────────────────────┘
```

**Neighbor States:**
1. Down
2. Init
3. Two-Way
4. ExStart
5. Exchange
6. Loading
7. Full (fully adjacent)

### OSPF Metric Calculation

```
Cost = Reference Bandwidth / Interface Bandwidth

Default Reference Bandwidth = 100 Mbps (legacy)
Recommended: 10,000 Mbps or higher for modern networks

Examples with default (100 Mbps):
  FastEthernet (100 Mbps): 100/100 = 1
  Gigabit Ethernet (1 Gbps): 100/1000 = 1 (rounds to 1 - not ideal!)
  Serial (1.544 Mbps): 100/1.544 = 64

Examples with modern reference (10,000 Mbps):
  FastEthernet (100 Mbps): 10000/100 = 100
  Gigabit Ethernet (1 Gbps): 10000/1000 = 10
  10G Ethernet (10 Gbps): 10000/10000 = 1

Configure modern reference bandwidth:
Router(config-router)# auto-cost reference-bandwidth 10000
```

### Configuration Example

```
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 10.0.0.0 0.255.255.255 area 1
Router(config-router)# passive-interface GigabitEthernet0/0
```

### Advantages and Disadvantages

**Advantages:**
- Fast convergence
- Scalable (hierarchical design)
- No hop count limit
- Efficient updates (only changes)
- Supports VLSM/CIDR

**Disadvantages:**
- Complex configuration
- Higher resource usage
- More difficult troubleshooting
- Requires planning (area design)

## EIGRP (Enhanced Interior Gateway Routing Protocol)

### Overview
- **Type:** Advanced Distance Vector (Hybrid)
- **Metric:** Composite (bandwidth, delay, load, reliability)
- **Algorithm:** DUAL (Diffusing Update Algorithm)
- **Updates:** Incremental, triggered
- **AD:** 90 (internal), 170 (external)
- **Cisco Proprietary** (was, now open standard)

### EIGRP Features

**Key Characteristics:**
- Rapid convergence
- Support for VLSM/CIDR
- Multicast updates (224.0.0.10)
- Reduced bandwidth usage
- Unequal cost load balancing
- Support for multiple protocols (IP, IPv6)

### EIGRP Metric

```
Default Metric = [(K1 × Bandwidth) + (K3 × Delay)] × 256

Where:
  K1 = 1 (Bandwidth weight)
  K3 = 1 (Delay weight)
  K2, K4, K5 = 0 (Load, Reliability, MTU - typically not used)

Bandwidth = (10^7 / slowest link in Kbps)
Delay = Sum of delays in tens of microseconds
```

### Configuration Example

```
Router(config)# router eigrp 100
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.0.0
Router(config-router)# no auto-summary
Router(config-router)# eigrp router-id 1.1.1.1
```

### EIGRP Terms

- **Successor:** Best route to destination (in routing table)
- **Feasible Successor:** Backup route (meets feasibility condition)
- **Feasible Distance (FD):** Best metric to destination
- **Reported Distance (RD):** Neighbor's metric to destination

## Administrative Distance (AD)

Administrative Distance determines which routing source is trusted when multiple sources provide routes to the same destination.

### AD Values (Lower = More Trusted)

| Route Source | AD Value |
|-------------|----------|
| Directly Connected | 0 |
| Static Route | 1 |
| EIGRP Summary | 5 |
| External BGP | 20 |
| Internal EIGRP | 90 |
| IGRP | 100 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| External EIGRP | 170 |
| Internal BGP | 200 |
| Unknown | 255 (not trusted) |

### Example

```
If a router learns about 10.0.0.0/24 from:
  - Static route (AD 1)
  - OSPF (AD 110)
  - RIP (AD 120)

The static route is chosen (lowest AD).
```

## Routing Metrics

Different protocols use different metrics to determine the best path.

| Protocol | Metric | Description |
|----------|--------|-------------|
| RIP | Hop Count | Number of routers to destination |
| OSPF | Cost | Based on bandwidth (100 Mbps / interface speed) |
| EIGRP | Composite | Bandwidth + Delay (default) |
| BGP | Path Attributes | AS path, local preference, etc. |

## Routing Process

### How Routers Forward Packets

```
1. Packet arrives at router interface
     ↓
2. Router examines destination IP
     ↓
3. Router consults routing table
     ↓
4. Router finds best match (longest prefix match)
     ↓
5. Router forwards packet out appropriate interface
     ↓
6. Router decrements TTL, recalculates checksum
     ↓
7. Packet sent to next hop
```

### Longest Prefix Match

When multiple routes match a destination, the router chooses the most specific (longest prefix).

**Example:**
```
Destination: 192.168.1.50

Routing Table:
  192.168.0.0/16     → Gateway A
  192.168.1.0/24     → Gateway B
  0.0.0.0/0          → Gateway C (default)

Best match: 192.168.1.0/24 (most specific)
Packet sent to Gateway B
```

## Common Routing Scenarios

### Scenario 1: Default Route

Used when no specific route exists for a destination.

```
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

**Purpose:**
- Gateway to the Internet
- Catch-all for unknown networks
- Reduces routing table size

### Scenario 2: Floating Static Route

A backup static route with higher AD than dynamic routes.

```
Router(config)# ip route 10.0.0.0 255.255.255.0 192.168.1.2 100
```

**Use Case:**
- Primary: OSPF route (AD 110 by default)
- Backup: Static route with AD 100
- If OSPF fails, static route activates

### Scenario 3: Load Balancing

Distributing traffic across multiple paths.

**Equal-Cost Load Balancing:**
```
Two routes with same metric → traffic distributed equally
```

**Unequal-Cost Load Balancing:**
```
EIGRP supports variance command
Router(config-router)# variance 2
```

## Troubleshooting Routing

### Common Commands

**Cisco IOS:**
```
show ip route                    # View routing table
show ip route [network]          # Specific route
show ip protocols                # Routing protocol status
show ip ospf neighbor            # OSPF neighbors
show ip eigrp neighbors          # EIGRP neighbors
debug ip routing                 # Debug routing events
traceroute [destination]         # Trace packet path
```

**Linux:**
```
ip route show                    # View routing table
route -n                         # Numeric routing table
traceroute [destination]         # Trace path
netstat -r                       # Routing table
```

### Common Issues

1. **Missing Route:**
   - Check routing table: `show ip route`
   - Verify routing protocol configuration
   - Check interface status

2. **Wrong Next Hop:**
   - Verify routing table entries
   - Check for more specific routes
   - Validate static routes

3. **Routing Loop:**
   - Packets circulate between routers
   - TTL expires
   - Check for conflicting routes

4. **Asymmetric Routing:**
   - Forward and return paths differ
   - Can cause issues with stateful firewalls
   - Verify routing tables on all routers

## Best Practices

1. **Use appropriate routing protocol**
   - Small networks: Static or RIP
   - Medium networks: EIGRP
   - Large networks: OSPF

2. **Implement route summarization**
   - Reduces routing table size
   - Improves stability
   - Speeds convergence

3. **Use default routes wisely**
   - Simplifies edge router configuration
   - Points to ISP or Internet gateway

4. **Document network topology**
   - Keep routing diagrams current
   - Document static routes
   - Note any special configurations

5. **Monitor routing table size**
   - Large tables slow lookups
   - Consider summarization
   - Implement route filtering

6. **Secure routing protocols**
   - Enable authentication
   - Use MD5 or stronger
   - Prevent route poisoning

## Summary

- **Routing** connects different networks and determines packet paths
- **Routing tables** contain network destinations and next hops
- **Static routing** is manual but predictable
- **Dynamic routing** adapts automatically to changes
- **RIP** is simple but limited (distance vector, 15 hop max)
- **OSPF** is scalable and fast (link state, hierarchical)
- **EIGRP** is efficient and rapid (advanced distance vector)
- **Administrative Distance** determines route preference
- **Metrics** vary by protocol (hops, cost, composite)

## Practice Questions

1. What is the AD of OSPF? (Answer: 110)
2. What metric does RIP use? (Answer: Hop count)
3. What is the maximum hop count for RIP? (Answer: 15)
4. Which protocol uses the DUAL algorithm? (Answer: EIGRP)
5. What is a default route? (Answer: 0.0.0.0/0)

## Next Steps

- Review [Switching](../switching/README.md) for Layer 2 operations
- Study [VLANs](../vlans/README.md) for network segmentation
- Explore [Packet Flow](../packet-flow/README.md) to see routing in action
