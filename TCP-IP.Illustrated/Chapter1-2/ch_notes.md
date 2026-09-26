# TCP/IP Illustrated — Chapters 1–2 Cyber Notes

---

## 0. The mental model I want to remember

```text
Application
    ↓
TCP / UDP
    ↓
IP (IPv4 / IPv6)
    ↓
Ethernet / Wi-Fi / other Link Layer
```

- **IP**: addressing + routing at the network layer.
- **TCP/UDP**: transport between application endpoints; ports identify the application endpoint.
- **Ethernet**: local-link delivery using MAC addresses.
- **Encapsulation**: each layer adds its own header information.
- **Address ≠ packet**: an IP address identifies an endpoint/interface; an IP packet is the actual unit carrying headers + payload.

Example:

```text
IPv4 address  = 192.168.1.10
TCP port      = 8080

"Who/where?"  → IP address
"Which app?"  → port
"How is data transported reliably?" → TCP
```

---

# 1. IPv4 and IPv6 Address Space

## IPv4

IPv4 addresses are 32 bits:

```text
2^32 = 4,294,967,296
```

An IPv4 address is commonly viewed as:

```text
[ Network portion | Host portion ]
```

The exact split depends on the prefix length (modern CIDR) or, historically, on the classful rules.

## IPv6

IPv6 addresses are 128 bits:

```text
2^128
= 340,282,366,920,938,463,463,374,607,431,768,211,456
```

IPv6 notation normally uses eight 16-bit hexadecimal groups and permits zero compression with `::`.

Example:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
                ↓
2001:db8::1
```

---

# 2. Network Number / Host Number / Prefix

Historically, IPv4 was conceptually divided into:

```text
[ Network Number | Host Number ]
```

- **Network number / network portion**: identifies the network.
- **Host portion**: identifies an address/interface within that network.

With modern CIDR, think in terms of **prefix length** rather than Class A/B/C.

Example:

```text
192.168.1.0/24
```

means:

```text
24 bits → network prefix
 8 bits → host portion
```

So the possible bit patterns for the host portion are:

```text
00000000
00000001
00000010
...
11111111
```

"Free bits" means bits that are available to vary when constructing addresses in that block. It does **not** mean that those bits contain zero.

### Important distinction

```text
Network bits → identify the network/prefix
Host bits    → distinguish addresses/interfaces inside that prefix
```

---

# 3. Classful IPv4 Addressing — Historical Background

Before CIDR, IPv4 used five classes:

```text
Class A → 0 | 7 network bits  | 24 host bits
Class B → 10 | 14 network bits | 16 host bits
Class C → 110 | 21 network bits | 8 host bits
Class D → multicast
Class E → reserved (historically)
```

The key problem was allocation efficiency.

A site needing about 1,000 hosts could not fit into a normal Class C (~254 usable hosts), so it might receive a Class B (~65k addresses), wasting a large amount of address space.

This contributed to rapid Class B exhaustion.

The Internet also faced a second scaling problem: more networks meant more routing-table entries.

---

# 4. Why CIDR and Aggregation Were Needed

Three historical pressures:

1. Class B address space was being consumed rapidly.
2. 32-bit IPv4 address space was considered too small for future Internet growth.
3. Global routing tables were growing, making routing less scalable.

## CIDR

CIDR = **Classless Inter-Domain Routing**.

Instead of saying:

```text
"Class B"
```

we can say:

```text
192.168.1.0/24
192.168.0.0/22
10.0.0.0/8
```

The `/N` tells us how many leading bits belong to the prefix.

## Aggregation

Multiple adjacent prefixes can sometimes be represented by a larger prefix.

Example:

```text
190.154.27.0/26   → .0–.63
190.154.27.64/26  → .64–.127
```

These are contiguous, so:

```text
190.154.27.0/25   → .0–.127
```

Add:

```text
190.154.27.128/26 → .128–.191
190.154.27.192/26 → .192–.255
```

and all four `/26`s become:

```text
190.154.27.0/24
```

Then add the adjacent `/24`:

```text
190.154.26.0/24
```

and together:

```text
190.154.26.0/23
```

### Rule

Aggregation is not simply "the addresses look close". The blocks must be structurally aligned so that a larger prefix covers exactly the intended address space.

---

# 5. Hierarchical Addressing

A routing hierarchy becomes much more scalable when addresses reflect topology.

Bad idea:

```text
Router A → random prefix
Router B → unrelated prefix
Router C → unrelated prefix
Router D → unrelated prefix
```

Better:

```text
                 Core
               /      \
          10.1.0.0/16  10.2.0.0/16
             /  \          /  \
           A     B        C     D
```

Now an upstream router may only need:

```text
10.1.0.0/16 → left side
10.2.0.0/16 → right side
```

Instead of separate routes for every child network.

### Mental model

```text
Topology
   ↓
Hierarchical prefixes
   ↓
Aggregation / summarization
   ↓
Smaller routing tables
```

---

# 6. Longest Prefix Match

When multiple routes match a destination, the routing system generally prefers the **longest/more-specific matching prefix**.

Example:

```text
12.0.0.0/8
12.46.129.0/25
```

Destination:

```text
12.46.129.1
```

Both routes match, but:

```text
/25 > /8
```

so the more-specific `/25` route wins.

### Mental model

```text
Many matching prefixes
        ↓
