# TCP/UDP Protocols

## Overview

TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) are the two main transport layer protocols (Layer 4) responsible for end-to-end communication between applications.

## Transport Layer (Layer 4)

**Functions:**
- **Segmentation and Reassembly** - Breaking data into manageable pieces
- **Port Addressing** - Identifying specific applications
- **Flow Control** - Managing data transmission rate
- **Error Detection** - Ensuring data integrity
- **Connection Management** - Establishing and terminating sessions

## TCP (Transmission Control Protocol)

### Characteristics

TCP is a **connection-oriented**, **reliable** protocol.

**Key Features:**
- **Connection-Oriented:** Three-way handshake before data transfer
- **Reliable:** Guarantees delivery with acknowledgments
- **Ordered:** Data arrives in sequence
- **Flow Control:** Prevents overwhelming receiver
- **Error Checking:** Detects and recovers from errors
- **Congestion Control:** Adapts to network conditions

### TCP Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Offset| Res |     Flags       |            Window             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**Key Fields:**
- **Source/Destination Port** (16 bits each) - Application identification
- **Sequence Number** (32 bits) - Data ordering
- **Acknowledgment Number** (32 bits) - Confirms received data
- **Flags** (6 bits) - Control information
- **Window Size** (16 bits) - Flow control
- **Checksum** (16 bits) - Error detection

### TCP Flags

| Flag | Name | Purpose |
|------|------|---------|
| **URG** | Urgent | Urgent data present |
| **ACK** | Acknowledgment | Acknowledges received data |
| **PSH** | Push | Send data immediately |
| **RST** | Reset | Reset connection |
| **SYN** | Synchronize | Initiate connection |
| **FIN** | Finish | Terminate connection |

## Three-Way Handshake

The TCP three-way handshake establishes a connection before data transfer.

### Process

```
Client                                  Server
  |                                       |
  |   1. SYN (Seq=100)                   |
  |-------------------------------------->|
  |                                       |
  |   2. SYN-ACK (Seq=300, Ack=101)      |
  |<--------------------------------------|
  |                                       |
  |   3. ACK (Seq=101, Ack=301)          |
  |-------------------------------------->|
  |                                       |
  |   Connection Established              |
  |   Data Transfer Begins                |
```

**Step-by-Step:**

**Step 1: SYN (Synchronize)**
- Client sends SYN packet
- Initial Sequence Number (ISN): 100
- Flags: SYN=1

**Step 2: SYN-ACK**
- Server responds with SYN-ACK
- Server's ISN: 300
- Acknowledges client's SYN: Ack=101 (100+1)
- Flags: SYN=1, ACK=1

**Step 3: ACK**
- Client sends ACK
- Acknowledges server's SYN: Ack=301 (300+1)
- Sequence: 101
- Flags: ACK=1

**Connection Established!** Data transfer can begin.

### Connection Termination (Four-Way Handshake)

```
Client                                  Server
  |                                       |
  |   1. FIN (Seq=1000)                  |
  |-------------------------------------->|
  |                                       |
  |   2. ACK (Ack=1001)                  |
  |<--------------------------------------|
  |                                       |
  |   3. FIN (Seq=2000)                  |
  |<--------------------------------------|
  |                                       |
  |   4. ACK (Ack=2001)                  |
  |-------------------------------------->|
  |                                       |
  |   Connection Closed                   |
```

**Steps:**
1. Client sends FIN (wants to close)
2. Server ACKs the FIN
3. Server sends its own FIN (ready to close)
4. Client ACKs server's FIN
5. Connection terminated

## TCP Flow Control

Flow control prevents the sender from overwhelming the receiver.

### Sliding Window

**Window Size** indicates how much data can be sent before requiring acknowledgment.

**Example:**
```
Sender                               Receiver
Window Size: 4000 bytes

Send 1000 bytes (Seq 1-1000)
Send 1000 bytes (Seq 1001-2000)
Send 1000 bytes (Seq 2001-3000)
Send 1000 bytes (Seq 3001-4000)
|<-------- Wait for ACK -------->|
                                  ACK 4001, Window 4000
Continue sending...
```

