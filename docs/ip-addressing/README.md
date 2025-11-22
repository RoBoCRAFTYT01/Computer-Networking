# IP Addressing

## Overview

IP (Internet Protocol) addressing is the fundamental method for identifying and locating devices on a network. An IP address is a unique numerical label assigned to each device connected to a network.

## IPv4 Addressing

### Structure

IPv4 addresses are 32-bit numbers typically written in **dotted decimal notation**:
- Format: `xxx.xxx.xxx.xxx`
- Each octet ranges from 0 to 255
- Example: `192.168.1.100`

**Binary Representation:**
```
192.168.1.100 in decimal
11000000.10101000.00000001.01100100 in binary
```

### IPv4 Address Classes

IPv4 addresses are divided into classes based on the first octet:

| Class | First Octet Range | Default Mask | Network/Host Bits | Purpose |
|-------|-------------------|--------------|-------------------|---------|
| A | 1-126 | 255.0.0.0 (/8) | 8/24 | Large networks |
| B | 128-191 | 255.255.0.0 (/16) | 16/16 | Medium networks |
| C | 192-223 | 255.255.255.0 (/24) | 24/8 | Small networks |
| D | 224-239 | N/A | N/A | Multicast |
| E | 240-255 | N/A | N/A | Experimental |

**Examples:**
- Class A: `10.0.0.1` - Large enterprise networks
- Class B: `172.16.0.1` - Campus networks
- Class C: `192.168.1.1` - Home/small office networks

### Public vs. Private IP Addresses

**Private IP Address Ranges (RFC 1918):**
```
Class A: 10.0.0.0 to 10.255.255.255 (10.0.0.0/8)
Class B: 172.16.0.0 to 172.31.255.255 (172.16.0.0/12)
Class C: 192.168.0.0 to 192.168.255.255 (192.168.0.0/16)
```

**Key Differences:**

| Private IP | Public IP |
|------------|-----------|
| Used within internal networks | Used on the Internet |
| Not routable on the Internet | Globally routable |
| Free to use | Must be assigned by ISP/RIR |
| Requires NAT for Internet access | Directly accessible |

### Special IP Addresses

**Important Reserved Addresses:**

1. **Loopback Address:**
   - Range: `127.0.0.0/8`
   - Most common: `127.0.0.1`
   - Purpose: Testing network stack on local machine
   - Example: `ping 127.0.0.1`

2. **Network Address:**
   - First address in a subnet
   - All host bits set to 0
   - Example: `192.168.1.0/24` → Network is `192.168.1.0`

3. **Broadcast Address:**
   - Last address in a subnet
   - All host bits set to 1
   - Example: `192.168.1.0/24` → Broadcast is `192.168.1.255`

4. **APIPA (Automatic Private IP Addressing):**
   - Range: `169.254.0.0/16`
   - Auto-assigned when DHCP fails
   - Indicates network configuration problem

5. **Default Route:**
   - `0.0.0.0/0`
   - Represents "any network"
   - Used in routing tables

## IPv6 Addressing

### Why IPv6?

IPv4 provides approximately 4.3 billion addresses, which is insufficient for the growing number of devices. IPv6 provides 340 undecillion addresses.

### Structure

IPv6 addresses are 128-bit numbers written in **hexadecimal notation**:
- Format: `xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx`
- Eight groups of four hexadecimal digits
- Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

**Shorthand Rules:**
1. Leading zeros can be omitted: `2001:0db8` → `2001:db8`
2. Consecutive groups of zeros can be replaced with `::` (only once)
   - `2001:0db8:0000:0000:0000:0000:0000:0001` → `2001:db8::1`

### IPv6 Address Types

1. **Unicast:**
   - Global Unicast: `2000::/3` (routable on Internet)
   - Link-Local: `fe80::/10` (local network only)
   - Unique Local: `fc00::/7` (private addressing)

2. **Multicast:**
   - Range: `ff00::/8`
   - Replaces broadcast in IPv4

3. **Anycast:**
   - Delivered to nearest node in a group

### IPv6 Loopback and Special Addresses

- **Loopback:** `::1` (equivalent to 127.0.0.1 in IPv4)
- **Unspecified:** `::` (equivalent to 0.0.0.0 in IPv4)
- **Link-Local:** `fe80::/10` (auto-configured on all interfaces)

## Practical Examples

### Example 1: Identifying Address Class

**Question:** What class is `172.25.30.15`?

