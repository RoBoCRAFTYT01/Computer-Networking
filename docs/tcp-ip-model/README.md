# TCP/IP Model

## Overview

The **TCP/IP Model** (also called Internet Protocol Suite) is a practical, simplified networking model developed by the Department of Defense. It consists of four layers and is the foundation of the modern Internet.

## The Four Layers

```
┌─────────────────────────────────────────┐
│  4. Application Layer                   │  ← Combines OSI 5-7
├─────────────────────────────────────────┤
│  3. Transport Layer                     │  ← OSI Layer 4
├─────────────────────────────────────────┤
│  2. Internet Layer                      │  ← OSI Layer 3
├─────────────────────────────────────────┤
│  1. Network Access/Link Layer           │  ← Combines OSI 1-2
└─────────────────────────────────────────┘
```

## TCP/IP vs. OSI Model

```
OSI Model (7 Layers)          TCP/IP Model (4 Layers)

7. Application     ┐
6. Presentation    ├──────────► 4. Application
5. Session         ┘

4. Transport       ──────────► 3. Transport

3. Network         ──────────► 2. Internet

2. Data Link       ┐
1. Physical        └──────────► 1. Network Access
```

| Aspect | OSI Model | TCP/IP Model |
|--------|-----------|--------------|
| Layers | 7 | 4 |
| Development | ISO (theoretical) | DoD (practical) |
| Approach | Protocol-independent | Protocol-specific |
| Usage | Reference model | Implementation model |
| Session/Presentation | Separate layers | Part of Application |
| Physical/Data Link | Separate layers | Combined in Network Access |

## Layer 1: Network Access (Link) Layer

### Purpose

Combines OSI Physical and Data Link layers. Handles physical transmission and framing.

### Functions

- **Physical addressing** (MAC addresses)
- **Media access control**
- **Frame formatting**
- **Error detection** (not correction)
- **Physical transmission** (bits on wire)

### Components

**Hardware:**
- Network Interface Cards (NICs)
- Switches
- Bridges
- Hubs (legacy)
- Cables (Ethernet, fiber)
- Wireless access points

### Protocols and Technologies

**LAN Technologies:**
- **Ethernet (IEEE 802.3)**
  - 10BASE-T (10 Mbps)
  - 100BASE-TX (Fast Ethernet - 100 Mbps)
  - 1000BASE-T (Gigabit Ethernet - 1 Gbps)
  - 10GBASE-T (10 Gigabit Ethernet)

- **Wi-Fi (IEEE 802.11)**
  - 802.11a (54 Mbps, 5 GHz)
  - 802.11b (11 Mbps, 2.4 GHz)
  - 802.11g (54 Mbps, 2.4 GHz)
  - 802.11n (600 Mbps, 2.4/5 GHz)
  - 802.11ac (1.3+ Gbps, 5 GHz)
  - 802.11ax (Wi-Fi 6, 9.6 Gbps, 2.4/5/6 GHz)

**WAN Technologies:**
- **PPP** (Point-to-Point Protocol)
- **HDLC** (High-Level Data Link Control)
- **Frame Relay** (legacy)
- **ATM** (Asynchronous Transfer Mode - legacy)
- **MPLS** (Multiprotocol Label Switching)

### Addressing

**MAC Address:** 48-bit hardware address
```
Format: AA:BB:CC:DD:EE:FF
Example: 00:1A:2B:3C:4D:5E

Structure:
[OUI - 24 bits][Device ID - 24 bits]
 Vendor ID        Unique identifier
```

### Frame Structure (Ethernet)

```
┌──────────┬──────────┬──────────┬──────┬──────────┬─────┐
│ Preamble │ Dst MAC  │ Src MAC  │ Type │   Data   │ FCS │
│ (8 bytes)│ (6 bytes)│ (6 bytes)│(2 B) │(46-1500) │(4 B)│
└──────────┴──────────┴──────────┴──────┴──────────┴─────┘
```

### Responsibility

