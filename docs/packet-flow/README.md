# Packet Flow

## Overview

Understanding packet flow is essential for troubleshooting network issues. This guide walks through the journey of a packet from source to destination, including all encapsulation, switching, routing, and protocol interactions along the way.

## The Complete Journey

When a user types a URL in a browser, multiple protocols and processes work together to deliver the requested content. Let's trace the complete packet flow.

## Scenario: Web Browsing

**Setup:**
```
PC-A (192.168.1.100) → Switch → Router → Internet → Web Server (93.184.216.34)

PC-A Configuration:
  IP: 192.168.1.100/24
  Gateway: 192.168.1.1
  DNS: 8.8.8.8
```

**Goal:** Access http://example.com

## Step-by-Step Packet Flow

### Phase 1: DNS Resolution

**1. User enters "example.com" in browser**

PC-A needs to resolve the domain name to an IP address.

**2. Check local DNS cache**
```
Command: ipconfig /displaydns (Windows)
         systemd-resolve --statistics (Linux)

If cached → Use cached IP
If not → Query DNS server
```

**3. DNS Query (UDP)**
```
Source: 192.168.1.100:52341 (Random high port)
Destination: 8.8.8.8:53 (DNS server)

Application Layer: DNS Query for "example.com"
Transport Layer: UDP header (Source: 52341, Dest: 53)
Network Layer: IP header (Src: 192.168.1.100, Dst: 8.8.8.8)
Data Link Layer: Ethernet header (Src MAC: PC-A, Dst MAC: Gateway)
Physical Layer: Electrical signals
```

**4. ARP Resolution (if needed)**

If PC-A doesn't have Gateway's MAC in ARP cache:
```
PC-A broadcasts: "Who has 192.168.1.1? Tell 192.168.1.100"
Gateway responds: "192.168.1.1 is at AA:BB:CC:DD:EE:FF"
PC-A updates ARP table
```

**5. DNS Query forwarded through router to Internet**

Router performs NAT:
```
Original: Src 192.168.1.100:52341 → Dst 8.8.8.8:53
After NAT: Src 203.0.113.10:52341 → Dst 8.8.8.8:53
```

**6. DNS Response**
```
DNS Server responds:
"example.com = 93.184.216.34"

Response: 8.8.8.8:53 → 203.0.113.10:52341
After NAT: 8.8.8.8:53 → 192.168.1.100:52341
```

PC-A now knows: example.com = 93.184.216.34

### Phase 2: TCP Connection Establishment

**7. Three-Way Handshake**

**Step 1 - SYN:**
```
Application: Browser wants to connect to port 80
Transport: TCP SYN (Src: 52342, Dst: 80, Seq: 1000, Flags: SYN)
Network: IP (Src: 192.168.1.100, Dst: 93.184.216.34)
Data Link: Ethernet (Src: PC-A MAC, Dst: Gateway MAC)

Encapsulation:
[Ethernet Header][IP Header][TCP Header][No Data]
```

**Packet travels:**
```
PC-A → Switch → Router (NAT) → Internet → Web Server
```

**Step 2 - SYN-ACK:**
```
Web Server responds:
Transport: TCP SYN-ACK (Src: 80, Dst: 52342, Seq: 5000, Ack: 1001)
Network: IP (Src: 93.184.216.34, Dst: 203.0.113.10)

After NAT:
Network: IP (Src: 93.184.216.34, Dst: 192.168.1.100)
```

**Step 3 - ACK:**
```
PC-A completes handshake:
Transport: TCP ACK (Src: 52342, Dst: 80, Seq: 1001, Ack: 5001)
```

**Connection Established!**

### Phase 3: HTTP Request

**8. Application Data (HTTP GET)**

```
Application Layer:
GET / HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0...

Transport Layer:
TCP segment (Src: 52342, Dst: 80, Seq: 1001, ACK: 5001, PSH, ACK flags)

Network Layer:
IP packet (Src: 192.168.1.100, Dst: 93.184.216.34, TTL: 64, Protocol: TCP)

Data Link Layer:
Ethernet frame (Src MAC: PC-A, Dst MAC: Gateway, Type: 0x0800 [IPv4])

Physical Layer:
Binary data transmitted as electrical/optical signals
```