choose the one with the most matching leading bits
```

This becomes extremely important when studying routing, BGP, multihoming, and route leaks.

---

# 7. Unicast / Broadcast / Multicast / Anycast

## Unicast

One address identifies one destination/interface.

```text
A ─────→ B
```

## Broadcast (IPv4)

One destination address represents all hosts in a broadcast domain/local network, depending on the broadcast type.

```text
        ┌→ B
A ──────┼→ C
        └→ D
```

## Multicast

One address identifies a **group** of interfaces.

```text
        ┌→ B ✓
A ──────┼→ C ✗
        └→ D ✓
```

Receivers can join/leave groups.

## Anycast

Anycast uses a **unicast address** that is advertised from multiple locations. Routing chooses one destination/instance of the service.

```text
          ┌→ Server A
Client ─── Anycast IP
          └→ Server B
```

It is not a separate "address format" like IPv6 multicast; it is a routing/service usage of a unicast address.

### One-line memory aid

```text
Unicast   → one
Broadcast → everyone in the relevant broadcast domain
Multicast → a group
Anycast   → one of several instances using the same unicast address
```

---

# 8. Ethernet Layer-2 Broadcast

Ethernet interfaces use MAC addresses.

Unicast Ethernet frame:

```text
Source MAC      → Destination MAC
AA:AA:...       → BB:BB:...
```

Ethernet broadcast uses:

```text
FF:FF:FF:FF:FF:FF
```

A switch recognizes a broadcast destination and forwards the frame out the appropriate ports except the incoming port.

Example:

```text
             Switch
           /   |   \
         PC1  PC2  PC3
          ↑
          │
Destination MAC = FF:FF:FF:FF:FF:FF
```

### Relation to IPv4 broadcast

A packet can have:

```text
Destination IP  = 255.255.255.255
Destination MAC = FF:FF:FF:FF:FF:FF
```

Different layers are doing different jobs:

```text
Layer 3 → IPv4 broadcast destination
Layer 2 → Ethernet broadcast destination
```

The host creates the broadcast frame; the switch propagates it within the Layer-2 broadcast domain.

### Useful example: ARP

ARP is a classic practical example of Layer-2 broadcast. A host can broadcast a request such as:

```text
"Who has 192.168.1.20?"
```

The Ethernet destination is the broadcast MAC, so devices on the local LAN can receive the request.

---

# 9. IPv4 Broadcast Addresses

## Subnet broadcast

For:

```text
128.32.1.0/24
```

host bits = 8 bits.

Set all host bits to 1:

```text
11111111₂ = 255
```

Therefore:

```text
Network    = 128.32.1.0
Broadcast  = 128.32.1.255
```

Rule:

```text
Broadcast = network prefix + all host bits set to 1
```

## Directed broadcast

A subnet broadcast such as:

```text
128.32.1.255
```

identifies the broadcast destination for the subnet:

```text
128.32.1.0/24
```

Conceptually, a directed broadcast could be routed toward the target subnet and then delivered to all hosts on that subnet. Modern networks commonly filter/disable directed broadcasts because of abuse potential.

## Limited broadcast

```text
255.255.255.255
```

This means **local-network broadcast** and is not forwarded by routers.

It is different from a subnet-directed broadcast:

```text
128.32.1.255       → specific subnet's broadcast
255.255.255.255    → local/limited broadcast
```

---

# 10. Link-Layer Broadcast vs Router Forwarding

A router normally separates Layer-2 broadcast domains.

```text
LAN A
PC1 ─ Switch ─ Router ─ Switch ─ PC2
LAN B
```

A Layer-2 Ethernet broadcast on LAN A does not automatically become an Ethernet broadcast on LAN B.

That is why the router is a boundary for ordinary L2 broadcasts.

However, local broadcast delivery does not require a router at all; the switch/link-layer mechanism can distribute the frame locally.

---

# 11. Why Broadcast Is Common with UDP/ICMP and Not TCP

TCP is connection-oriented and represents communication between two transport endpoints.

```text
Client ←── TCP connection ──→ Server
```

UDP is connectionless and is naturally suited to one-to-many patterns.

```text
Sender
  │
  ├→ Receiver 1
  ├→ Receiver 2
  └→ Receiver 3
```

This does not mean UDP is "the broadcast protocol"; it means UDP is much easier to use in broadcast/multicast application designs than TCP's two-party connection model.

---

# 12. IPv6 Scope

IPv6 adds an explicit concept of **scope** for many address types, especially multicast.

Scope answers:

> "Where can this address/traffic be used or how far can it reach?"

Important examples:

```text
Node-local   → same machine
Link-local   → same link / subnet context
Global       → Internet-wide scope
```

### Node-local

Communication constrained to the same node.

### Link-local

The address is usable only on the local link.

The well-known IPv6 link-local unicast range is:

```text
fe80::/10
```

### Global

Global unicast addresses can be routed across the Internet.

---

# 13. IPv6 Nodes Commonly Have Multiple Addresses

A single interface can have several IPv6 addresses at the same time.

Example:

```text
Ethernet interface
│
├── fe80::1234              ← link-local
└── 2001:db8:1234::1234     ← global/documentation-style example
```

So do not assume:

```text
one interface = one IP
```

IPv6 commonly uses multiple addresses with different purposes/scopes.

### Important TCP/IP distinction

TCP provides transport communication; IPv6 provides network-layer addressing and routing. The IPv6 address identifies the network endpoint/interface, while the transport port helps identify the application endpoint.

---

# 14. IPv4 Special-Use Addresses — Core Cheat Sheet

## Private address ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Used for private/internal networks. They are not global Internet destinations.

Common architecture:

```text
Private hosts
192.168.1.x
     │
   Router/NAT
     │
 Public IPv4
     │
  Internet