This layer ensures:
- Frames delivered on local network
- Proper physical connection
- Error detection in frames
- Media access control (CSMA/CD, CSMA/CA)

## Layer 2: Internet Layer

### Purpose

Corresponds to OSI Network Layer. Handles logical addressing, routing, and packet forwarding across networks.

### Functions

- **Logical addressing** (IP addresses)
- **Routing** (path determination)
- **Packet forwarding**
- **Fragmentation** and reassembly
- **Error reporting** (ICMP)

### Core Protocols

**1. IP (Internet Protocol)**

**IPv4:**
```
Version: 4
Address: 32-bit (192.168.1.1)
Address Space: ~4.3 billion addresses
Header: 20-60 bytes (variable)
```

**IPv6:**
```
Version: 6
Address: 128-bit (2001:db8::1)
Address Space: 340 undecillion addresses
Header: 40 bytes (fixed)
```

**IP Functions:**
- Packet addressing
- Packet routing
- Fragmentation/reassembly
- Best-effort delivery (unreliable)

**2. ICMP (Internet Control Message Protocol)**

**Purpose:** Error reporting and diagnostics

**Common ICMP Messages:**
```
Type 0:  Echo Reply (ping response)
Type 3:  Destination Unreachable
Type 5:  Redirect
Type 8:  Echo Request (ping)
Type 11: Time Exceeded (TTL=0)
```

**Tools using ICMP:**
- **ping** - Tests connectivity
- **traceroute** - Maps network path

**3. ARP (Address Resolution Protocol)**

**Purpose:** Resolves IP addresses to MAC addresses

**Process:**
```
1. Host needs MAC for IP 192.168.1.50
2. Broadcasts: "Who has 192.168.1.50?"
3. Target responds: "192.168.1.50 is at AA:BB:CC:DD:EE:FF"
4. Host caches mapping in ARP table
```

**4. IGMP (Internet Group Management Protocol)**

**Purpose:** Manages multicast group memberships

**Use Cases:**
- Streaming video to multiple hosts
- IPTV
- Stock tickers
- Video conferencing

### Routing Protocols

**Interior Gateway Protocols (IGP):**
- **RIP** (Routing Information Protocol)
  - Distance vector
  - Hop count metric (max 15)
  - Simple, limited scalability

- **OSPF** (Open Shortest Path First)
  - Link state
  - Cost metric (bandwidth-based)
  - Scalable, fast convergence

- **EIGRP** (Enhanced Interior Gateway Routing Protocol)
  - Advanced distance vector
  - Composite metric
  - Cisco-developed (now open)

**Exterior Gateway Protocol (EGP):**
- **BGP** (Border Gateway Protocol)
  - Path vector
  - Internet backbone routing
  - Policy-based routing

### IP Packet Header

```
IPv4 Header (Simplified):
┌─────────────────────────────────────────┐
│ Version(4) | IHL(4) | TOS(8) | Len(16) │
├─────────────────────────────────────────┤
│   ID(16)    | Flags(3) | Offset(13)    │
├─────────────────────────────────────────┤
│   TTL(8)    | Proto(8) | Checksum(16)  │
├─────────────────────────────────────────┤
│         Source IP Address (32)          │
├─────────────────────────────────────────┤
│      Destination IP Address (32)        │
└─────────────────────────────────────────┘

Key Fields:
- TTL: Time to Live (hop limit)
- Protocol: 1=ICMP, 6=TCP, 17=UDP
- Source/Dest IP: Logical addresses
```

## Layer 3: Transport Layer

### Purpose

Corresponds to OSI Transport Layer. Provides end-to-end communication, reliability, and flow control.

### Functions

- **Segmentation** and reassembly
- **Port addressing** (application identification)
- **Connection management**
- **Flow control**
- **Error detection and recovery**
- **Multiplexing/demultiplexing**

### Core Protocols

**1. TCP (Transmission Control Protocol)**

