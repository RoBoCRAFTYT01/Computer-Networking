# OSI Model

## Overview

The **OSI (Open Systems Interconnection) Model** is a conceptual framework that standardizes the functions of a telecommunication or computing system into seven abstraction layers. It helps understand and troubleshoot network communications.

## The Seven Layers

```
┌─────────────────────────────────────────┐
│  7. Application Layer                   │  ← User Interaction
├─────────────────────────────────────────┤
│  6. Presentation Layer                  │  ← Data Translation
├─────────────────────────────────────────┤
│  5. Session Layer                       │  ← Session Management
├─────────────────────────────────────────┤
│  4. Transport Layer                     │  ← End-to-End Connection
├─────────────────────────────────────────┤
│  3. Network Layer                       │  ← Routing & Addressing
├─────────────────────────────────────────┤
│  2. Data Link Layer                     │  ← Frame Delivery
├─────────────────────────────────────────┤
│  1. Physical Layer                      │  ← Physical Transmission
└─────────────────────────────────────────┘
```

**Mnemonic (Bottom to Top):** 
- **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way
- **P**hysical, **D**ata Link, **N**etwork, **T**ransport, **S**ession, **P**resentation, **A**pplication

**Mnemonic (Top to Bottom):**
- **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

## Layer 1: Physical Layer

### Purpose
Transmits raw bits over a physical medium.

### Functions
- **Electrical signals** - Voltage levels, timing
- **Mechanical specifications** - Cable types, connectors
- **Physical topology** - How devices are connected
- **Bit synchronization** - Clock signal for timing
- **Transmission mode** - Simplex, half-duplex, full-duplex

### Components
- **Cables:** Ethernet (Cat5e, Cat6), Fiber optic, Coaxial
- **Connectors:** RJ-45, SC, LC, BNC
- **Hubs** (legacy)
- **Repeaters**
- **Network Interface Cards (NICs)**

### Specifications
- **Ethernet:** 100BASE-TX, 1000BASE-T, 10GBASE-T
- **Fiber:** 1000BASE-SX, 1000BASE-LX
- **Wireless:** 802.11a/b/g/n/ac/ax radio frequencies

### Key Concepts
```
Data Representation:
- Binary: 0s and 1s
- Electrical: +5V / 0V (Ethernet)
- Optical: Light on/off (Fiber)
- Radio: Frequency modulation (Wi-Fi)

Transmission Modes:
- Simplex: One direction only (TV broadcast)
- Half-Duplex: Both directions, not simultaneously (Walkie-talkie)
- Full-Duplex: Both directions simultaneously (Phone call)
```

### Troubleshooting Layer 1
- Check physical connections
- Test cables
- Verify link lights on NICs/switches
- Check for electromagnetic interference

**Commands:**
```
show interfaces status
show interfaces [interface]
```

## Layer 2: Data Link Layer

### Purpose
Provides node-to-node data transfer and handles error correction from the physical layer.

### Sublayers

**1. LLC (Logical Link Control) - 802.2**
- Error detection
- Flow control
- Interface to Network layer

**2. MAC (Media Access Control) - 802.3**
- Frame formatting
- MAC addressing
- Media access methods (CSMA/CD)

### Functions
- **Framing** - Encapsulates packets into frames
- **Physical addressing** - Uses MAC addresses
- **Error detection** - Frame Check Sequence (FCS)
- **Flow control** - Manages data transmission rate
- **Access control** - Determines which device can transmit

### MAC Address

**Format:** 48-bit address (6 bytes)
```
AA:BB:CC:DD:EE:FF (Hexadecimal)
AA-BB-CC-DD-EE-FF (Alternative notation)

Structure:
[OUI - 24 bits][Device ID - 24 bits]
 Organizationally    Unique device
 Unique Identifier   identifier
```

**Types:**
- **Unicast:** Single destination (First bit of first byte = 0)
- **Multicast:** Group of devices (First bit = 1)
- **Broadcast:** All devices (FF:FF:FF:FF:FF:FF)

### Ethernet Frame

```
┌──────────┬──────────┬──────┬──────┬──────────┬─────┐
│ Preamble │ Dst MAC  │ Src  │ Type │   Data   │ FCS │
│  (7 bytes│  (6)     │ MAC  │ (2)  │(46-1500) │ (4) │
│  + 1 SFD)│          │ (6)  │      │          │     │
└──────────┴──────────┴──────┴──────┴──────────┴─────┘

Preamble: Synchronization (10101010... pattern)
SFD: Start Frame Delimiter (10101011)
Type: Protocol (0x0800 = IPv4, 0x0806 = ARP, 0x86DD = IPv6)
FCS: Frame Check Sequence (CRC error detection)
```

