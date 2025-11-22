# Wireshark Analysis

## Overview

**Wireshark** is the world's foremost network protocol analyzer. It allows you to capture and interactively browse traffic running on a computer network, making it an essential tool for network troubleshooting, analysis, security, and education.

## What is Wireshark?

**Wireshark** is:
- **Free and open-source** network packet analyzer
- **Cross-platform** (Windows, macOS, Linux, Unix)
- **Formerly known as** Ethereal
- **De facto standard** for network analysis
- **Used by** network professionals, security experts, educators, and developers

### Key Features

- ✅ Live packet capture
- ✅ Detailed protocol analysis
- ✅ Supports 1000+ protocols
- ✅ Powerful display filters
- ✅ Capture filters (BPF syntax)
- ✅ Packet color coding
- ✅ Export capabilities
- ✅ Statistics and graphs
- ✅ Following TCP streams
- ✅ Decryption support (WPA, SSL/TLS with keys)

## Installing Wireshark

### Windows
```
1. Download from: https://www.wireshark.org/download.html
2. Run installer
3. Install Npcap (packet capture library) when prompted
4. Launch Wireshark
```

### macOS
```
1. Download DMG from: https://www.wireshark.org/download.html
2. Install ChmodBPF for packet capture permissions
3. Drag Wireshark to Applications
4. Launch from Applications
```

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install wireshark
sudo usermod -aG wireshark $USER
# Log out and back in
```

### Linux (RHEL/CentOS/Fedora)
```bash
sudo yum install wireshark
# or
sudo dnf install wireshark
```

## Wireshark Interface

### Main Components

```
┌─────────────────────────────────────────────────────┐
│  Menu Bar  (File, Edit, View, Go, Capture, etc.)   │
├─────────────────────────────────────────────────────┤
│  Main Toolbar  (Start, Stop, Filter buttons)       │
├─────────────────────────────────────────────────────┤
│  Filter Toolbar  (Display filter input)            │
├─────────────────────────────────────────────────────┤
│  Packet List Pane                                   │
│  (List of captured packets)                         │
├─────────────────────────────────────────────────────┤
│  Packet Details Pane                                │
│  (Expandable protocol tree)                         │
├─────────────────────────────────────────────────────┤
│  Packet Bytes Pane                                  │
│  (Hex dump of packet data)                          │
├─────────────────────────────────────────────────────┤
│  Status Bar  (Packet count, profile)               │
└─────────────────────────────────────────────────────┘
```

### Packet List Pane Columns

Default columns:
- **No.** - Packet number
- **Time** - Timestamp
- **Source** - Source address
- **Destination** - Destination address
- **Protocol** - Highest layer protocol
- **Length** - Packet length
- **Info** - Summary information

## Capturing Packets

### Starting a Capture

**Method 1: Interface Selection**
```
1. Open Wireshark
2. Select network interface (e.g., eth0, wlan0, Wi-Fi)
3. Click the blue shark fin icon (Start Capture)
```

**Method 2: Command Line**
```bash
# Capture on specific interface
wireshark -i eth0 -k

# Capture with filter
wireshark -i eth0 -f "port 80" -k
```

**Method 3: Tshark (Command-Line)**
```bash
# Basic capture
tshark -i eth0

# Capture to file
tshark -i eth0 -w capture.pcap

# Capture with count limit
tshark -i eth0 -c 100
```

### Stopping a Capture

- Click the red square (Stop) button
- Press Ctrl+E
- Menu: Capture → Stop

### Saving Captures

```
File → Save
or
File → Save As

Common formats:
- .pcap (Wireshark/tcpdump)
- .pcapng (Next generation pcap, default)
- .cap (Alternative extension)
```

## Capture Filters

**Capture filters** use Berkeley Packet Filter (BPF) syntax and are applied **before** packets are captured, reducing capture size.

### Syntax

```
[protocol] [direction] [host/net] [value] [logical operator]
```

### Common Capture Filters

**By Protocol:**
```
tcp                    # Only TCP traffic
udp                    # Only UDP traffic
icmp                   # Only ICMP traffic
arp                    # Only ARP traffic
```

**By Host:**
```
host 192.168.1.100              # Traffic to/from specific IP
host www.google.com             # Traffic to/from hostname
src host 192.168.1.100          # Traffic from specific IP
dst host 192.168.1.100          # Traffic to specific IP
```

**By Network:**
```
net 192.168.1.0/24              # Traffic on subnet
net 192.168.0.0 mask 255.255.0.0
```

**By Port:**
```
port 80                         # Traffic on port 80
port 80 or port 443            # HTTP or HTTPS
portrange 1-1024               # Ports 1-1024
src port 80                    # Source port 80
dst port 443                   # Destination port 443
```

**Combinations:**
```
tcp and port 80                # TCP traffic on port 80
host 192.168.1.1 and port 53   # DNS to/from 192.168.1.1
tcp and (port 80 or port 443)  # HTTP and HTTPS
not arp and not icmp           # Exclude ARP and ICMP
```

**Advanced Examples:**
```
# Web traffic from specific host
src host 192.168.1.100 and (port 80 or port 443)