**Characteristics:**
- **Connection-oriented** - Three-way handshake
- **Reliable** - Guaranteed delivery
- **Ordered** - Sequential delivery
- **Flow control** - Window-based
- **Error recovery** - Retransmission
- **Full-duplex** - Bidirectional communication

**TCP Header:**
```
┌──────────────────┬──────────────────┐
│   Source Port    │ Destination Port │
│      (16)        │       (16)       │
├──────────────────┴──────────────────┤
│         Sequence Number (32)        │
├─────────────────────────────────────┤
│      Acknowledgment Number (32)     │
├─────┬─────┬──────┬──────────────────┤
│Offset│Rsvd│Flags │   Window Size    │
│ (4) │(4) │ (8)  │      (16)        │
├─────┴─────┴──────┼──────────────────┤
│    Checksum(16)  │ Urgent Ptr (16)  │
└──────────────────┴──────────────────┘

Flags: URG, ACK, PSH, RST, SYN, FIN
```

**TCP Three-Way Handshake:**
```
Client                    Server
  |                         |
  |-------- SYN ----------->|  Step 1
  |                         |
  |<----- SYN-ACK ----------|  Step 2
  |                         |
  |-------- ACK ----------->|  Step 3
  |                         |
  | Connection Established  |
```

**Use Cases:**
- HTTP/HTTPS (web browsing)
- FTP (file transfer)
- SSH (secure shell)
- SMTP/POP3/IMAP (email)
- Telnet

**2. UDP (User Datagram Protocol)**

**Characteristics:**
- **Connectionless** - No handshake
- **Unreliable** - Best-effort delivery
- **Unordered** - No sequence guarantee
- **Low overhead** - 8-byte header
- **Fast** - No connection setup
- **No flow control** - Sends at will

**UDP Header:**
```
┌──────────────────┬──────────────────┐
│   Source Port    │ Destination Port │
│      (16)        │       (16)       │
├──────────────────┼──────────────────┤
│   Length (16)    │   Checksum (16)  │
└──────────────────┴──────────────────┘

Total: 8 bytes (vs TCP's 20+ bytes)
```

**Use Cases:**
- DNS (domain resolution)
- DHCP (IP configuration)
- SNMP (network management)
- VoIP (voice calls)
- Video streaming
- Online gaming
- TFTP (trivial FTP)

### Port Numbers

**Well-Known Ports (0-1023):**
```
20/21  - FTP (TCP)
22     - SSH (TCP)
23     - Telnet (TCP)
25     - SMTP (TCP)
53     - DNS (TCP/UDP)
67/68  - DHCP (UDP)
80     - HTTP (TCP)
110    - POP3 (TCP)
143    - IMAP (TCP)
443    - HTTPS (TCP)
3389   - RDP (TCP)
```

**Registered Ports (1024-49151):**
- Application-specific
- Requires registration with IANA

**Dynamic/Private Ports (49152-65535):**
- Temporary client ports
- Ephemeral ports

### Socket

**Definition:** IP Address + Port Number

**Format:** `IP:Port`

**Examples:**
```
192.168.1.100:80    (Web server on client)
8.8.8.8:53          (Google DNS)
10.0.0.50:22        (SSH server)
```

**Connection Identification:**
```
Client Socket: 192.168.1.100:52341
Server Socket: 93.184.216.34:443

Unique connection: 192.168.1.100:52341 ↔ 93.184.216.34:443
```

## Layer 4: Application Layer

### Purpose

Combines OSI layers 5-7 (Session, Presentation, Application). Provides network services to end-user applications.

### Functions

- **User interface** to network services
- **Application protocols**
- **Data representation**
- **Encryption/decryption**
- **Compression**
- **Session management**

### Protocol Categories

**1. Web Services**

**HTTP (HyperText Transfer Protocol):**
- Port 80
- Web page transfer
- Request-response model
- Stateless

**HTTPS (HTTP Secure):**
- Port 443
- Encrypted HTTP (using TLS/SSL)
- Secure web transactions

**2. File Transfer**