### Phase 4: Packet Journey Through Network

**9. Layer 2 Switching (Local Network)**

```
PC-A sends frame:
[Dst MAC: Gateway][Src MAC: PC-A][IP Packet]

Switch Process:
1. Receives frame on port 5
2. Reads Dst MAC (Gateway)
3. Looks up MAC table: Gateway is on port 1
4. Forwards frame out port 1 only
5. Updates MAC table: PC-A on port 5
```

**10. Layer 3 Routing (Router)**

```
Router receives frame:
1. Strips Ethernet header (de-encapsulation)
2. Examines IP destination: 93.184.216.34
3. Consults routing table:
   0.0.0.0/0 → Next hop: ISP (via Gi0/1)
4. Decrements TTL: 64 → 63
5. Performs NAT translation
6. Recalculates checksums
7. Creates new Ethernet header for next hop
8. Forwards packet to ISP
```

**NAT Translation Table:**
```
Inside Local          Inside Global
192.168.1.100:52342 ↔ 203.0.113.10:52342
```

**11. Internet Routing**

Packet hops through multiple routers:
```
Home Router (TTL=64) → 
ISP Router (TTL=63) →
Regional Router (TTL=62) →
Backbone Router (TTL=61) →
... →
Destination Network Router (TTL=52) →
Web Server (TTL=51)

Each router:
- Examines destination IP
- Looks up routing table
- Forwards to next hop
- Decrements TTL
- Recalculates checksum
```

### Phase 5: HTTP Response

**12. Web Server Processing**

```
Web Server receives HTTP request:
1. TCP stack delivers data to web application (port 80)
2. Web server (Apache/Nginx) processes request
3. Retrieves index.html
4. Generates HTTP response
```

**13. Response Packet Creation**

```
Application Layer:
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1256

<!DOCTYPE html>
<html>...

Transport Layer:
TCP segment (Src: 80, Dst: 52342, Seq: 5001, Ack: 1501, PSH, ACK)

Network Layer:
IP packet (Src: 93.184.216.34, Dst: 203.0.113.10, TTL: 64)

Data Link Layer:
Ethernet frame (Src: Server MAC, Dst: Next hop MAC)
```

**14. Return Journey**

Packet travels back through Internet to home router:
```
Web Server → ... → ISP Router → Home Router

Router performs reverse NAT:
Dst: 203.0.113.10:52342 → 192.168.1.100:52342
```

**15. Local Delivery**

```
Router → Switch → PC-A

Switch:
1. Receives frame with Dst MAC: PC-A
2. Looks up MAC table: PC-A on port 5
3. Forwards frame to port 5

PC-A:
1. NIC receives frame
2. Strips Ethernet header
3. Passes to IP layer
4. Strips IP header
5. Passes to TCP layer
6. TCP reassembles segments
7. Delivers to browser (port 52342)
8. Browser renders HTML
```

## Encapsulation and De-Encapsulation

### Encapsulation (Outbound)

```
Layer 7-5: Application Data
           ↓
Layer 4:   [TCP Header][Application Data]
           ↓
Layer 3:   [IP Header][TCP Header][Application Data]
           ↓
Layer 2:   [Eth Header][IP Header][TCP Header][Data][Eth Trailer]
           ↓
Layer 1:   101010101... (Physical transmission)
```

**PDU (Protocol Data Unit) Names:**
- **Application/Presentation/Session:** Data
- **Transport:** Segment (TCP) / Datagram (UDP)
- **Network:** Packet
- **Data Link:** Frame
- **Physical:** Bits

### De-Encapsulation (Inbound)

```
Layer 1:   Receives bits, converts to frame
           ↓
Layer 2:   Strips Ethernet header/trailer → Packet
           ↓
Layer 3:   Strips IP header → Segment
           ↓
Layer 4:   Strips TCP header → Data
           ↓
Layer 7-5: Delivers data to application
```

## ARP (Address Resolution Protocol)

ARP resolves IP addresses to MAC addresses.

### ARP Process

**Scenario:** PC-A (192.168.1.100) wants to send to PC-B (192.168.1.50)