# SSH traffic except from specific host
tcp port 22 and not host 10.0.0.50

# All traffic except DNS
not port 53
```

## Display Filters

**Display filters** are applied **after** capture to show/hide packets from captured data. More powerful than capture filters.

### Syntax

```
protocol.field operator value
```

### Common Operators

| Operator | Description | Example |
|----------|-------------|---------|
| == | Equal | ip.addr == 192.168.1.1 |
| != | Not equal | tcp.port != 80 |
| > | Greater than | frame.len > 1000 |
| < | Less than | frame.time_relative < 5 |
| >= | Greater or equal | tcp.len >= 100 |
| <= | Less or equal | ip.ttl <= 64 |
| contains | Contains string | http.request.uri contains "login" |
| matches | Regex match | http.host matches ".*google.*" |
| in | In a range | tcp.port in {80 443 8080} |

### Logical Operators

```
and  or  &&     # Logical AND
or   or  ||     # Logical OR
not  or  !      # Logical NOT
```

### Common Display Filters

**By Protocol:**
```
http                    # HTTP traffic
dns                     # DNS traffic
tcp                     # TCP traffic
udp                     # UDP traffic
arp                     # ARP traffic
icmp                    # ICMP traffic
```

**By IP Address:**
```
ip.addr == 192.168.1.100         # Traffic to/from IP
ip.src == 192.168.1.100          # Source IP
ip.dst == 192.168.1.100          # Destination IP
ip.addr == 192.168.1.0/24        # Subnet
```

**By Port:**
```
tcp.port == 80                   # TCP port 80
tcp.dstport == 443               # TCP destination port 443
tcp.srcport == 52341             # TCP source port
udp.port == 53                   # UDP port 53
```

**By MAC Address:**
```
eth.addr == 00:11:22:33:44:55
eth.src == 00:11:22:33:44:55
eth.dst == ff:ff:ff:ff:ff:ff    # Broadcast
```

**HTTP Specific:**
```
http.request                     # HTTP requests
http.response                    # HTTP responses
http.request.method == "GET"     # GET requests
http.request.method == "POST"    # POST requests
http.request.uri contains "login"
http.response.code == 200        # HTTP 200 OK
http.response.code == 404        # HTTP 404 Not Found
http.host == "www.example.com"
```

**DNS Specific:**
```
dns.qry.name == "www.google.com"
dns.flags.response == 1          # DNS responses
dns.qry.type == 1                # A records
```

**TCP Specific:**
```
tcp.flags.syn == 1               # SYN packets
tcp.flags.ack == 1               # ACK packets
tcp.flags.reset == 1             # RST packets
tcp.analysis.retransmission      # Retransmissions
tcp.analysis.duplicate_ack       # Duplicate ACKs
tcp.window_size_value < 1000     # Small window
```

**Combinations:**
```
# HTTP traffic from specific IP
ip.src == 192.168.1.100 and http

# Failed HTTP requests
http.response.code >= 400

# DNS queries for google.com
dns and dns.qry.name contains "google"

# Large packets
frame.len > 1500

# TCP handshake (SYN packets)
tcp.flags.syn == 1 and tcp.flags.ack == 0

# Retransmissions and errors
tcp.analysis.retransmission or tcp.analysis.duplicate_ack
```

## Following TCP Streams

**TCP Stream** reassembles a complete conversation.

**Steps:**
```
1. Right-click on any packet in a TCP conversation
2. Select "Follow" → "TCP Stream"
3. View entire conversation in readable format
4. See client requests and server responses
```

**Use Cases:**
- Reading HTTP conversations
- Viewing FTP commands
- Analyzing email (SMTP/POP3)
- Debugging application protocols

**Stream Colors:**
- Red: Client to Server
- Blue: Server to Client

**Save Stream:**
```
File → Export → as specified format
```

## Packet Analysis Examples

### Example 1: Analyzing HTTP Request

**Display Filter:** `http.request.method == "GET"`

**Packet Details:**
```
Frame:
  - Length: 546 bytes
  - Capture time: 2024-01-15 10:30:45

Ethernet:
  - Source: aa:bb:cc:dd:ee:ff
  - Destination: 11:22:33:44:55:66
  - Type: IPv4 (0x0800)