```

## Loopback

```text
127.0.0.0/8
```

Most common:

```text
127.0.0.1
```

Means the local host itself.

Security/programming relevance:

```text
127.0.0.1:8080
```

can represent a service bound for local-host access.

## IPv4 link-local

```text
169.254.0.0/16
```

Commonly associated with automatic local-link addressing when DHCP configuration is unavailable.

## Documentation ranges

```text
192.0.2.0/24       → TEST-NET-1
198.51.100.0/24    → TEST-NET-2
203.0.113.0/24     → TEST-NET-3
```

Use in examples/documentation, not as real public Internet targets.

## Multicast

```text
224.0.0.0/4
```

## Limited broadcast

```text
255.255.255.255/32
```

### Security mental model

```text
127.0.0.1          → same host
169.254.x.x        → local link
10/8               → private/internal
172.16/12          → private/internal
192.168/16         → private/internal
224/4              → multicast group
255.255.255.255    → local broadcast
```

---

# 15. IPv6 Special-Use / Important Ranges

## Unspecified

```text
::/128
```

The unspecified address.

Conceptually:

> "I do not currently have a usable source address / I am not specifying one."

It is used as a source in specific protocol/configuration situations and is not a normal destination for a host.

## Loopback

```text
::1/128
```

IPv6 equivalent of the IPv4 loopback concept:

```text
127.0.0.1 ↔ ::1
```

## IPv4-mapped

```text
::ffff:0:0/96
```

Example:

```text
::ffff:10.0.0.1
```

This represents an IPv4 address in an IPv6-style API/address representation.

**Important:** this is not the same as tunneling and is not an IPv6 packet carrying an IPv4 packet.

## Documentation

```text
2001:db8::/32
```

Use for IPv6 documentation/examples.

## Unique Local

```text
fc00::/7
```

Unique Local IPv6 unicast space.

Practical commonly seen locally assigned addresses are in the `fd00::/8` portion.

Think:

```text
IPv4 private        → 10/8, 172.16/12, 192.168/16
IPv6 unique-local   → fc00::/7
```

ULA is intended for local/inter-site private use and is not meant to be globally reachable across the public Internet.

## Link-local

```text
fe80::/10
```

Used on a single local link.

A link-local address is not normally routed across routers.

## Multicast

```text
ff00::/8
```

IPv6 multicast space.

IPv6 has **no broadcast address type**. Where IPv4 might use broadcast, IPv6 uses multicast mechanisms instead.

---

# 16. IPv4-mapped vs IPv4-compatible vs IPv4-embedded vs Tunneling

This is one of the easiest places to get confused.

## IPv4-mapped IPv6

```text
::ffff:10.0.0.1
```

Purpose:

```text
Represent an IPv4 endpoint using an IPv6 address representation.
```

Think:

```text
mapping / API representation
```

not a tunnel.

## IPv4-compatible IPv6 (historical)

Example style:

```text
::1.2.240.1
```

The IPv4 address occupies the low 32 bits in a way that allowed a special IPv6 notation/transition mechanism.

This mechanism is deprecated and should not be used for modern designs.

## IPv4-embedded IPv6

Used by IPv4/IPv6 translation mechanisms.

Well-known prefix:

```text
64:ff9b::/96
```

For a `/96` prefix, the remaining 32 bits can carry the IPv4 address.

Example:

```text
IPv4 = 192.0.2.33

hex bytes:
C0 00 02 21

IPv4-embedded IPv6:
64:ff9b::c000:221
```

Mental model:

```text
IPv6 packet
   ↓
Translator
   ↓
IPv4 packet
```

This is **translation**, not just a display mapping.

## Tunneling

Example class of mechanism: ISATAP / other IPv6-over-IPv4 transition technologies.

```text
IPv6 packet
   ↓ encapsulation
IPv4 packet
   ↓
IPv4-only infrastructure
   ↓
remove outer IPv4 wrapper
   ↓
Original IPv6 packet
```

### VPN analogy

The *tunneling* idea is similar to a VPN, but a tunnel does not automatically mean encryption.

```text
Tunnel = encapsulation / transport method
VPN    = a use case that may add encryption + authentication + tunneling
```

---

# 17. IPv4-Embedded IPv6 Addressing Details

RFC 6052 defines algorithmic IPv4-embedded IPv6 formats.

Allowed prefix lengths in the scheme include:

```text
32, 40, 48, 56, 64, 96 bits
```

The general idea is:

```text
IPv6 prefix + embedded IPv4 address + required zero/suffix bits
```

For the common well-known `/96` case:

```text
96-bit prefix + 32-bit IPv4 = 128 bits
```

This makes extraction straightforward.

---

# 18. IPv6 Interface Identifier (IID)

Many IPv6 unicast addresses are conceptually split into:

```text
[ Network Prefix | Interface Identifier ]
       64 bits            64 bits