**Key Points:**
- Dynamic window size
- Receiver advertises available buffer space
- Sender adjusts transmission accordingly
- Prevents buffer overflow

## TCP Reliability

### Acknowledgments (ACKs)

TCP uses acknowledgments to confirm data receipt.

**Cumulative ACK:**
```
Sender sends: Seq 1-1000
Sender sends: Seq 1001-2000
Receiver ACKs: Ack 2001 (received up to 2000)
```

### Retransmission

If data is lost or corrupted, TCP retransmits.

**Timeout-Based:**
```
Sender sends Seq 1000
|--- Packet Lost ----|
Timeout expires
Sender retransmits Seq 1000
```

**Fast Retransmit (Duplicate ACKs):**
```
Send: Seq 1000 → ACK 1001
Send: Seq 2000 → Lost
Send: Seq 3000 → ACK 2001 (duplicate)
Send: Seq 4000 → ACK 2001 (duplicate)
Send: Seq 5000 → ACK 2001 (duplicate)

After 3 duplicate ACKs:
Retransmit Seq 2000 immediately (don't wait for timeout)
```

## TCP Congestion Control

TCP adjusts transmission rate based on network conditions.

### Algorithms

**1. Slow Start:**
- Start with small congestion window (cwnd)
- Double cwnd for each ACK received
- Exponential growth until threshold

**2. Congestion Avoidance:**
- After threshold, increase linearly
- Add 1 segment per RTT (Round Trip Time)

**3. Fast Recovery:**
- After fast retransmit
- Reduce cwnd by half
- Resume congestion avoidance

### Visual Representation

```
Congestion Window Size
      ^
      |     Slow Start
      |    /|
      |   / |  Congestion
      |  /  |  Avoidance
      | /   |\
      |/    | \__ Fast Recovery
      |     |  /
      +----------------> Time
      
      Packet Loss Detected
```

## UDP (User Datagram Protocol)

### Characteristics

UDP is a **connectionless**, **unreliable** protocol.