**1. Check ARP Cache**
```
Command: arp -a

If entry exists: Use cached MAC
If not: Send ARP request
```

**2. ARP Request (Broadcast)**
```
Ethernet Frame:
  Dst MAC: FF:FF:FF:FF:FF:FF (Broadcast)
  Src MAC: AA:AA:AA:AA:AA:AA (PC-A)

ARP Packet:
  Operation: Request (1)
  Sender MAC: AA:AA:AA:AA:AA:AA
  Sender IP: 192.168.1.100
  Target MAC: 00:00:00:00:00:00 (Unknown)
  Target IP: 192.168.1.50

Message: "Who has 192.168.1.50? Tell 192.168.1.100"
```

**3. Switch Floods ARP Request**

Switch broadcasts to all ports (except source).

**4. ARP Reply (Unicast)**

PC-B responds:
```
Ethernet Frame:
  Dst MAC: AA:AA:AA:AA:AA:AA (PC-A)
  Src MAC: BB:BB:BB:BB:BB:BB (PC-B)

ARP Packet:
  Operation: Reply (2)
  Sender MAC: BB:BB:BB:BB:BB:BB
  Sender IP: 192.168.1.50
  Target MAC: AA:AA:AA:AA:AA:AA
  Target IP: 192.168.1.100

Message: "192.168.1.50 is at BB:BB:BB:BB:BB:BB"
```

**5. Update ARP Cache**

PC-A adds entry:
```
192.168.1.50 → BB:BB:BB:BB:BB:BB
```

**6. Send Data**

PC-A can now send frames directly to PC-B using its MAC address.

## NAT (Network Address Translation)

NAT translates private IP addresses to public IP addresses.

### NAT Types

**1. Static NAT (One-to-One)**
```
Inside: 192.168.1.100 → Outside: 203.0.113.10
Always the same mapping
```

**2. Dynamic NAT (Pool)**
```
Inside: 192.168.1.0/24 → Outside: 203.0.113.1-254
Temporary mapping from pool
```

**3. PAT (Port Address Translation) - Overload**
```
Inside: 192.168.1.100:52341 → Outside: 203.0.113.10:52341
Inside: 192.168.1.101:52342 → Outside: 203.0.113.10:52342
Inside: 192.168.1.102:52343 → Outside: 203.0.113.10:52343

Many inside addresses share one outside address (different ports)
```

### NAT Translation Process

**Outbound:**
```
1. Internal host sends packet
   Src: 192.168.1.100:52341, Dst: 8.8.8.8:53

2. Router NAT table lookup (if exists, use; if not, create)

3. Translate source address
   Src: 203.0.113.10:52341, Dst: 8.8.8.8:53

4. Update NAT table
   Inside: 192.168.1.100:52341 ↔ Outside: 203.0.113.10:52341

5. Forward packet
```

**Inbound:**
```
1. Router receives response
   Src: 8.8.8.8:53, Dst: 203.0.113.10:52341

2. NAT table lookup
   203.0.113.10:52341 → 192.168.1.100:52341

3. Translate destination
   Src: 8.8.8.8:53, Dst: 192.168.1.100:52341

4. Forward to internal host
```

## VLAN Packet Flow

### Same VLAN Communication

**Scenario:** PC-A (VLAN 10) → PC-B (VLAN 10)

```
1. PC-A sends frame with Dst MAC: PC-B
2. Switch receives on VLAN 10 port
3. MAC table lookup: PC-B on port 5 (VLAN 10)
4. Forward frame out port 5
5. No routing required (same subnet/VLAN)
```

### Different VLAN Communication

**Scenario:** PC-A (VLAN 10) → PC-C (VLAN 20)

```
1. PC-A sends to default gateway (Router/L3 Switch)
2. Frame arrives at VLAN 10 interface
3. Router de-encapsulates, examines IP
4. Routing decision: Forward to VLAN 20
5. Router re-encapsulates for VLAN 20
6. Switch forwards to PC-C on VLAN 20
```

### Trunk Link Packet Flow

**Scenario:** Frame crosses trunk between switches

