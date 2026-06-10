# Subnetting Notes

## IP Address Classes

| Class | Range | Default Mask | Prefix |
|---------|---------|---------|---------|
| A | 1 - 126 | 255.0.0.0 | /8 |
| B | 128 - 191 | 255.255.0.0 | /16 |
| C | 192 - 223 | 255.255.255.0 | /24 |
| D | 224 - 239 | Multicast | N/A |
| E | 240 - 255 | Experimental | N/A |

### Special Addresses

- 127.x.x.x → Loopback
- 0.0.0.0 → Default Route
- 255.255.255.255 → Broadcast

---

# Subnet Mask and Prefix

A prefix tells how many bits belong to the network.

Example:

```
192.168.1.0/24
```

- Network Bits = 24
- Host Bits = 8

Subnet Mask:

```
11111111.11111111.11111111.00000000
=
255.255.255.0
```

---

# Formula: Host Bits

```
Host Bits = 32 - Prefix
```

Example:

```
/24

Host Bits = 32 - 24
          = 8
```

---

# Formula: Usable Hosts

```
Usable Hosts = 2^(Host Bits) - 2
```

Why -2?

- One Network Address
- One Broadcast Address

Example:

```
/24

Hosts = 2^8 - 2
       = 256 - 2
       = 254
```

---

# Formula: Number of Networks

```
Number of Networks = 2^(Borrowed Bits)
```

Borrowed Bits = Bits carried from host portion.

Example:

```
Default Class C = /24

New Prefix = /27

Borrowed Bits = 27 - 24
              = 3

Networks = 2^3
         = 8
```

---

# Borrowing (Carrying) Bits

Example:

```
Class C Default

192.168.1.0/24
```

Need more subnets:

```
/26
```

Borrowed:

```
26 - 24 = 2 bits
```

Result:

```
Networks = 2^2 = 4

Hosts = 2^6 - 2
      = 62
```

---

# Block Size Formula

```
Block Size = 256 - Interesting Octet
```

Example:

```
Mask = 255.255.255.192
```

Interesting Octet:

```
192
```

Block Size:

```
256 - 192 = 64
```

Subnets:

```
0
64
128
192
```

---

# Range Formula

```
Current Subnet
to
Next Subnet - 1
```

Example:

```
192.168.1.64/26
```

Next Subnet:

```
192.168.1.128
```

Range:

```
192.168.1.64
to
192.168.1.127
```

---

# Broadcast Address

Formula:

```
Broadcast = Next Subnet - 1
```

Example:

```
Network = 192.168.1.64/26

Next Subnet = 192.168.1.128

Broadcast = 192.168.1.127
```

---

# Valid Host Range

Formula:

```
First Host = Network + 1

Last Host = Broadcast - 1
```

Example:

```
Network = 192.168.1.64

Broadcast = 192.168.1.127
```

Hosts:

```
192.168.1.65
to
192.168.1.126
```

---

# Right to Left = Host Calculation

Host bits are counted from the right.

Example:

```
/27

11111111.11111111.11111111.11100000
```

Host Bits:

```
00000
```

Host Count:

```
2^5 - 2
= 30
```

Rule:

```
Right → Left = Host Calculation
```

---

# Left to Right = Network Calculation

Network bits are counted from the left.

Example:

```
/27

11111111.11111111.11111111.11100000
```

Network Bits:

```
27
```

Rule:

```
Left → Right = Network Calculation
```

---

# Quick Reference Table

| Prefix | Mask | Usable Hosts |
|----------|----------|----------|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /29 | 255.255.255.248 | 6 |
| /30 | 255.255.255.252 | 2 |
| /31 | 255.255.255.254 | Point-to-Point |
| /32 | 255.255.255.255 | Single Host |

---

# Point-to-Point Link

Used when exactly two devices need communication.

Examples:

- Router ↔ Router
- Client ↔ Client
- Vendor ↔ Vendor
- ISP ↔ Customer Router

Most commonly:

```
/30
```

Mask:

```
255.255.255.252
```

Host Calculation:

```
2^(2) - 2
= 2 Hosts
```

Example:

```
Network   : 10.0.0.0/30

Host 1    : 10.0.0.1

Host 2    : 10.0.0.2

Broadcast : 10.0.0.3
```

Provides exactly two usable IP addresses.

---

# Fast Subnetting Steps

### Step 1
Identify Prefix

### Step 2
Find Host Bits

```
Host Bits = 32 - Prefix
```

### Step 3
Find Hosts

```
Hosts = 2^(Host Bits) - 2
```

### Step 4
Find Interesting Octet

### Step 5
Find Block Size

```
Block Size = 256 - Interesting Octet
```

### Step 6
Generate Subnets

### Step 7
Find Broadcast

```
Broadcast = Next Subnet - 1
```

### Step 8
Find Host Range

```
Network + 1
to
Broadcast - 1
```