```

The IID is used to identify the interface within the prefix in common IPv6 address formats.

### Important

Do not blindly assume every IPv6 address always has exactly a 64/64 split. The standard architecture has exceptions for some address forms. The 64-bit IID model is the common pattern for link-local and many global unicast addresses.

---

# 19. EUI-48 → EUI-64 → Modified EUI-64

Example EUI-48 / MAC-style address:

```text
00-11-22-33-44-55
```

## Step 1: identify OUI

First 24 bits / first 3 bytes:

```text
00-11-22
```

## Step 2: insert FFFE

```text
00-11-22-FF-FE-33-44-55
```

This gives the EUI-64 form.

## Step 3: invert the u bit

The first byte is:

```text
00 = 00000000
```

The `u` bit is bit 1 (the second-lowest bit).

Invert it:

```text
00000000
       ↑ u = 0

00000010
       ↑ u = 1
```

So:

```text
00-11-22-FF-FE-33-44-55
```

becomes modified EUI-64:

```text
02-11-22-FF-FE-33-44-55
```

### Result as IPv6 IID

```text
0211:22ff:fe33:4455
```

### Example full IPv6 address

```text
Prefix:
2001:db8:1234:5678

IID:
0211:22ff:fe33:4455

Full address:
2001:db8:1234:5678:0211:22ff:fe33:4455
```

### u and g bits

In the first byte of an EUI-48/EUI-64 style identifier:

```text
bit 1 → u bit
bit 0 → g bit
```

- `u=0`: universally administered
- `u=1`: locally administered
- `g=0`: individual/unicast-style address
- `g=1`: group/multicast-style address

IPv6 modified EUI-64 **inverts the u bit** when deriving the IID.

---

# 20. Privacy IIDs

Using a MAC-derived IID can create address stability/tracking concerns.

Modern systems can instead use randomized/privacy-oriented interface identifiers.

Conceptually:

```text
MAC-based IID
      vs.
privacy/randomized IID
```

For security and modern IPv6 behavior, do not assume:

```text
IPv6 IID == MAC-derived permanent identifier
```

---

# 21. ISATAP Example From the Book

ISATAP is a transition/tunneling mechanism for carrying IPv6 traffic across IPv4 infrastructure.

Example embedded IPv4 address:

```text
0A-99-8D-87
```

Convert each byte from hex to decimal:

```text
0A = 10
99 = 153
8D = 141
87 = 135
```

Therefore:

```text
0A-99-8D-87 = 10.153.141.135
```

An ISATAP-style identifier includes the IPv4 address with a characteristic form involving:

```text
5EFE
```

leading to an address such as:

```text
fe80::5efe:10.153.141.135
```

Here:

```text
fe80::/10                 → IPv6 link-local prefix
5efe:10.153.141.135       → ISATAP-related IID content
```

### `%2`

On Windows:

```text
fe80::5efe:10.153.141.135%2
```

The `%2` is a **zone ID / interface index**, not part of the 128-bit IPv6 address.

It tells the OS which interface/zone the link-local destination belongs to.

Mental model:

```text
IPv6 address = fe80::...
Zone ID      = %2
```

---

# 22. IPv6 Multicast

IPv6 uses:

```text
ff00::/8
```

for multicast.

IPv6 multicast is much more deeply integrated into normal operation than IPv4 broadcast.

## General format

```text
+----------+--------+--------+----------------------+
|   11111111| Flags  | Scope  |      Group ID        |
|   8 bits | 4 bits | 4 bits |      112 bits        |
+----------+--------+--------+----------------------+
```

So think:

```text
ff | flags | scope | group ID
```

## Group ID

There are 112 bits available in the basic format for the multicast group ID.

The huge group space is one reason IPv6 can use multicast so aggressively.

---

# 23. IPv6 Multicast Scope Values

The 4-bit Scope field controls the intended distribution scope.

Important values:

```text
1 → interface/machine-local
2 → link/subnet-local
4 → administrative
5 → site-local
8 → organization-local
e → global
```

Reserved/unassigned values from the table should not be memorized as normal operational scopes.

### Example

```text
ff02::1
```

Breakdown:

```text
ff   → multicast
02   → link-local scope
::1  → All Nodes group ID
```

Meaning:

> all IPv6 nodes on the local link.

Another classic address:

```text
ff02::2
```

= all IPv6 routers on the local link.

---

# 24. Variable-Scope Multicast

Some permanent multicast group numbers are meaningful at different scopes.

Example pattern from the book:

```text
ff0x::101
```

where `x` is the chosen scope.

For NTP:

```text
ff01::101  → NTP servers on same machine
ff02::101  → NTP servers on same link
ff04::101  → administrative scope
ff05::101  → same site
ff08::101  → same organization
ff0e::101  → global scope
```

The key idea is not the number `101` itself; it is:

```text
same group ID + different scope
```

---

# 25. IPv6 Multicast Flags

Four flag bits exist in the base structure; three operational concepts are especially important:

## T flag — Transient

```text
T=0 → permanent/well-known
T=1 → transient/temporary/dynamically allocated
```

## P flag — Prefix-based

```text
P=0 → regular format
P=1 → multicast address incorporates a unicast prefix
```

When `P=1`, the format changes.

## R flag — Rendezvous Point

```text
R=0 → ordinary
R=1 → RP information is encoded/associated with the multicast address format
```

R is related to multicast routing protocols such as PIM-SM.

---

# 26. IPv4 Multicast

IPv4 multicast space:

```text
224.0.0.0/4
```

First 4 bits identify the multicast space; the remaining 28 bits provide the group-address space.

```text
2^28 = 268,435,456
```

Remember:

```text
224.x.x.x = group address
```

not "a special host IP."

---

# 27. Important IPv4 Multicast Blocks

```text
224.0.0.0/24     → Local Network Control
224.0.1.0/24     → Internetwork Control
232.0.0.0/8      → SSM
233.0.0.0/8      → GLOP-related allocation space
239.0.0.0/8      → Administrative scope
```

## Local Network Control

```text
224.0.0.0/24
```

Traffic is not forwarded by multicast routers beyond the local link.

Example:

```text
224.0.0.1 → All Hosts
```

## Internetwork Control

```text
224.0.1.0/24
```

Intended for control traffic that may be routed beyond a single local link.

Example:

```text
224.0.1.1 → NTP multicast group
```

## SSM

```text
232.0.0.0/8
```

Source-Specific Multicast.

## Administrative scope

```text
239.0.0.0/8
```

Used for controlled/local administrative domains.

Conceptually similar to private-use space in the sense that different organizations can reuse such multicast addresses inside their own controlled domains.

---

# 28. ASM vs SSM

## ASM — Any-Source Multicast

Receiver joins based on the multicast group address:

```text
Join(G)
```

Multiple senders can send to the same group.

```text
Source A ─┐
Source B ─┼→ Group G → Receivers
Source C ─┘
```

## SSM — Source-Specific Multicast

A channel is identified by:

```text
(S, G)
```

where:

```text
S = source IP
G = multicast group address
```

Example:

```text
Source = 10.0.0.5
Group  = 232.1.1.1
```

Receiver requests that particular source/group channel.

Mental model:

```text
ASM → "give me the group"
SSM → "give me this source's traffic for this group"
```

---

# 29. GLOP

GLOP is a multicast address allocation technique historically associated with a 16-bit AS number.

Conceptually:

```text
233 + AS-derived middle bytes + group space
```

The idea is to derive multicast space algorithmically from an Autonomous System number.

The reason this matters:

```text
AS number
   ↓