```
Switch A (VLAN 10) → Trunk → Switch B (VLAN 10)

1. Frame enters Switch A on VLAN 10 access port
2. Switch A forwards to trunk port
3. Switch A adds 802.1Q tag (VLAN ID: 10)
4. Tagged frame travels over trunk
5. Switch B receives tagged frame
6. Switch B reads VLAN tag (10)
7. Switch B strips tag
8. Switch B forwards to VLAN 10 access port
```

## ICMP and Ping

### Ping Process

**Command:** `ping 8.8.8.8`

```
1. OS creates ICMP Echo Request
   Type: 8 (Echo Request)
   Code: 0
   Sequence: 1
   Data: 32 bytes

2. Encapsulation
   [ICMP][IP Header][Ethernet Header]

3. Sent through network

4. Destination receives and responds
   Type: 0 (Echo Reply)
   Code: 0
   Sequence: 1
   Data: Same 32 bytes

5. Round-trip time calculated
   Reply from 8.8.8.8: bytes=32 time=15ms TTL=117
```

### Traceroute Process

**Command:** `traceroute google.com` (Linux) / `tracert google.com` (Windows)

```
How it works:
1. Send packet with TTL=1
   → First router responds "TTL Expired" (ICMP Type 11)
   → Records first hop

2. Send packet with TTL=2
   → Second router responds "TTL Expired"
   → Records second hop

3. Continue incrementing TTL
   → Map each hop

4. Destination reached
   → Echo Reply (Linux uses UDP, gets Port Unreachable)
   → Complete path traced

Output:
 1  192.168.1.1 (Gateway)           1 ms
 2  10.0.0.1 (ISP Router)          10 ms
 3  172.16.0.1 (Regional Router)   15 ms
 ...
```

## Packet Flow Troubleshooting

### Common Issues and Solutions

**1. No Connectivity**

```
Symptom: Cannot reach destination

Troubleshoot:
1. Ping localhost (127.0.0.1) → Test TCP/IP stack
2. Ping own IP → Test NIC
3. Ping default gateway → Test local network
4. Ping remote IP → Test routing
5. Ping remote hostname → Test DNS

Identifies layer where problem occurs
```

**2. Routing Loop**

```
Symptom: Packet circulates endlessly, TTL expires

Traceroute shows:
 1  router-a
 2  router-b
 3  router-a
 4  router-b
 (repeating...)

Solution: Fix routing tables
```

**3. MAC Address Table Issues**

```
Symptom: Frames not reaching destination on switch

Check:
1. show mac address-table → Verify MAC entries
2. clear mac address-table → Force relearning
3. Check port status → Verify interfaces up
```

**4. VLAN Mismatch**

```
Symptom: Devices in same VLAN cannot communicate

Check:
1. Verify VLAN assignments
2. Check trunk configuration
3. Verify native VLAN matches
4. Check VLAN exists on both switches
```

## Tools for Analyzing Packet Flow

### 1. Wireshark
- Capture and analyze packets
- View all protocol layers
- Filter specific traffic

### 2. tcpdump
- Command-line packet capture
- Useful on servers/routers

### 3. NetFlow
- Network traffic analysis
- Flow monitoring

### 4. Debug Commands (Cisco)
```
debug ip packet
debug ip icmp
debug arp
```

## Summary

- **Packet flow** involves multiple layers and protocols
- **Encapsulation** adds headers at each layer (outbound)
- **De-encapsulation** strips headers at each layer (inbound)
- **ARP** resolves IP to MAC addresses
- **NAT** translates private to public IPs
- **DNS** resolves hostnames to IP addresses
- **Switching** forwards based on MAC addresses (Layer 2)
- **Routing** forwards based on IP addresses (Layer 3)
- **Understanding packet flow** is essential for troubleshooting

## Practice Scenarios

1. Trace a packet from your PC to www.google.com
2. Explain what happens when you ping your default gateway
3. Describe the difference between a packet crossing a switch vs. a router
4. Explain how ARP reduces broadcast traffic
5. Trace an HTTP request through a NAT router

## Next Steps

- Study [OSI Model](../osi-model/README.md) for framework understanding
- Review [TCP/UDP](../tcp-udp/README.md) for transport layer details
- Practice with [Wireshark](../wireshark/README.md) to capture real packet flows
