# Subnetting

## Overview

Subnetting is the process of dividing a large network into smaller, more manageable sub-networks (subnets). This improves network performance, security, and efficient IP address utilization.

## Why Subnet?

**Benefits of Subnetting:**
1. **Efficient IP address utilization** - Avoid wasting addresses
2. **Improved performance** - Reduced broadcast domains
3. **Enhanced security** - Logical separation of networks
4. **Simplified management** - Organized network structure
5. **Reduced network congestion** - Smaller collision domains

## Subnet Mask

A subnet mask determines which portion of an IP address represents the network and which represents the host.

### Decimal and Binary Representation

**Class C Example:**
```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
Binary Mask:   11111111.11111111.11111111.00000000
               [    Network Portion    ][Host]
```

### Common Subnet Masks

| CIDR | Dotted Decimal | Binary (last octet) | Hosts | Networks |
|------|---------------|---------------------|-------|----------|
| /24 | 255.255.255.0 | 00000000 | 254 | 1 |
| /25 | 255.255.255.128 | 10000000 | 126 | 2 |
| /26 | 255.255.255.192 | 11000000 | 62 | 4 |
| /27 | 255.255.255.224 | 11100000 | 30 | 8 |
| /28 | 255.255.255.240 | 11110000 | 14 | 16 |
| /29 | 255.255.255.248 | 11111000 | 6 | 32 |
| /30 | 255.255.255.252 | 11111100 | 2 | 64 |

## CIDR Notation

**CIDR (Classless Inter-Domain Routing)** is a compact representation of an IP address and its subnet mask.

**Format:** `IP Address/Prefix Length`

**Examples:**
- `192.168.1.0/24` - Network with 256 addresses (254 usable)
- `10.0.0.0/8` - Large network with 16,777,216 addresses
- `172.16.0.0/16` - Medium network with 65,536 addresses

**Prefix Length** = Number of consecutive 1s in subnet mask

## Subnetting Calculations

### Key Formulas

1. **Number of Subnets:**
   ```
   2^n (where n = borrowed bits)
   ```

2. **Number of Hosts per Subnet:**
   ```
   2^h - 2 (where h = host bits)
   Subtract 2 for network and broadcast addresses
   ```

3. **Block Size (Increment):**
   ```
   256 - subnet mask value in interesting octet
   ```

### The Magic Number Method

**Steps:**
1. Identify the interesting octet (where subnetting occurs)
2. Calculate block size: 256 - subnet mask value
3. Start at 0 and count by block size
4. List all subnet ranges

## Practical Subnetting Examples

### Example 1: Simple Subnetting (/26)

**Requirement:** Divide `192.168.1.0/24` into subnets with 50 hosts each

**Solution:**
1. **Calculate hosts needed:** 50 + network + broadcast = 52
2. **Host bits required:** 2^6 = 64 (need 6 bits)
3. **New subnet mask:** 32 - 6 = /26 or 255.255.255.192
4. **Block size:** 256 - 192 = 64
5. **Number of subnets:** 256 ÷ 64 = 4

**Subnet Breakdown:**

| Subnet | Network Address | First Host | Last Host | Broadcast | Hosts |
|--------|----------------|------------|-----------|-----------|-------|
| 1 | 192.168.1.0 | 192.168.1.1 | 192.168.1.62 | 192.168.1.63 | 62 |
| 2 | 192.168.1.64 | 192.168.1.65 | 192.168.1.126 | 192.168.1.127 | 62 |
| 3 | 192.168.1.128 | 192.168.1.129 | 192.168.1.190 | 192.168.1.191 | 62 |
| 4 | 192.168.1.192 | 192.168.1.193 | 192.168.1.254 | 192.168.1.255 | 62 |

### Example 2: Finding Subnet Information

**Given:** IP Address `172.16.35.123/20`

**Question:** Find network, broadcast, and host range

**Solution:**
1. **Subnet mask:** /20 = 255.255.240.0
2. **Interesting octet:** Third octet (240)
3. **Block size:** 256 - 240 = 16
4. **Subnets:** 0, 16, 32, 48...
5. **IP 35 falls in range:** 32-47