**FTP (File Transfer Protocol):**
- Ports 20 (data) and 21 (control)
- Full-featured file transfer
- Authentication required

**TFTP (Trivial FTP):**
- Port 69 (UDP)
- Simple file transfer
- No authentication
- Used for network device configs

**SFTP (SSH FTP):**
- Port 22
- Secure file transfer over SSH
- Encrypted

**3. Email**

**SMTP (Simple Mail Transfer Protocol):**
- Port 25 (or 587 for submission)
- Sending email
- Mail server to mail server

**POP3 (Post Office Protocol v3):**
- Port 110
- Retrieving email
- Downloads and deletes from server

**IMAP (Internet Message Access Protocol):**
- Port 143
- Retrieving email
- Keeps messages on server
- Multiple device access

**4. Name Resolution**

**DNS (Domain Name System):**
- Port 53 (UDP for queries, TCP for zone transfers)
- Translates domain names to IP addresses
- Hierarchical distributed database

**Example:**
```
Query: "What's the IP for www.google.com?"
Response: "142.250.185.78"
```

**5. Network Management**

**SNMP (Simple Network Management Protocol):**
- Ports 161 (agent), 162 (trap)
- Network device monitoring and management
- Get/Set/Trap operations

**6. Remote Access**

**Telnet:**
- Port 23
- Unencrypted remote terminal
- Legacy (insecure)

**SSH (Secure Shell):**
- Port 22
- Encrypted remote terminal
- Secure alternative to Telnet
- Also used for SFTP, SCP

**7. Dynamic Configuration**

**DHCP (Dynamic Host Configuration Protocol):**
- Ports 67 (server), 68 (client)
- Automatic IP configuration
- DORA process (Discover, Offer, Request, Acknowledge)

**8. Time Synchronization**

**NTP (Network Time Protocol):**
- Port 123
- Clock synchronization
- Hierarchical stratum levels

**9. Directory Services**

**LDAP (Lightweight Directory Access Protocol):**
- Port 389 (636 for LDAPS)
- Directory information access
- User authentication

### Application Layer Protocols Summary

| Protocol | Port | Transport | Purpose |
|----------|------|-----------|---------|
| HTTP | 80 | TCP | Web browsing |
| HTTPS | 443 | TCP | Secure web |
| FTP | 20/21 | TCP | File transfer |
| SFTP | 22 | TCP | Secure file transfer |
| TFTP | 69 | UDP | Simple file transfer |
| SMTP | 25 | TCP | Send email |
| POP3 | 110 | TCP | Retrieve email |
| IMAP | 143 | TCP | Retrieve email |
| DNS | 53 | UDP/TCP | Name resolution |
| DHCP | 67/68 | UDP | IP configuration |
| SNMP | 161/162 | UDP | Network management |
| SSH | 22 | TCP | Secure remote access |
| Telnet | 23 | TCP | Remote access |
| NTP | 123 | UDP | Time sync |

## Data Encapsulation in TCP/IP

### Sending Process (Top to Bottom)

```
Application Layer:
  [HTTP Request Data]
         ↓
Transport Layer:
  [TCP Header][HTTP Data]
  → TCP Segment
         ↓
Internet Layer:
  [IP Header][TCP Header][HTTP Data]
  → IP Packet
         ↓
Network Access Layer:
  [Ethernet Header][IP Header][TCP Header][HTTP Data][Trailer]
  → Ethernet Frame
         ↓
Physical Transmission:
  10101010... → Bits
```

### Receiving Process (Bottom to Top)

```
Physical: Receives bits
         ↓
Network Access: Strips Ethernet header/trailer
         ↓ Frame → Packet
Internet: Strips IP header, checks routing
         ↓ Packet → Segment
Transport: Strips TCP header, reassembles
         ↓ Segment → Data
Application: Processes HTTP data
```

## TCP/IP Protocol Suite

### Complete Protocol Stack