### Components
- **Switches** - Forward frames based on MAC addresses
- **Bridges** - Connect network segments
- **NICs** - Network interface cards
- **Wireless Access Points**

### Protocols and Standards
- **Ethernet (802.3)** - Wired LANs
- **Wi-Fi (802.11)** - Wireless LANs
- **PPP** - Point-to-Point Protocol
- **HDLC** - High-Level Data Link Control
- **Frame Relay** (legacy)
- **ARP** - Address Resolution Protocol

### Troubleshooting Layer 2
- Check MAC address table
- Verify VLAN assignments
- Check for broadcast storms
- Verify spanning-tree status
- Check for duplex mismatches

**Commands:**
```
show mac address-table
show vlan
show spanning-tree
show interfaces switchport
```

## Layer 3: Network Layer

### Purpose
Provides logical addressing, routing, and path determination for data delivery across networks.

### Functions
- **Logical addressing** - IP addresses
- **Routing** - Path selection through network
- **Packet forwarding** - Moving packets between networks
- **Fragmentation** - Breaking large packets into smaller ones
- **Path determination** - Best route calculation

### IP Addressing

**IPv4:**
```
192.168.1.100
32-bit address, dotted decimal notation
Provides ~4.3 billion addresses
```

**IPv6:**
```
2001:0db8:85a3::8a2e:0370:7334
128-bit address, hexadecimal notation
Provides 340 undecillion addresses
```

### IP Packet Header

```
┌─────────┬─────────┬─────────────┬─────────────┐
│ Version │   IHL   │     TOS     │   Length    │
│  (4)    │   (4)   │     (8)     │    (16)     │
├─────────┴─────────┼─────────────┼─────────────┤
│   Identification  │Flags│Fragment Offset (13)│
│       (16)        │ (3) │                     │
├───────────────────┼─────────────┴─────────────┤
│  TTL   │ Protocol │    Header Checksum        │
│  (8)   │   (8)    │         (16)              │
├────────┴──────────┴───────────────────────────┤
│           Source IP Address (32)              │
├───────────────────────────────────────────────┤
│         Destination IP Address (32)           │
├───────────────────────────────────────────────┤
│                Options (if any)               │
└───────────────────────────────────────────────┘
```

**Key Fields:**
- **Version:** 4 (IPv4) or 6 (IPv6)
- **TTL (Time to Live):** Hop limit (decremented by each router)
- **Protocol:** 1=ICMP, 6=TCP, 17=UDP
- **Source/Destination IP:** Logical addresses

### Components
- **Routers** - Forward packets between networks
- **Layer 3 Switches** - Hardware-based routing
- **Firewalls** - Security filtering

### Protocols
- **IP (Internet Protocol)** - Packet delivery
- **ICMP (Internet Control Message Protocol)** - Error reporting, diagnostics
- **ARP (Address Resolution Protocol)** - IP to MAC resolution
- **IGMP (Internet Group Management Protocol)** - Multicast group management
- **Routing Protocols:**
  - RIP (Routing Information Protocol)
  - OSPF (Open Shortest Path First)
  - EIGRP (Enhanced Interior Gateway Routing Protocol)
  - BGP (Border Gateway Protocol)

### Routing

**Routing Table:**
```
Network          Gateway         Interface    Metric
0.0.0.0/0        192.168.1.1    eth0         10
10.0.0.0/8       10.0.0.1       eth1         1
192.168.1.0/24   0.0.0.0        eth0         0
```

**Routing Decision:**
1. Examine destination IP
2. Look up routing table
3. Match longest prefix
4. Forward to next hop
5. Decrement TTL

### Troubleshooting Layer 3
- Verify IP configuration
- Check routing tables
- Test with ping
- Use traceroute
- Check for routing loops

**Commands:**
```
show ip route
show ip interface brief
ping [destination]
traceroute [destination]
```

## Layer 4: Transport Layer

### Purpose
Provides end-to-end communication services, reliability, and data flow control between applications.

### Functions
- **Segmentation** - Breaking data into smaller units
- **Port addressing** - Identifying applications
- **Connection control** - Connection-oriented or connectionless
- **Flow control** - Managing transmission rate
- **Error control** - Ensuring reliable delivery
- **Multiplexing** - Multiple connections over single network

### Protocols