multicast allocation
   ↓
less manual global coordination
```

### Do not overfocus on the historical details

Modern AS numbers are 32-bit, so the old 16-bit GLOP model has limitations. Treat GLOP primarily as a historical example of **algorithmic address allocation**.

---

# 30. IPv4 UBM — Unicast-Prefix-Based Multicast

Another allocation idea is to base multicast addresses on an existing unicast prefix.

Range:

```text
234.0.0.0/8
```

Basic idea:

```text
234/8 + unicast prefix + group ID
```

Example from the book:

```text
Unicast prefix:
192.0.2.0/24

Associated UBM-style multicast address:
234.192.0.2
```

The advantage is that existing unicast allocation information can help determine who controls the corresponding multicast space.

---

# 31. IPv6 Unicast-Prefix-Based Multicast

IPv6 has a related multicast allocation concept.

When the `P` flag is set, the multicast address format can carry:

```text
unicast prefix
prefix length
group ID
```

The purpose is to leverage existing globally allocated unicast prefixes instead of requiring a completely independent global multicast allocation process for each group.

Mental model:

```text
Existing unicast prefix
        ↓
derive multicast space
        ↓
unique multicast addresses
```

---

# 32. IPv6 Link-Scoped Multicast Based on IID

For local/link scope, an IPv6 multicast address can be constructed using an IID.

The structure in the book is conceptually:

```text
ff3x:0011:<IID>:<32-bit group ID>
```

where the scope is small enough for link/node-local use.

The key advantage:

```text
No global prefix allocation agreement needed.
```

A node can generate unique local multicast addresses from its own IID.

This is not the method to use for global multicast allocation.

---

# 33. Rendezvous Point (RP)

In some multicast routing architectures, a **Rendezvous Point** is a router used to help multicast senders and receivers discover/coordinate with each other.

Mental model:

```text
Sender
   ↕
  RP
   ↕
Receivers
```

The book discusses encoding RP-related information in an IPv6 multicast address when the relevant flags/format are used.

You do not need to memorize the bit-by-bit extraction procedure for early networking/pentesting study.

The important idea is:

```text
Multicast routing may need a rendezvous point.
```

---

# 34. Important IPv6 Multicast Addresses

The following are useful to recognize immediately:

```text
ff01::1              → All Nodes, node-local
ff01::2              → All Routers, node-local
ff02::1              → All Nodes, link-local
ff02::2              → All Routers, link-local
ff02::5              → OSPF routers
ff02::6              → OSPF designated routers
ff02::9              → RIPng routers
ff02::a              → EIGRP routers
ff02::d              → PIM routers
ff02::1:2            → DHCP agents
ff02::1:3            → LLMNR
ff02::1:ffxx:xxxx    → solicited-node multicast range
```

The **solicited-node** multicast range is especially important in IPv6 Neighbor Discovery.

---

# 35. Unicast Address Allocation Hierarchy

IP addresses are allocated hierarchically.

```text
IANA
  ↓
RIR
  ↓
ISP / registry
  ↓
Customer/site
  ↓
Subnet
  ↓