**Solution:**
- First octet: 172
- Range: 128-191
- **Answer: Class B**

### Example 2: Determining Network and Broadcast

**Given:** IP: `192.168.10.50/24`

**Solution:**
- Subnet mask: `/24` = `255.255.255.0`
- Network address: `192.168.10.0` (all host bits = 0)
- Broadcast address: `192.168.10.255` (all host bits = 1)
- Usable host range: `192.168.10.1` to `192.168.10.254`
- Total usable hosts: 254

### Example 3: IPv6 Address Shortening

**Original:** `2001:0db8:0000:0000:0000:ff00:0042:8329`

**Step-by-step shortening:**
1. Remove leading zeros: `2001:db8:0:0:0:ff00:42:8329`
2. Replace consecutive zeros: `2001:db8::ff00:42:8329`

**Final:** `2001:db8::ff00:42:8329`

## Address Configuration Methods

### Static IP Configuration

Manually assigning IP addresses to devices:
```
IP Address: 192.168.1.100
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
DNS Server: 8.8.8.8
```

**Use Cases:**
- Servers
- Network devices (routers, switches)
- Printers
- Critical infrastructure

### Dynamic IP Configuration (DHCP)

DHCP (Dynamic Host Configuration Protocol) automatically assigns IP addresses.

**DHCP Process (DORA):**
1. **Discover:** Client broadcasts to find DHCP server
2. **Offer:** Server offers an IP address
3. **Request:** Client requests the offered address
4. **Acknowledge:** Server confirms the assignment

**DHCP Lease Information:**
- IP Address
- Subnet Mask
- Default Gateway
- DNS Servers
- Lease Duration

## Common Commands

### Windows
```cmd
ipconfig                    # View IP configuration
ipconfig /all              # Detailed IP information
ipconfig /release          # Release DHCP lease
ipconfig /renew            # Renew DHCP lease
ping 192.168.1.1           # Test connectivity
```

### Linux/macOS
```bash
ifconfig                   # View network interfaces (older)
ip addr show              # View IP addresses (modern)
ip route show             # View routing table
ping 192.168.1.1          # Test connectivity
dhclient -r               # Release DHCP lease
dhclient                  # Renew DHCP lease
```

## Troubleshooting IP Addressing Issues

### Common Problems and Solutions

1. **APIPA Address (169.254.x.x):**
   - **Problem:** Cannot reach DHCP server
   - **Solution:** Check network cable, DHCP server status

2. **Duplicate IP Address:**
   - **Problem:** Two devices with same IP
   - **Solution:** Use DHCP or verify static IP assignments

3. **Wrong Subnet:**
   - **Problem:** Device in wrong subnet for gateway
   - **Solution:** Correct IP or subnet mask

4. **No Default Gateway:**
   - **Problem:** Cannot reach other networks
   - **Solution:** Configure default gateway

## Best Practices

1. **Document your IP addressing scheme**
   - Maintain spreadsheets or IPAM tools
   - Record static IP assignments

2. **Use private IP ranges for internal networks**
   - Saves public IP addresses
   - Provides security through NAT

3. **Plan for growth**
   - Don't use all addresses in initial deployment
   - Leave room for expansion

4. **Consistent addressing**
   - Use predictable patterns (e.g., .1 for gateway)
   - Group similar devices in same subnets

5. **Implement DHCP with reservations**
   - Automatic for most devices
   - Reservations for servers/infrastructure

## Summary

- **IPv4** uses 32-bit addresses in dotted decimal notation
- **IPv6** uses 128-bit addresses in hexadecimal notation
- **Address classes** (A, B, C) define network size
- **Private addresses** are used for internal networks
- **Special addresses** serve specific purposes (loopback, broadcast)
- **DHCP** automates IP address assignment
- Proper IP addressing is fundamental to network functionality

## Practice Exercises

1. Identify the class of these addresses: `10.5.2.1`, `172.31.255.254`, `200.100.50.25`
2. Determine if these are public or private: `192.168.50.1`, `8.8.8.8`, `172.20.0.5`
3. Shorten this IPv6 address: `2001:0000:0000:0001:0000:0000:0000:0001`
4. What is the broadcast address for `10.50.100.0/24`?

## Next Steps

- Continue to [Subnetting](../subnetting/README.md) to learn how to divide networks
- Review [OSI Model](../osi-model/README.md) to understand where IP fits in networking