**TCP (Transmission Control Protocol):**
- **Connection-oriented** - Three-way handshake
- **Reliable** - Guaranteed delivery with acknowledgments
- **Ordered** - Sequential delivery
- **Flow control** - Window size management
- **Use cases:** HTTP, HTTPS, FTP, SSH, email

**UDP (User Datagram Protocol):**
- **Connectionless** - No handshake
- **Unreliable** - Best-effort delivery
- **Fast** - Minimal overhead
- **No ordering** - Packets may arrive out of order
- **Use cases:** DNS, DHCP, streaming, gaming

### Port Numbers

```
0-1023:      Well-Known Ports (HTTP=80, HTTPS=443, SSH=22)
1024-49151:  Registered Ports (application-specific)
49152-65535: Dynamic/Private Ports (temporary client ports)
```

**Socket:** IP Address + Port Number
```
Example: 192.168.1.100:80
```

### TCP Segment

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
├─────┴─────┴──────┼──────────────────┤
│    Checksum      │  Urgent Pointer  │
└──────────────────┴──────────────────┘
```

### Troubleshooting Layer 4
- Verify port numbers
- Check firewall rules
- Test with telnet or netcat
- Check connection states
- Monitor for port exhaustion

**Commands:**
```
netstat -an
ss -tuln
telnet [host] [port]
```

## Layer 5: Session Layer

### Purpose
Establishes, manages, and terminates sessions between applications.

### Functions
- **Session establishment** - Sets up connections
- **Session maintenance** - Keeps connections active
- **Session termination** - Closes connections gracefully
- **Synchronization** - Checkpoints for long transfers
- **Dialog control** - Half-duplex or full-duplex

### Protocols
- **NetBIOS** - Network Basic Input/Output System
- **PPTP** - Point-to-Point Tunneling Protocol
- **RPC** - Remote Procedure Call
- **SMB** - Server Message Block

### Examples
- **Web session:** Login to website, browse pages, logout
- **Database session:** Connect, query, disconnect
- **VoIP call:** Setup, conversation, hangup

## Layer 6: Presentation Layer

### Purpose
Translates data between application and network formats, handling encryption, compression, and encoding.

### Functions
- **Data translation** - Format conversion
- **Encryption/Decryption** - Security
- **Compression** - Reduce data size
- **Character encoding** - ASCII, Unicode, EBCDIC

### Protocols and Standards
- **SSL/TLS** - Secure Sockets Layer / Transport Layer Security
- **JPEG, GIF, PNG** - Image formats
- **MPEG, MOV** - Video formats
- **ASCII, Unicode** - Character encoding

### Examples
- **HTTPS:** Encrypts HTTP traffic
- **JPEG:** Compresses images
- **ASCII to EBCDIC** conversion

## Layer 7: Application Layer

### Purpose
Provides network services directly to end-user applications.

### Functions
- **Network virtual terminal** - Remote login
- **File transfer** - Moving files
- **Mail services** - Email
- **Directory services** - Name resolution
- **Resource sharing** - Accessing shared resources

### Protocols

**Web Services:**
- **HTTP/HTTPS** - Web browsing
- **WebSocket** - Real-time communication

**File Transfer:**
- **FTP** - File Transfer Protocol
- **TFTP** - Trivial FTP
- **SFTP** - SSH File Transfer Protocol

**Email:**
- **SMTP** - Simple Mail Transfer Protocol (sending)
- **POP3** - Post Office Protocol (receiving)
- **IMAP** - Internet Message Access Protocol (receiving)

**Name Resolution:**
- **DNS** - Domain Name System

**Network Management:**
- **SNMP** - Simple Network Management Protocol
- **Telnet** - Remote terminal
- **SSH** - Secure Shell

**Other:**
- **DHCP** - Dynamic Host Configuration Protocol
- **NTP** - Network Time Protocol
- **LDAP** - Lightweight Directory Access Protocol

### Troubleshooting Layer 7
- Check application logs
- Verify protocol-specific configuration
- Test with protocol-specific tools
- Check DNS resolution
- Verify application services are running

**Commands:**
```
nslookup [domain]
curl [url]
telnet [host] [port]
```

## Data Encapsulation

### Sending Data (Top to Bottom)

```
Layer 7 (Application):
  [Data]

Layer 6 (Presentation):
  [Data] (formatted, possibly encrypted)

Layer 5 (Session):
  [Data] (session management info added)

Layer 4 (Transport):
  [TCP/UDP Header][Data]
  → Segment (TCP) or Datagram (UDP)

Layer 3 (Network):
  [IP Header][TCP Header][Data]
  → Packet