Interface
```

## RIRs from the book

```text
AFRINIC → Africa
APNIC   → Asia/Pacific
ARIN    → North America
LACNIC  → Latin America + Caribbean
RIPE NCC→ Europe + Middle East + Central Asia
```

Egypt/Middle East falls under the RIPE NCC service region.

---

# 36. PA — Provider Aggregatable

Customer receives a prefix out of the ISP's own allocation.

Example:

```text
ISP
200.10.0.0/16
   │
   ├── Customer A → 200.10.1.0/24
   ├── Customer B → 200.10.2.0/24
   └── Customer C → 200.10.3.0/24
```

The ISP can advertise an aggregate such as:

```text
200.10.0.0/16
```

to the rest of the Internet where appropriate.

PA is sometimes called **non-portable** because the address space belongs to/comes from the provider's allocation structure.

If the customer changes ISP, renumbering may be required.

---

# 37. PI — Provider Independent

PI space is assigned to the customer/organization independently of a specific ISP.

```text
           Company
          /       \
       ISP A     ISP B
          \       /
           PI prefix
```

Main benefit:

```text
Change ISP
   ↓
keep the PI prefix
   ↓
avoid renumbering
```

Main cost/scalability issue:

```text
PI prefix is not part of each ISP's naturally aggregatable address block
   ↓
more specific route must often be advertised separately
   ↓
larger routing tables
```

This is why PI is valuable to some organizations but harder for global routing scalability.

---

# 38. Multihoming

**Multihoming** = an organization has connections to more than one ISP.

Goals may include:

- redundancy/failure tolerance
- traffic engineering
- more provider independence

Example:

```text
          Internet
         /        \
       ISP1      ISP2
         \        /
          Company
```

---

# 39. PA Multihoming Problem From the Book

Suppose site S has PA space from ISP P1:

```text
12.46.129.0/25
```

P1 owns a larger block:

```text
12.0.0.0/8
```

P2 owns:

```text
137.164.0.0/16
```

P1 can aggregate:

```text
12.46.129.0/25
```

into:

```text
12.0.0.0/8
```

P2 cannot aggregate that prefix into `137.164/16` because they are not numerically adjacent.

If P1 and P2 both advertise the site's prefix, a remote router may prefer the more-specific `/25` route over `/8` due to longest-prefix matching.

This can produce an asymmetric/unintuitive traffic pattern.

---

# 40. PI Multihoming

With PI space:

```text
198.134.135.0/24
```

both ISPs can advertise the same prefix:

```text
ISP1 → 198.134.135.0/24
ISP2 → 198.134.135.0/24
```

Neither can naturally aggregate it into the other ISP's unrelated address block.

But routing can then choose between the two providers according to the routing system's path calculations, and the organization keeps the same prefix if it changes provider.

Trade-off:

```text
PI → portability / multihoming flexibility
PI → less aggregation / more routing state
```

---

# 41. Identifier vs Locator

This is one of the deeper concepts in the chapter.

Historically, an IP address plays two roles:

```text
1. Identifier: "which endpoint/interface?"
2. Locator:    "where is it in the routing topology?"
```

The problem is that if the IP changes, both the identity and the routing locator appear to change.

This motivates **identifier/locator separation** ideas.

---

# 42. Shim6 / ID-Locator Split (IPv6 Multihoming Concept)

Shim6 introduces a network-layer shim intended to separate the upper-layer protocol identity from the IP locator.

Conceptual model:

```text
Stable upper-layer identity
          ↓
      Shim6
          ↓
Current IP locator
```

Then a multihomed host can change which locator is used without changing the higher-level peer relationship in the same way that a pure IP-address identity model would.

Related historical work includes HIP (Host Identity Protocol), where cryptographic host identifiers can be used as host identities.

For current pentesting basics, remember the **identity vs locator distinction** more than the exact Shim6 bit/protocol format.

---

# 43. Single Host / Single Provider / Single Address

Simplest Internet setup:

```text
Single computer
     │
   ISP
     │
 one IPv4 address
```

A DSL/PPP-like connection may get one address that can change over time.

The host still has other active addresses, such as:

```text
127.0.0.1
```

and multicast memberships.

If IPv6 is enabled, it may also have:

```text
::1
fe80::/10 address on each IPv6-capable interface
IPv6 multicast memberships
other assigned IPv6 addresses
```

### Important mental correction

A host having one public IPv4 address does **not** mean it only has one IP address total.

---

# 44. Single Provider / Single Network / Single Address — Home NAT

Typical home architecture:

```text
                 ISP
                  │
             Public IPv4
                  │
               Router
              /  |  \
            PC  Phone Laptop
         192.168.1.x private LAN
```

The router:

- forwards packets toward the ISP
- performs NAT
- commonly provides DHCP to internal clients

From the ISP's point of view, one public address can represent many internal hosts.

---

# 45. NAT

NAT rewrites addresses as traffic crosses the boundary.

Typical pattern:

```text
192.168.1.10
      ↓
    NAT
      ↓
Public IPv4
      ↓
Internet
```

The important security idea is:

```text
Private IP != publicly reachable Internet endpoint
```

But do not think of NAT itself as a complete security control; firewall policy/stateful filtering and exposure configuration matter too.

---

# 46. Enterprise Network + DMZ

Small/medium enterprise example from the chapter:

```text
                     Internet
                        │
                Border Router/Firewall
                   /              \
                DMZ          Internal NAT Router
                 │                    │
            Public servers        Internal LAN
              /  |  \              10.x.x.x
            Web Mail DNS