**Answer:**
- Network: `172.16.32.0`
- First Host: `172.16.32.1`
- Last Host: `172.16.47.254`
- Broadcast: `172.16.47.255`
- Hosts: 4,094

### Example 3: VLSM Subnetting

**Scenario:** Company needs:
- 1 subnet with 100 hosts (Sales)
- 2 subnets with 50 hosts each (HR, IT)
- 3 subnets with 10 hosts each (Management, Finance, R&D)

**Given network:** `192.168.10.0/24`

**Solution (using VLSM - largest to smallest):**

**Step 1: Sales Department (100 hosts)**
- Hosts needed: 100
- Host bits: 2^7 = 128 (use /25)
- Network: `192.168.10.0/25`
- Range: 192.168.10.0 - 192.168.10.127
- Usable: 192.168.10.1 - 192.168.10.126

**Step 2: HR Department (50 hosts)**
- Host bits: 2^6 = 64 (use /26)
- Network: `192.168.10.128/26`
- Range: 192.168.10.128 - 192.168.10.191
- Usable: 192.168.10.129 - 192.168.10.190

**Step 3: IT Department (50 hosts)**
- Network: `192.168.10.192/26`
- Range: 192.168.10.192 - 192.168.10.255

**Step 4: Management (10 hosts)**
- Host bits: 2^4 = 16 (use /28)
- Would use: `192.168.11.0/28` (if expanding network)

### Example 4: Point-to-Point Links (/30)

**Use case:** Router-to-router connections

**Network:** `10.1.1.0/30`

```
Block size: 256 - 252 = 4 addresses per subnet

Link 1:
  Network:    10.1.1.0
  Router A:   10.1.1.1
  Router B:   10.1.1.2
  Broadcast:  10.1.1.3

Link 2:
  Network:    10.1.1.4
  Router C:   10.1.1.5
  Router D:   10.1.1.6
  Broadcast:  10.1.1.7
```

## VLSM (Variable Length Subnet Masking)

VLSM allows using different subnet masks within the same network, enabling more efficient IP address utilization.

### VLSM Rules

1. **Start with the largest subnet requirement**
2. **Subnet in order from largest to smallest**
3. **Never overlap subnets**
4. **Document everything clearly**

### VLSM Example

**Given:** `172.30.0.0/16` needs to be subnetted for:
- Branch A: 4,000 hosts
- Branch B: 2,000 hosts
- Branch C: 500 hosts
- 10 point-to-point links: 2 hosts each

**Solution:**

```
Branch A (4,000 hosts):
  Need: 2^12 = 4,096 → /20
  Network: 172.30.0.0/20
  Range: 172.30.0.0 - 172.30.15.255

Branch B (2,000 hosts):
  Need: 2^11 = 2,048 → /21
  Network: 172.30.16.0/21
  Range: 172.30.16.0 - 172.30.23.255

Branch C (500 hosts):
  Need: 2^9 = 512 → /23
  Network: 172.30.24.0/23
  Range: 172.30.24.0 - 172.30.25.255

Point-to-Point Links (2 hosts each):
  Need: 2^2 = 4 → /30
  Link 1: 172.30.26.0/30
  Link 2: 172.30.26.4/30
  Link 3: 172.30.26.8/30
  ... and so on
```

## Supernetting (Route Summarization)

Supernetting combines multiple networks into a single routing entry, reducing routing table size.

### Example: Route Summarization

**Networks to summarize:**
```
192.168.16.0/24
192.168.17.0/24
192.168.18.0/24
192.168.19.0/24
```

**Process:**
1. Convert to binary:
   ```
   192.168.00010000.0
   192.168.00010001.0
   192.168.00010010.0
   192.168.00010011.0
   ```

2. Find common bits: `00010` (first 5 bits match)

3. **Summary route:** `192.168.16.0/21`
   - Covers 192.168.16.0 through 192.168.23.255

## Subnetting Tools and Shortcuts

### Quick Reference Chart

**Host Requirements → Subnet Mask:**