Layer 2 (Data Link):
  [Ethernet Header][IP Header][TCP Header][Data][Trailer]
  → Frame

Layer 1 (Physical):
  10101010101... → Bits on the wire
```

### Receiving Data (Bottom to Top)

```
Layer 1: Receives bits, converts to frame
Layer 2: Strips Ethernet header/trailer, passes packet up
Layer 3: Strips IP header, passes segment up
Layer 4: Strips TCP header, reassembles data
Layer 5: Manages session
Layer 6: Decrypts/decompresses if needed
Layer 7: Delivers data to application
```

## PDU (Protocol Data Unit) Names

| Layer | PDU Name | Information |
|-------|----------|-------------|
| 7 (Application) | Data | User data |
| 6 (Presentation) | Data | Formatted data |
| 5 (Session) | Data | Session data |
| 4 (Transport) | Segment/Datagram | TCP segment or UDP datagram |
| 3 (Network) | Packet | IP packet |
| 2 (Data Link) | Frame | Ethernet frame |
| 1 (Physical) | Bits | Binary signals |

## Troubleshooting with OSI Model

### Bottom-Up Approach

Start at Physical layer, work up:

```
1. Physical (Layer 1)
   - Check cables plugged in?
   - Link lights on?
   - Cable good?

2. Data Link (Layer 2)
   - MAC address correct?
   - VLAN configured?
   - Switch port enabled?

3. Network (Layer 3)
   - IP address correct?
   - Subnet mask correct?
   - Default gateway configured?
   - Can ping gateway?

4. Transport (Layer 4)
   - Correct port?
   - Firewall blocking?
   - Service listening?

5-7. Upper Layers
   - Application configured correctly?
   - DNS resolving?
   - Credentials correct?
```

### Top-Down Approach

Start at Application layer, work down:

```
7. Application
   - Application error message?
   - Wrong URL/hostname?

6. Presentation
   - Encryption issue?
   - Format incompatible?

5. Session
   - Session timeout?
   - Connection dropped?

4. Transport
   - Port blocked?
   - Connection refused?

3. Network
   - Routing issue?
   - Can't reach network?

2. Data Link
   - Switch issue?
   - VLAN problem?

1. Physical
   - Cable unplugged?
   - No link light?
```

## Real-World Example

**User browses to https://www.example.com**

```
Layer 7 (Application):
  Browser creates HTTP GET request

Layer 6 (Presentation):
  SSL/TLS encrypts the request

Layer 5 (Session):
  Establishes TLS session

Layer 4 (Transport):
  TCP wraps data (SYN handshake first)
  Destination port: 443
  Source port: 52341

Layer 3 (Network):
  IP packet created
  Source: 192.168.1.100
  Destination: 93.184.216.34

Layer 2 (Data Link):
  Ethernet frame created
  Source MAC: PC's NIC
  Destination MAC: Gateway's MAC

Layer 1 (Physical):
  Electrical signals sent over Cat6 cable
```

## Summary

| Layer | Name | Function | Protocol Examples | Devices |
|-------|------|----------|-------------------|---------|
| 7 | Application | User interface | HTTP, FTP, SMTP, DNS | -  |
| 6 | Presentation | Data format | SSL/TLS, JPEG, ASCII | - |
| 5 | Session | Session control | NetBIOS, RPC | - |
| 4 | Transport | End-to-end delivery | TCP, UDP | - |
| 3 | Network | Routing | IP, ICMP, OSPF | Routers |
| 2 | Data Link | Frame delivery | Ethernet, Wi-Fi, PPP | Switches, Bridges |
| 1 | Physical | Bits on wire | Cables, signals | Hubs, Cables, NICs |

**Key Concepts:**
- **OSI Model** has 7 layers
- **Encapsulation** adds headers going down
- **De-encapsulation** removes headers going up
- **Each layer** communicates with its peer layer
- **Troubleshooting** uses OSI model as framework

## Practice Questions

1. Which layer uses MAC addresses? (Answer: Layer 2 - Data Link)
2. Which layer uses IP addresses? (Answer: Layer 3 - Network)
3. What is a TCP segment at Layer 4 called at Layer 3? (Answer: Packet)
4. Which layer handles encryption? (Answer: Layer 6 - Presentation)
5. Which layers do routers operate at? (Answer: Layers 1, 2, and 3)

## Next Steps

- Compare with [TCP/IP Model](../tcp-ip-model/README.md)
- Study [Packet Flow](../packet-flow/README.md) to see OSI in action
- Review layer-specific protocols in other sections