```

The site receives a public block such as:

```text
128.32.2.64/26
```

A `/26` contains:

```text
2^(32-26) = 64 total addresses
```

Historically, the book describes the usable host count as:

```text
64 - 2 = 62
```

because the all-zero network and all-one broadcast were excluded under traditional subnet rules.

### DMZ purpose

Put Internet-facing services in a separate zone so that compromising one of those hosts does not automatically expose the internal network directly.

```text
Internet
   ↓
 DMZ
   ↓
Firewall/segmentation
   ↓
Internal network
```

This is extremely relevant to security testing.

---

# 47. Security Relevance of IP Addressing

## IP address ≠ person

An IP can be:

- temporarily assigned
- reassigned later
- shared by many devices through NAT
- used from a public/shared network
- associated with a compromised machine

Therefore:

```text
IP address alone ≠ reliable proof of human identity
```

Time matters too.

A mapping such as:

```text
Public IP + exact timestamp
        ↓
ISP allocation records
```

is much more meaningful than an IP address with no timing context.

---

# 48. IP Spoofing

A sender can sometimes put a forged source IP into a packet:

```text
Attacker
Source IP field = fake address
        ↓
      Victim
```

Therefore:

```text
Source IP shown in a packet
        ≠
necessarily the true originator
```

But the impact depends heavily on the protocol and network behavior.

For example, protocols/attacks requiring two-way conversation and correct return traffic behave differently from one-way/connectionless traffic.

---

# 49. Shared/Open Networks and Compromised Hosts

A visible public IP can belong to:

```text
home router
public Wi-Fi
office network
NAT gateway
compromised host
```

Example:

```text
Attacker
   ↓
Open Wi-Fi / compromised machine
   ↓
Public IP
   ↓
Internet
```

So attribution requires more evidence than merely finding the IP address.

---

# 50. Security Cheat Sheet: Why Special IPs Matter

When doing reconnaissance, packet analysis, or pentesting, immediately classify an address.

```text
127.0.0.1
→ loopback / same machine

10.x.x.x
172.16–31.x.x
192.168.x.x
→ private/internal address space

169.254.x.x
→ IPv4 link-local

224.x.x.x
→ IPv4 multicast group

255.255.255.255
→ local broadcast

fe80::/10
→ IPv6 link-local

fc00::/7
→ IPv6 Unique Local

ff00::/8
→ IPv6 multicast

::1
→ IPv6 loopback

::
→ IPv6 unspecified

2001:db8::/32
→ documentation/example IPv6
```

### Security mindset

When you see an IP, ask:

```text
1. Public or private?
2. Unicast, multicast, broadcast, or anycast usage?
3. Routable where?
4. What is its scope?
5. Is it a real host endpoint or a special-use address?
6. Could NAT/proxy/VPN/anycast hide the actual origin?
```

---

# 51. Addressing as an Attack-Surface Clue

If enumeration shows:

```text
10.10.10.0/24
10.10.20.0/24
10.10.30.0/24
```

do not see three random ranges only.

They may represent:

```text
Users
Servers
Management
```

or other security zones.

The actual meaning must be verified from traffic, DNS, routing, hostnames, ACLs, etc.

### Key security principle

```text
Addressing + subnetting + routing
        ↓
reveals clues about network topology and segmentation
```

---

# 52. Multicast Security-Relevant Mental Model

When you see:

```text
224.x.x.x
ffxx:....
```

do not treat it as a normal single-host target by default.

Ask:

```text
Which group?
Which scope?
Who can join?
What protocol uses it?
Is it local-link only?
Does a router forward it?
```

Examples:

```text
224.0.0.1 → IPv4 all-hosts group
ff02::1   → IPv6 all-nodes group on the link
```

These are group destinations, not single machines.

---

# 53. Fast Comparison Table

| Concept | Mental model |
|---|---|
| IPv4 | 32-bit address space |
| IPv6 | 128-bit address space |
| Prefix | leading bits identifying a network/route block |
| Host bits | remaining bits used for host/interface addressing inside a prefix |
| Unicast | one destination/interface |
| Broadcast | all nodes in a broadcast domain (IPv4) |
| Multicast | a group of receivers |
| Anycast | one destination chosen from multiple instances |
| NAT | address rewriting at a boundary |
| PA | provider-owned/provider-aggregatable address space |
| PI | provider-independent address space |
| CIDR | classless prefix-based addressing/routing |
| Aggregation | combine contiguous prefixes into a larger route |
| Longest Prefix Match | more-specific matching route wins |
| IID | common IPv6 interface identifier field |
| EUI-64 | 64-bit identifier format |
| ISATAP | IPv6-over-IPv4 transition/tunneling mechanism |
| IPv4-mapped IPv6 | IPv4 represented in an IPv6 address form |
| IPv4-embedded IPv6 | IPv4 encoded inside an IPv6 address for translation |
| Link-local IPv6 | `fe80::/10`, local-link scope |
| ULA | `fc00::/7`, private/local IPv6 unicast |
| IPv6 multicast | `ff00::/8` |

---

# 54. Things I Must NOT Confuse

## 1. TCP vs IP

```text
TCP → transport between application endpoints
IP  → addressing/routing
```

## 2. IP address vs packet

```text
IP address → identifier/locator
IP packet   → actual transmitted data unit
```

## 3. MAC broadcast vs IP broadcast

```text
Ethernet broadcast → FF:FF:FF:FF:FF:FF
IPv4 limited broadcast → 255.255.255.255
```

Different layers.

## 4. IPv4-mapped vs IPv4-embedded vs tunnel

```text
::ffff:IPv4
→ mapping/representation