```
Application Layer:
├── HTTP/HTTPS
├── FTP/SFTP/TFTP
├── SMTP/POP3/IMAP
├── DNS
├── DHCP
├── SNMP
├── SSH/Telnet
├── NTP
└── LDAP

Transport Layer:
├── TCP (connection-oriented, reliable)
└── UDP (connectionless, fast)

Internet Layer:
├── IP (IPv4/IPv6) - Packet delivery
├── ICMP - Error reporting
├── ARP - Address resolution
├── IGMP - Multicast management
└── Routing Protocols (RIP, OSPF, EIGRP, BGP)

Network Access Layer:
├── Ethernet (802.3)
├── Wi-Fi (802.11)
├── PPP
├── HDLC
└── Frame Relay
```

## Practical Example: Web Browsing

**User visits https://www.example.com**

```
Application Layer:
- Browser (HTTP client) wants secure connection
- Initiates HTTPS (HTTP over TLS)

Transport Layer:
- TCP three-way handshake (SYN, SYN-ACK, ACK)
- TLS handshake for encryption
- Establishes connection to port 443

Internet Layer:
- DNS query (UDP to resolve domain name)
  "www.example.com" → "93.184.216.34"
- IP packets created
  Source: 192.168.1.100
  Destination: 93.184.216.34

Network Access Layer:
- ARP to find gateway MAC address
- Ethernet frames created
- Physical transmission over network cable
```

## TCP/IP Model Advantages

**1. Practical Implementation:**
- Based on real protocols
- Used in actual networks
- Internet foundation

**2. Simplified:**
- 4 layers vs 7 layers
- Easier to understand
- Less abstract

**3. Protocol-Specific:**
- Designed around TCP/IP suite
- Reflects real-world usage

**4. Flexible:**
- Accommodates various technologies
- Vendor-independent

## Comparison Summary

| Feature | OSI Model | TCP/IP Model |
|---------|-----------|--------------|
| **Layers** | 7 | 4 |
| **Origin** | ISO | DoD/DARPA |
| **Purpose** | Reference/educational | Implementation |
| **Approach** | Protocol-independent | Built around TCP/IP |
| **Session Layer** | Separate | Part of Application |
| **Presentation** | Separate | Part of Application |
| **Network Access** | Two layers (Physical + Data Link) | Single layer |
| **Usage** | Conceptual framework | Actual implementation |
| **Adoption** | Academic/reference | Internet/practical |

## Best Practices

1. **Understand both models:**
   - OSI for troubleshooting framework
   - TCP/IP for implementation

2. **Use appropriate protocols:**
   - TCP for reliability
   - UDP for speed

3. **Secure applications:**
   - Use encrypted versions (HTTPS, SFTP, SSH)
   - Avoid legacy protocols (Telnet, FTP)

4. **Proper addressing:**
   - Plan IP address scheme
   - Use private addresses internally
   - Implement NAT properly

5. **Monitor network:**
   - Use SNMP for management
   - Implement logging
   - Analyze traffic patterns

## Summary

- **TCP/IP Model** has 4 layers vs OSI's 7
- **Network Access** combines Physical + Data Link
- **Internet Layer** handles routing (IP, ICMP, ARP)
- **Transport Layer** provides end-to-end delivery (TCP, UDP)
- **Application Layer** combines Session + Presentation + Application
- **TCP** for reliable, connection-oriented communication
- **UDP** for fast, connectionless communication
- **TCP/IP** is the foundation of the Internet

## Practice Questions

1. How many layers in the TCP/IP model? (Answer: 4)
2. Which layer handles IP addressing? (Answer: Internet Layer)
3. What are the two main transport protocols? (Answer: TCP and UDP)
4. Which protocol resolves domain names? (Answer: DNS)
5. What port does HTTPS use? (Answer: 443)

## Next Steps

- Compare with [OSI Model](../osi-model/README.md) for detailed layer breakdown
- Study [TCP/UDP](../tcp-udp/README.md) for transport layer details
- Review [Packet Flow](../packet-flow/README.md) to see TCP/IP in action