Internet Protocol:
  - Source: 192.168.1.100
  - Destination: 93.184.216.34
  - Protocol: TCP (6)
  - TTL: 64

Transmission Control Protocol:
  - Source Port: 52341
  - Destination Port: 80
  - Sequence: 1
  - Flags: PSH, ACK

Hypertext Transfer Protocol:
  - Method: GET
  - URI: /index.html
  - Host: www.example.com
  - User-Agent: Mozilla/5.0...
```

### Example 2: DNS Query/Response

**Display Filter:** `dns.qry.name == "www.google.com"`

**Query Packet:**
```
DNS Query:
  - Transaction ID: 0x1234
  - Questions: 1
  - Query: www.google.com
  - Type: A (IPv4 address)
  - Class: IN (Internet)
```

**Response Packet:**
```
DNS Response:
  - Transaction ID: 0x1234
  - Answers: 1
  - www.google.com: 142.250.185.78
  - TTL: 300 seconds
```

### Example 3: TCP Three-Way Handshake

**Display Filter:** `tcp.flags.syn == 1 or tcp.flags.fin == 1`

**Packets:**
```
1. SYN
   - Flags: SYN
   - Seq: 0
   - Client → Server

2. SYN-ACK
   - Flags: SYN, ACK
   - Seq: 0, Ack: 1
   - Server → Client

3. ACK
   - Flags: ACK
   - Seq: 1, Ack: 1
   - Client → Server

Connection Established!
```

### Example 4: ARP Resolution

**Display Filter:** `arp`

**ARP Request:**
```
- Opcode: Request (1)
- Sender MAC: aa:bb:cc:dd:ee:ff
- Sender IP: 192.168.1.100
- Target MAC: 00:00:00:00:00:00
- Target IP: 192.168.1.1
- Broadcast: ff:ff:ff:ff:ff:ff
```

**ARP Reply:**
```
- Opcode: Reply (2)
- Sender MAC: 11:22:33:44:55:66
- Sender IP: 192.168.1.1
- Target MAC: aa:bb:cc:dd:ee:ff
- Target IP: 192.168.1.100
```

## Statistics and Analysis Tools

### Protocol Hierarchy

```
Statistics → Protocol Hierarchy

Shows:
- Protocol distribution
- Percentage of traffic
- Packet counts
- Byte counts
```

### Conversations

```
Statistics → Conversations

Shows:
- Endpoint pairs
- Packets exchanged
- Bytes transferred
- Duration
```

**Types:**
- Ethernet
- IPv4
- IPv6
- TCP
- UDP

### Endpoints

```
Statistics → Endpoints

Shows:
- Individual hosts
- Traffic per host
- Packet counts
- Byte counts
```

### I/O Graph

```
Statistics → I/O Graph

Features:
- Time-based traffic graph
- Multiple filters
- Different metrics (packets, bytes, bits)
- Customizable intervals
```

### TCP Stream Graphs

```
Statistics → TCP Stream Graphs

Options:
- Round Trip Time (RTT)
- Throughput
- Time/Sequence (Stevens)
- Time/Sequence (tcptrace)
- Window Scaling
```

## Color Coding

Wireshark uses colors to highlight packet types:

**Default Colors:**
- **Light Purple:** TCP traffic
- **Light Blue:** UDP traffic
- **Black:** Packets with errors
- **Green:** HTTP traffic
- **Light Green:** DNS traffic
- **Yellow:** Routing protocols
- **Dark Gray:** Bad TCP (checksum errors, retransmissions)
- **Red:** Critical issues

**Customize Colors:**
```
View → Coloring Rules
```

## Troubleshooting with Wireshark

### Scenario 1: Slow Network Performance

**Analysis Steps:**
```
1. Capture traffic during slow period
2. Check for retransmissions:
   Display filter: tcp.analysis.retransmission
3. Check for duplicate ACKs:
   Display filter: tcp.analysis.duplicate_ack
4. Analyze TCP window size:
   Display filter: tcp.window_size_value < 1000
5. Look for packet loss indicators
```

**Common Issues:**
- High retransmission rate (network congestion or errors)
- Small TCP window (receiving side buffer limitation)
- Packet loss (physical issues or congestion)

### Scenario 2: Connection Problems

**Analysis Steps:**
```
1. Capture during connection attempt
2. Look for TCP handshake:
   tcp.flags.syn == 1
3. Check for RST (reset) packets:
   tcp.flags.reset == 1