64:ff9b::IPv4
→ translation address format

IPv6 inside IPv4
→ tunneling/encapsulation
```

## 5. Multicast vs broadcast

```text
Broadcast → everyone in the relevant broadcast domain
Multicast → only group members
```

## 6. Anycast vs multicast

```text
Anycast   → one instance from many
Multicast → many group members
```

## 7. PA vs PI

```text
PA → tied to provider allocation; aggregation-friendly
PI → independent of provider; portable, less aggregatable
```

## 8. Scope vs routing

```text
Scope tells you how far an address/traffic is intended to apply.
Routing determines how packets are forwarded through the network.
```

---

# 55. Review Examples

## Example A — subnet broadcast

```text
192.168.10.0/24
```

Host bits = 8.

Broadcast:

```text
192.168.10.255
```

## Example B — host count by prefix

```text
192.168.10.0/26
```

Host bits:

```text
32 - 26 = 6
```

Total addresses:

```text
2^6 = 64
```

Traditional usable hosts:

```text
64 - 2 = 62
```

## Example C — classify an address

```text
192.168.1.20
```

Answer:

```text
IPv4 private-use unicast address
```

## Example D — IPv6 multicast

```text
ff02::1
```

Answer:

```text
Multicast
Scope = 2 → link-local
Group = All Nodes
```

## Example E — mapping

```text
::ffff:192.168.1.10
```

Answer:

```text
IPv4-mapped IPv6 representation
IPv4 = 192.168.1.10
```

## Example F — translation

```text
64:ff9b::c000:221
```

Decode final 32 bits:

```text
C0 00 02 21
192.0.2.33
```

So the embedded IPv4 address is:

```text
192.0.2.33
```

## Example G — longest prefix match

Routes:

```text
10.0.0.0/8      → A
10.20.0.0/16   → B
```

Destination:

```text
10.20.5.10
```

Choose:

```text
10.20.0.0/16 → B
```

because `/16` is more specific than `/8`.

---

# 56. What Matters Most for My Cybersecurity Path

Priority order for practical security/networking understanding:

## Tier 1 — Must be automatic

```text
IPv4 vs IPv6
Prefix notation (/24, /26, /64, ...)
Private vs public
Loopback
Link-local
Broadcast
Multicast
Unicast
Anycast
NAT
Longest Prefix Match
CIDR aggregation
Subnet/network/host distinction
MAC vs IP
Layer-2 broadcast
```

## Tier 2 — Very useful

```text
PA vs PI
Multihoming
DMZ
Routing hierarchy
IPv6 scopes
IPv6 IID
EUI-64 / modified EUI-64
IPv4-mapped vs IPv4-embedded
Tunneling vs translation
```

## Tier 3 — Mostly historical / advanced for now

```text
Classful A/B/C history
GLOP details
6to4
Teredo
ISATAP internals
RP bit extraction
Shim6 internals
old IPv4-compatible addresses
```

Know what they mean, but don't spend the same memorization effort on them as subnetting/routing/NAT/address classification.

---

# 57. Final 30-Second Revision

```text
IPv4 = 32 bits
IPv6 = 128 bits

CIDR = prefix length
Aggregation = combine adjacent prefixes
Longest Prefix Match = most specific route wins

Unicast   = one
Broadcast = everyone in broadcast domain
Multicast = group
Anycast   = one of many instances

IPv4 private:
10/8
172.16/12
192.168/16

IPv4 loopback:
127/8

IPv4 link-local:
169.254/16

IPv4 multicast:
224/4

IPv4 limited broadcast:
255.255.255.255

IPv6 loopback:
::1

IPv6 unspecified:
::

IPv6 ULA:
fc00::/7

IPv6 link-local:
fe80::/10

IPv6 multicast:
ff00::/8

IPv6 documentation:
2001:db8::/32

IPv4-mapped:
::ffff:IPv4

IPv4/IPv6 translation prefix:
64:ff9b::/96

MAC broadcast:
FF:FF:FF:FF:FF:FF
```

---

# 58. References Worth Keeping

- RFC 1122 — IPv4 host requirements / special-use behavior
- RFC 1918 — IPv4 private address space
- RFC 4291 — IPv6 Addressing Architecture
- RFC 4193 — Unique Local IPv6 Unicast Addresses
- RFC 6052 — IPv6 Addressing of IPv4/IPv6 Translators
- RFC 5771 — IPv4 Multicast Address Assignments
- RFC 3569 / RFC 4607 — Source-Specific Multicast
- RFC 4489 — Link-Scoped IPv6 Multicast Addresses
- RFC 3306 — Unicast-Prefix-Based IPv6 Multicast Addresses
- RFC 4786 — Operation of Anycast Services
- RFC 5156 — IPv6 Special-Use Addresses (historical table used by the book)

> **Note:** Chapter 2 of the book was written around 2011, so some allocations and transition technologies have since changed status. For current operations, always verify special-use ranges against the current IANA registries.