**Key Features:**
- **Connectionless:** No handshake, immediate data transfer
- **Unreliable:** No delivery guarantee
- **Unordered:** Packets may arrive out of sequence
- **No Flow Control:** Send rate not regulated
- **Low Overhead:** Minimal header (8 bytes vs TCP's 20+ bytes)
- **Fast:** No connection setup or reliability mechanisms

### UDP Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|             Length            |           Checksum            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**Fields:**
- **Source Port** (16 bits) - Optional sender's port
- **Destination Port** (16 bits) - Receiver's port
- **Length** (16 bits) - UDP header + data length
- **Checksum** (16 bits) - Error detection (optional in IPv4)

**Total Header Size: 8 bytes**

### UDP Operation

```
Sender                          Receiver
  |                               |
  |   UDP Datagram               |
  |------------------------------>|
  |                               |
  |   No ACK, No Handshake        |
  |   "Fire and Forget"           |
```

**No Connection Setup:**
- Sender starts sending immediately
- No handshake required
- Lower latency

**No Reliability:**
- Packets may be lost
- No retransmission
- Application handles reliability if needed

## TCP vs. UDP Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| **Connection** | Connection-oriented | Connectionless |
| **Reliability** | Reliable (guaranteed delivery) | Unreliable (best effort) |
| **Ordering** | Ordered delivery | No ordering guarantee |
| **Speed** | Slower (overhead) | Faster (minimal overhead) |
| **Header Size** | 20-60 bytes | 8 bytes |
| **Flow Control** | Yes | No |
| **Congestion Control** | Yes | No |
| **Error Checking** | Yes (extensive) | Yes (basic checksum) |
| **Retransmission** | Yes | No |
| **Acknowledgment** | Yes | No |
| **Broadcasting** | No | Yes (supported) |
| **Use Cases** | HTTP, FTP, Email, SSH | DNS, VoIP, Video Streaming, Gaming |

## Port Numbers

Ports identify specific applications or services.

### Port Ranges

| Range | Type | Description |
|-------|------|-------------|
| 0-1023 | **Well-Known Ports** | Reserved for common services |
| 1024-49151 | **Registered Ports** | Assigned to specific applications |
| 49152-65535 | **Dynamic/Private Ports** | Temporary client ports |

### Common Well-Known Ports

| Port | Protocol | Service | Transport |
|------|----------|---------|-----------|
| 20 | FTP | Data Transfer | TCP |
| 21 | FTP | Control | TCP |
| 22 | SSH | Secure Shell | TCP |
| 23 | Telnet | Remote Login | TCP |
| 25 | SMTP | Email (Send) | TCP |
| 53 | DNS | Domain Name System | TCP/UDP |
| 67/68 | DHCP | Dynamic IP | UDP |
| 69 | TFTP | Trivial FTP | UDP |
| 80 | HTTP | Web | TCP |
| 110 | POP3 | Email (Receive) | TCP |
| 123 | NTP | Time Sync | UDP |
| 143 | IMAP | Email (Receive) | TCP |
| 161/162 | SNMP | Network Management | UDP |
| 443 | HTTPS | Secure Web | TCP |
| 445 | SMB | File Sharing | TCP |
| 3389 | RDP | Remote Desktop | TCP |

### Socket

A **socket** is the combination of IP address and port number.

**Format:** `IP:Port`

**Examples:**
- `192.168.1.100:80` (web server)
- `10.0.0.50:443` (HTTPS server)
- `8.8.8.8:53` (DNS server)

**Connection Identification:**
```
Source Socket + Destination Socket = Unique Connection

Client: 192.168.1.100:52341
Server: 93.184.216.34:80

Connection: 192.168.1.100:52341 <-> 93.184.216.34:80
```

## When to Use TCP vs. UDP

### Use TCP When:

✅ **Reliability is critical**
- File transfers (FTP, SFTP)
- Email (SMTP, POP3, IMAP)
- Web browsing (HTTP, HTTPS)
- Remote access (SSH, Telnet)

✅ **Data integrity matters**
- Database transactions
- Financial transactions
- Configuration downloads

✅ **Ordered delivery required**
- Streaming recorded video
- Text-based protocols

### Use UDP When:

✅ **Speed is more important than reliability**
- Live video streaming
- Voice over IP (VoIP)
- Online gaming

✅ **Small, simple queries**
- DNS lookups
- DHCP requests
- NTP time synchronization

✅ **Broadcasting/Multicasting**
- Network discovery
- Media distribution

✅ **Real-time applications**
- Video conferencing
- Live sports streaming
- Online multiplayer games

**Key Insight:** If occasional packet loss is acceptable and low latency is critical, use UDP. If every bit must arrive correctly and in order, use TCP.

## TCP States

TCP connections progress through various states.

### Connection States

```
CLOSED → LISTEN → SYN-SENT → SYN-RECEIVED → ESTABLISHED
         (Server)   (Client)     (Server)

ESTABLISHED → FIN-WAIT-1 → FIN-WAIT-2 → TIME-WAIT → CLOSED
              (Active Close)

ESTABLISHED → CLOSE-WAIT → LAST-ACK → CLOSED
              (Passive Close)
```

**Common States:**
- **LISTEN:** Server waiting for connection
- **SYN-SENT:** Client sent SYN, waiting for SYN-ACK
- **ESTABLISHED:** Connection active, data transfer
- **FIN-WAIT-1/2:** Active close in progress
- **CLOSE-WAIT:** Passive close, waiting for application
- **TIME-WAIT:** Waiting before fully closing (2MSL)

**View States (Linux/Windows):**
```bash
netstat -an | grep ESTABLISHED
netstat -an | grep LISTEN
```

## Practical Examples

### Example 1: Web Browsing (TCP)

```
User enters: http://example.com

1. DNS lookup (UDP port 53)
   Query: "What's the IP of example.com?"
   Response: "93.184.216.34"

2. TCP Three-Way Handshake
   Client → Server: SYN (port 80)
   Server → Client: SYN-ACK
   Client → Server: ACK

3. HTTP Request (TCP)
   GET / HTTP/1.1
   Host: example.com

4. HTTP Response (TCP)
   HTTP/1.1 200 OK
   Content: HTML page

5. TCP Connection Termination
   Four-way handshake (FIN, ACK, FIN, ACK)
```

### Example 2: DNS Lookup (UDP)

```
Application needs IP for www.google.com

1. Send UDP datagram to DNS server (port 53)
   Query: "A record for www.google.com"

2. DNS server responds with UDP datagram
   Response: "142.250.185.78"

3. No handshake, no ACK, single exchange
   Fast and efficient for simple query
```

### Example 3: Video Streaming (UDP)

```
Live sports stream:

- Packets sent continuously via UDP
- If packet lost → Skip it, continue
- No retransmission (would cause delay)
- Viewer sees real-time action
- Occasional glitch acceptable
```

## Troubleshooting

### Common Commands

**Linux:**
```bash
# View active connections
netstat -an
ss -tuln

# View listening ports
netstat -ln
ss -tln

# View TCP statistics
netstat -st

# View UDP statistics
netstat -su

# Test TCP connection
telnet example.com 80
nc -zv example.com 80

# Test UDP connection
nc -uzv example.com 53
```

**Windows:**
```cmd
# View active connections
netstat -an

# View listening ports
netstat -an | findstr LISTENING

# Test TCP connection
telnet example.com 80
Test-NetConnection example.com -Port 80
```

### Common Issues

**1. Connection Refused (TCP)**
- Port not listening
- Firewall blocking
- Service not running

**2. Connection Timeout (TCP)**
- Network path issue
- Firewall dropping packets
- Server unreachable

**3. No Response (UDP)**
- Normal for UDP (connectionless)
- Check with application logs
- Use tcpdump/Wireshark

**4. Port Already in Use**
- Another application using port
- Check: `netstat -an | grep :80`
- Solution: Stop conflicting service or use different port

## Best Practices

1. **Choose appropriate protocol**
   - TCP for reliability
   - UDP for speed

2. **Use standard ports when possible**
   - Easier for firewall rules
   - Better documentation

3. **Secure services**
   - Use encrypted versions (HTTPS, SFTP, SSH)
   - Disable unnecessary services

4. **Monitor connections**
   - Watch for excessive connections
   - Detect potential attacks (SYN flood)

5. **Tune TCP parameters** (advanced)
   - Window size
   - Timeout values
   - Congestion control algorithm

## Summary

- **TCP:** Connection-oriented, reliable, ordered delivery
- **UDP:** Connectionless, unreliable, fast
- **Three-way handshake:** SYN, SYN-ACK, ACK establishes TCP connection
- **Ports:** Identify applications (0-65535)
- **Sockets:** IP + Port combination
- **Use TCP** when reliability matters
- **Use UDP** when speed matters
- **Well-known ports:** 0-1023 (HTTP=80, HTTPS=443, DNS=53)

## Practice Questions

1. How many bytes is the UDP header? (Answer: 8 bytes)
2. What are the three TCP flags in a three-way handshake? (Answer: SYN, SYN-ACK, ACK)
3. What port does HTTPS use? (Answer: 443)
4. Which protocol guarantees delivery? (Answer: TCP)
5. What is a socket? (Answer: IP address + port number)

## Next Steps

- Study [Packet Flow](../packet-flow/README.md) to see TCP/UDP in action
- Explore [OSI Model](../osi-model/README.md) for Layer 4 context
- Practice with [Wireshark](../wireshark/README.md) to capture TCP/UDP traffic