4. Verify SYN-ACK response
5. Check ICMP unreachable messages
```

**Common Issues:**
- No SYN-ACK (service not running or firewall)
- RST packet (connection refused)
- ICMP unreachable (routing issue)

### Scenario 3: DNS Issues

**Analysis Steps:**
```
1. Filter DNS traffic: dns
2. Check query: dns.qry.name == "example.com"
3. Verify response received
4. Check response time
5. Look for NXDOMAIN (domain not found)
```

**Common Issues:**
- No response (DNS server unreachable)
- NXDOMAIN (domain doesn't exist)
- Slow response (DNS server overloaded)

### Scenario 4: HTTP Errors

**Analysis Steps:**
```
1. Filter HTTP: http
2. Check status codes:
   http.response.code >= 400
3. Follow HTTP stream
4. Analyze request/response timing
```

**Common Status Codes:**
- 200: OK
- 301/302: Redirect
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 500: Internal Server Error
- 503: Service Unavailable

## Best Practices

### 1. Use Appropriate Filters

```
# Start broad, narrow down
http → http.request → http.request.method == "POST"
```

### 2. Save Captures Strategically

```
# Name captures descriptively
problem_description_2024-01-15.pcap
```

### 3. Use Display Filters, Not Capture Filters for Analysis

```
Capture: Minimize what you save
Display: Analyze what you captured
```

### 4. Learn Keyboard Shortcuts

```
Ctrl+E:  Stop capture
Ctrl+K:  Start new capture
Ctrl+F:  Find packet
Ctrl+G:  Go to packet
Ctrl+M:  Mark packet
Ctrl+N:  Next packet
Ctrl+P:  Previous packet
```

### 5. Export What You Need

```
File → Export:
- Specified Packets
- Packet Dissections
- Objects (HTTP, SMB, etc.)
```

### 6. Use Profiles

```
Create profiles for different tasks:
- Troubleshooting
- Security analysis
- Protocol development

Edit → Configuration Profiles
```

### 7. Document Findings

```
Add comments to packets:
Right-click → Edit Packet Comment
```

## Common Wireshark Use Cases

**1. Network Troubleshooting:**
- Connectivity issues
- Performance problems
- Protocol errors

**2. Security Analysis:**
- Detecting attacks
- Analyzing malware traffic
- Investigating breaches

**3. Application Development:**
- Debugging network code
- Verifying protocol implementation
- Testing APIs

**4. Learning:**
- Understanding protocols
- Studying network behavior
- CCNA/CCNP preparation

**5. Forensics:**
- Incident investigation
- Evidence collection
- Timeline reconstruction

## Command-Line Alternative: tshark

**tshark** is command-line version of Wireshark.

**Basic Usage:**
```bash
# List interfaces
tshark -D

# Capture on interface
tshark -i eth0

# Capture to file
tshark -i eth0 -w capture.pcap

# Read from file
tshark -r capture.pcap

# With display filter
tshark -r capture.pcap -Y "http.request"

# Display specific fields
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.port
```

## Security Considerations

**Ethical Use:**
- ⚠️ Only capture traffic you're authorized to monitor
- ⚠️ Be aware of privacy laws
- ⚠️ Don't capture on networks you don't own/administer
- ⚠️ Encrypted traffic requires keys to decrypt

**Password Security:**
- Wireshark can capture plaintext passwords
- Use encrypted protocols (HTTPS, SSH, SFTP)
- Be careful with captured files containing sensitive data

## Summary

- **Wireshark** is essential network analysis tool
- **Capture filters** reduce capture size (BPF syntax)
- **Display filters** analyze captured data (more powerful)
- **Follow TCP Stream** reassembles conversations
- **Statistics** provide traffic overviews
- **Color coding** highlights important packets
- **Troubleshooting** uses systematic filter approach
- **Best practices** improve efficiency and accuracy

## Practice Exercises

1. Capture and analyze your web browsing traffic
2. Filter HTTP GET requests to specific domain
3. Follow a TCP stream for complete conversation
4. Identify DNS queries and responses
5. Analyze TCP three-way handshake
6. Find retransmitted packets
7. Export HTTP objects from capture
8. Create custom display filter for specific port range

## Resources

**Official:**
- Website: https://www.wireshark.org
- Documentation: https://www.wireshark.org/docs/
- Wiki: https://wiki.wireshark.org

**Learning:**
- Sample captures: https://wiki.wireshark.org/SampleCaptures
- Display filter reference: https://www.wireshark.org/docs/dfref/

## Next Steps

- Practice with [Packet Flow](../packet-flow/README.md) examples
- Apply to [TCP/UDP](../tcp-udp/README.md) analysis
- Use for troubleshooting [Routing](../routing/README.md) issues
- Analyze [VLANs](../vlans/README.md) and trunking