| Hosts Needed | Host Bits | Subnet Mask | CIDR | Usable Hosts |
|--------------|-----------|-------------|------|--------------|
| 2 | 2 | 255.255.255.252 | /30 | 2 |
| 6 | 3 | 255.255.255.248 | /29 | 6 |
| 14 | 4 | 255.255.255.240 | /28 | 14 |
| 30 | 5 | 255.255.255.224 | /27 | 30 |
| 62 | 6 | 255.255.255.192 | /26 | 62 |
| 126 | 7 | 255.255.255.128 | /25 | 126 |
| 254 | 8 | 255.255.255.0 | /24 | 254 |
| 510 | 9 | 255.255.254.0 | /23 | 510 |
| 1,022 | 10 | 255.255.252.0 | /22 | 1,022 |

### Powers of 2 Reference

```
2^1 = 2       2^9 = 512
2^2 = 4       2^10 = 1,024
2^3 = 8       2^11 = 2,048
2^4 = 16      2^12 = 4,096
2^5 = 32      2^13 = 8,192
2^6 = 64      2^14 = 16,384
2^7 = 128     2^15 = 32,768
2^8 = 256     2^16 = 65,536
```

## Subnetting Practice Problems

### Problem 1
**Given:** 200.1.1.0/24
**Task:** Create 8 subnets

<details>
<summary>Solution</summary>

- Subnets needed: 8 → 2^3 = 8 (borrow 3 bits)
- New mask: /27 (255.255.255.224)
- Block size: 32
- Subnets: 200.1.1.0/27, 200.1.1.32/27, 200.1.1.64/27...
</details>

### Problem 2
**Given:** 10.5.120.77/22
**Task:** What is the subnet address?

<details>
<summary>Solution</summary>

- Mask: 255.255.252.0
- Interesting octet: 3rd (252)
- Block size: 4
- Multiples of 4: 0, 4, 8, 12... 116, 120, 124
- 120 falls in 120-123 range
- **Subnet: 10.5.120.0**
</details>

### Problem 3
**Given:** Need 30 subnets with at least 2,000 hosts each
**Task:** What network and mask?

<details>
<summary>Solution</summary>

- Hosts: 2,000 → need 2^11 = 2,048 (11 host bits)
- Subnets: 30 → need 2^5 = 32 (5 subnet bits)
- Total bits: 5 + 11 = 16 bits
- Use Class B: 172.16.0.0/16
- New mask: /21 (16 + 5 subnet bits)
- **Answer: Class B network with /21 mask**
</details>

## Common Mistakes to Avoid

1. **Forgetting to subtract 2** for network and broadcast addresses
2. **Confusing CIDR notation** with subnet mask values
3. **Not allowing for growth** - always plan for expansion
4. **Overlapping subnets** when using VLSM
5. **Wrong interesting octet** - identify where subnetting occurs

## Best Practices

1. **Document everything** - Keep detailed records of subnet allocations
2. **Plan for growth** - Allocate more addresses than currently needed (25-30% buffer)
3. **Use consistent patterns** - Makes troubleshooting easier
4. **Implement VLSM** - Maximize address efficiency
5. **Test your calculations** - Verify with online calculators
6. **Use /31 for P2P links** - Saves addresses (RFC 3021)

## Summary

- **Subnetting** divides networks into smaller, more efficient segments
- **Subnet masks** define network and host portions
- **CIDR notation** provides compact representation (e.g., /24)
- **VLSM** enables variable-sized subnets for efficient addressing
- **Key formula:** Hosts = 2^h - 2
- **Practice** is essential for mastering subnetting

## Tools and Resources

**Online Subnet Calculators:**
- CIDR Calculator
- IP Subnet Calculator
- Visual Subnet Calculator

**Practice Sites:**
- Subnetting Practice
- Subnet Quiz
- CCNA Subnetting Exercises

## Next Steps

- Review [IP Addressing](../ip-addressing/README.md) for foundational concepts
- Continue to [Routing](../routing/README.md) to learn how subnets communicate
- Practice with [Wireshark](../wireshark/README.md) to see subnetting in action
