# Chapter 2 - Networking Foundation

# Communication Model

**Protocol** = a set of rules that dictates how communication happens.

## OSI Model (Open Systems Interconnection)

Defined late 70s by the **ISO** to standardise communication and get better interoperability between vendors.

**7 layers (top to bottom):**

| # | Layer | Role | Examples |
| --- | --- | --- | --- |
| 7 | Application | Handles the communication needs of applications | HTTP, HTTPS, DNS, SMTP |
| 6 | Presentation | Prepares/formats data for the application layer | ASCII, Unicode, EBCDIC, JPEG |
| 5 | Session | Sets up and maintains the communication between apps | RPC, NetBIOS |
| 4 | Transport | Segments messages, multiplexes communication, uses **ports** so the receiver knows which app to hand traffic to | TCP, UDP |
| 3 | Network | Gets messages from one endpoint to another — addressing and routing | IP, ICMP |
| 2 | Data Link | Identifies the interface on the local network via **MAC address** | Ethernet, ARP, Frame Relay, VLANs |
| 1 | Physical | Dictates how the pulses on the wire are handled | 10BaseT, 10Base2, 100BaseTX, 1000BaseT |

**Mnemonic:** *Please Do Not Touch Steven's Pet Alligator* (Physical → Application)

**Key points:**

- Messages start being created at the Application layer and go **down** the stack on send, **up** on receive.
- Each layer adds its own header (encapsulation).
- Limit: protocols don't always map cleanly to a layer — the session/presentation/application boundary is especially fuzzy.

## TCP/IP Model — 4 layers

| # | Layer | Maps to OSI |
| --- | --- | --- |
| 4 | Application | 7 + 6 + 5 |
| 3 | Transport | 4 |
| 2 | Internet | 3 |
| 1 | Link | 2 + 1 |

## OSI vs TCP/IP

- A protocol generally doesn't sprawl across several layers — it's designed to fill one specific function.
- TCP/IP is what's actually implemented; OSI is the reference model.
- In the real world (and on the exam), when someone says "layer 3" or "layer 7", they mean the **OSI** model.

# Topologies

**Why it matters:**

- A physical map of the network shows how everything is connected → helps isolate issues.
- It shows how traffic flows and which intermediary devices it passes through → helps determine **where traffic can be intercepted or manipulated**.

## Bus

- One single cable (coaxial with T-connectors); every device taps into it.
- Needs **terminators** at both ends to keep the signal on the wire.
- Problem: lots of **collisions** — messages run over each other.

## Star

- Same idea, but with a central device between all systems.
- **Hub** → repeats the signal to every port = same collision problem as a bus.
- **Switch** → sends the signal only to the correct port = no collisions.

## Ring

- Traffic passes around the ring from system to system.
- **Token Ring**: wired through a **MAU** (Multistation Access Unit).
- Collisions avoided with a **token** — a talking stick: only the holder can transmit.
- Problem: the token can get lost → a new one is generated → the old one reappears → two tokens on the network.
- No terminators needed.

## Mesh

- Systems wired directly to one another (peer to peer).
- **Full mesh** = every system connected to every other system.
- If two systems aren't directly connected, traffic passes through another device.
- Advantage: **redundancy** — if one system/link fails, another path exists.
- Problem: complexity and cost.
- Number of connections: **n(n−1)/2**

## Hybrid

- Most common: **star-bus** — switches are star topologies, linked together in a bus.
- Switching infrastructure can also be linked in a **mesh or ring** for redundancy and multiple paths.

# Physical Networking

To connect to a network you need a physical layer — in practice, **Ethernet**.

## Protocol Data Units (PDU)

Each layer of the stack has its own name for the chunk of data it encapsulates:

| Layer | PDU |
| --- | --- |
| 7–5 | Data |
| 4 | **Segment** (TCP) / **Datagram** (UDP) |
| 3 | **Packet** |
| 2 | **Frame** |
| 1 | **Bits** |

## Addressing — MAC

- **MAC address** = unique physical identifier burned into the network card (hard-coded in hardware). Also called hardware address or physical address.
- Format: 6 octets (8-bit bytes), usually separated by colons → `BA:00:4C:78:57:00`
- Split in two halves:
    - **OUI** (Organizationally Unique Identifier) = first 3 octets = the vendor that manufactured the interface
    - Last 3 octets = unique identifier for that card within the vendor's range
- Used **only on the local network**: anything sending on the LAN sends to a MAC address.
- **Broadcast MAC** = `ff:ff:ff:ff:ff:ff` → delivered to every device on the segment.

## Switching

**Switching** = making forwarding decisions based on the physical (MAC) address — a layer 2 job.

- **Bridge** = device that connects two network segments together, forwarding traffic based on MAC address.
- **Switch** = essentially a **multiport bridge** — one collision domain per port instead of one for the whole network.

**How a switch learns:**

1. It starts empty and floods any frame out of all ports (like a hub).
2. When a frame arrives, it reads the **source MAC** and records "this MAC is on this port" in its **CAM table**.
3. Next time a frame is destined for that MAC, it forwards it **only** out of that one port (unicast).
4. Unknown destination MAC, broadcast, or multicast → flooded to all ports.
5. Entries age out after a timeout (typically ~5 min) so devices can move.

**CAM (Content-Addressable Memory)** = the special memory holding that MAC-to-port table. Normal RAM is queried by address; CAM is queried by **content** — you feed it a MAC and it returns the port instantly. That's what lets a switch forward at wire speed. Its size is limited, which is exactly what attackers abuse.

**Consequences for an attacker:**

- A switch means you no longer see everyone else's traffic by default — sniffing is much harder than on a hub or bus.
- **Port mirroring / SPAN port**: legitimate feature that copies traffic from one port (or VLAN) to another for monitoring. If you can configure it, you get a copy of the traffic.
- **MAC flooding** (e.g. macof): flood the switch with thousands of fake source MACs to fill the CAM table. Once full, the switch can't learn any more and **fails open**, flooding frames out of every port like a hub → you can sniff again.
- **ARP spoofing/poisoning**: doesn't attack the switch itself but poisons the hosts' ARP caches so traffic is sent to your MAC → man-in-the-middle. This is the usual method on a modern switch.
- **MAC spoofing**: change your own MAC to impersonate another device or bypass port-based filtering.

# IP

## Encapsulation

As a message goes down the stack, each layer adds its own **header**. Each protocol attaches its own set of fields. Message + headers = a new **PDU**. At layer 3 (IP), the PDU is called a **packet**.

Two versions in use: **IPv4** and **IPv6**.

## Who defines this

- **IETF** maintains all documentation related to protocols.
- To propose a new protocol or an extension, you write an **RFC** (Request For Comments).
- The RFC for IPv4 (RFC 791) defines how it works and which header fields exist.

## IPv4 Header fields

| Field | Role |
| --- | --- |
| **Version** | Which IP version is in the packet (4) |
| **Header Length** (IHL) | How many 32-bit words in the header (min 5 = 20 bytes) |
| **Type of Service** (ToS/DSCP) | QoS decisions — prioritise or deprioritise some messages |
| **Total Length** | Total length of headers + data (before layer 2) |
| **Identification** | If a message is too long → fragmentation; all fragments share the same ID |
| **Flags** | 3 bits: 1 reserved, **DF** (Don't Fragment) — set = don't fragment, **MF** (More Fragments) — set = more fragments coming, 0 = last fragment |
| **Fragment Offset** | 13 bits — where this fragment's data aligns, so fragments can be reassembled in order |
| **Time to Live** (TTL) | Originally in seconds, in practice a **hop count**. Every network device that forwards the packet decrements it. Reaches 0 → packet discarded and an ICMP error returned to the sender |
| **Protocol** | Numeric value saying which protocol comes next (1 = ICMP, 6 = TCP, 17 = UDP) |
| **Checksum** | 16-bit value used to verify the header is intact |
| **Source address** | IPv4 address of the sender — 4 octets |
| **Destination address** | IPv4 address of the receiver — 4 octets |
| **Options / Padding** | Optional, rarely used (e.g. source routing, record route) |

**Notes for attacks:** TTL is what makes **traceroute** work (send packets with TTL 1, 2, 3… and collect the ICMP errors). TTL values also help **OS fingerprinting** (Windows starts at 128, Linux at 64). Fragmentation fields are abused to evade IDS (`nmap -f`).

## Octets vs Bytes

8 bits = 1 octet = 1 byte. "Octet" is used because historically a byte wasn't always 8 bits.

## IPv4 Addressing

- 4 octets separated by a period, each 8 bits → **0–255**. Total = 2³² ≈ 4.3 billion addresses.
- Some ranges are reserved:

| Range | Use |
| --- | --- |
| 127.0.0.0 – 127.255.255.255 | **Loopback** — refers to the host itself. Guarantees there's always a network interface and allows testing over the network without sending traffic outside the system |
| 10.0.0.0 – 10.255.255.255 | **Private** (RFC 1918) |
| 172.16.0.0 – 172.31.255.255 | **Private** |
| 192.168.0.0 – 192.168.255.255 | **Private** |
| 169.254.0.0 – 169.254.255.255 | **APIPA / link-local** — self-assigned when DHCP fails |
| 224.0.0.0 – 239.255.255.255 | **Multicast** |
| 240.0.0.0 and above | Reserved, not in use |
- Private addresses are **not routable over the internet** — they need **NAT** to reach outside.

## IPv6

- Reason for the move: IPv4 address exhaustion. The stopgap was private ranges + NAT.
- **IPv4** = 4 octets (32 bits). **IPv6** = 16 bytes (128 bits) → 8 groups of 4 hex characters, 32 characters max.
- Shorthand rules: drop leading zeros in a group, and replace **one** run of all-zero groups with `::`
    - `fe80:0000:0000:0000:62e3:5ec3:3e06:daa2` → `fe80::62e3:5ec3:3e06:daa2`

**Three address types (no broadcast in IPv6):**

| Type | Meaning |
| --- | --- |
| **Unicast** | One single system — one-to-one |
| **Multicast** | A group of systems sharing one address; the message is delivered to **all** members of the group. Replaces broadcast in IPv6 (`ff00::/8`) |
| **Anycast** | A group of systems sharing one address, but the message is delivered to the **nearest/one** member only. Used for load balancing and services like DNS root servers |

Common prefixes: `::1` = loopback, `fe80::/10` = link-local, `fc00::/7` = unique local (the private equivalent).

## Subnets

- **Subnet** = a logical subdivision of an IP network. Splits a big network into smaller segments (broadcast domains) for organisation, performance and security.
- **Subnet mask** = 32 bits that separate the address into a **network part** (1s) and a **host part** (0s).

Example:

```
11111111.11111111.11111111.10000000
= 255.255.255.128
= /25   (CIDR notation)
```

- A /25 splits the last octet into two ranges: **0–127** and **128–255**. To know which one you're in, look at the address: `127.20.30.42` falls in the first → the subnet is `127.20.30.0` to `127.20.30.127`.

**Sizes:**

| CIDR | Addresses | Usable hosts |
| --- | --- | --- |
| /23 | 512 | 510 |
| /24 | 256 | 254 |
| /25 | 128 | 126 |
| /26 | 64 | 62 |

Rule of thumb: each bit added **halves** the range, each bit removed **doubles** it. Total addresses = 2^(32−prefix).

**In every subnet:**

- **Lowest address** = network address (identifies the subnet, not assignable)
- **Highest address** = broadcast address (not assignable)
- → that's why usable hosts = total − 2

# TCP

**Transmission Control Protocol** — layer 4, **connection-oriented**, provides **guaranteed delivery**: it keeps track of every message sent, and if something goes wrong the message is retransmitted.

The transport layer also provides **ports**, used to address applications and multiplex several conversations over one IP address.

## TCP Header fields

| Field | Size | Role |
| --- | --- | --- |
| **Source port** | 16 bits | Port the traffic originates from on the sending side (usually ephemeral) |
| **Destination port** | 16 bits | Port associated with the target application |
| **Sequence number** | 32 bits | Set to a random value (ISN) when the connection starts, then increments by the number of bytes sent. Core of guaranteed delivery |
| **Acknowledgement number** | 32 bits | The other side of the conversation — the next sequence number expected from the peer |
| **Data offset** | 4 bits | Header length — indicates where the data starts |
| **Reserved** | 6 bits | Reserved for future use |
| **Control bits (flags)** | 6 bits | Indicate the disposition of the message — see below |
| **Window** | 16 bits | How many bytes the sender is willing to accept (flow control). Small window = receiver is struggling / less reliable link |
| **Checksum** | 16 bits | Verifies the communication hasn't been corrupted |
| **Urgent pointer** | 16 bits | Points to the byte after the urgent data — only meaningful if URG is set |
| **Options + Padding** | Variable | Headers must align on 32-bit words → padding bits added when needed (e.g. MSS, window scaling) |

## Flags

| Flag | Meaning |
| --- | --- |
| **SYN** | Synchronize — the sequence number is set and should be recorded (connection setup) |
| **ACK** | The acknowledgement number is set and should be recorded |
| **RST** | Reset the connection — appears on errors, or when a port is closed |
| **PSH** | Push the data straight up to the application instead of buffering it |
| **FIN** | The conversation is over, no more data to send (graceful close) |
| **URG** | The urgent pointer contains significant data |

Mnemonic: **U A P R S F** (Urgent, Ack, Push, Reset, Syn, Fin) — the order they appear in the header.

## Three-way handshake

TCP is connection-oriented — connections are established with a **3-way handshake**, which proves both sides are live and active:

1. **SYN** → client sends SYN flag with its initial sequence number
2. **SYN-ACK** → server sets SYN with its *own* sequence number, and sets ACK with the client's sequence number **+1** (confirming receipt)
3. **ACK** → client sets ACK with the server's sequence number **+1**

Connection established. Each side tracks **its own sequence number** and **the other side's acknowledgement number**.

**Teardown** is a 4-way exchange: FIN → ACK → FIN → ACK (each direction closes independently). RST closes it abruptly instead.

## Why sequence/ack numbers matter

- Sequence number increments with the number of **bytes sent** → the acknowledgement number tells the sender whether any data went missing. If it did, it gets **retransmitted**.
- Together they also ensure the **correct ordering** of messages at the recipient: if a segment arrives out of order, the sequence number tells the receiver to hold it while waiting for the missing one.

## Why it matters for attacks

- The handshake is the basis of **port scanning**: SYN → SYN-ACK = open; SYN → RST = closed; no answer = filtered.
- **SYN scan** (`nmap -sS`, half-open): sends SYN, reads the reply, sends RST instead of completing the handshake — stealthier and faster.
- **SYN flood**: flood of SYNs never completed, filling the server's connection table (DoS).
- ISNs are randomised precisely to prevent **TCP session hijacking** / sequence prediction.

# UDP

**User Datagram Protocol** — layer 4, **connectionless**, much lighter than TCP. No guarantee of delivery: it assumes the application (or nothing at all) will handle the losses.

**No handshake, no sequence numbers, no acknowledgements, no retransmission, no flow control, no ordering.** Send and forget.

## Header — only 4 fields, 8 bytes total

| Field | Size | Role |
| --- | --- | --- |
| **Source port** | 16 bits | Optional (can be 0) — no response is expected |
| **Destination port** | 16 bits | Port of the target application |
| **Length** | 16 bits | Length of header + data |
| **Checksum** | 16 bits | Optional in IPv4, mandatory in IPv6 |

Compare: TCP header = 20 bytes minimum. UDP = 8 bytes → less overhead, faster.

## Where it's used

Applications that need fast setup and transmission, and where losing a packet matters less than waiting for it:

- Streaming video and audio, VoIP, gaming
- DNS, DHCP, SNMP, TFTP, NTP, Syslog

## Why it matters for attacks

- **UDP scanning** (`nmap -sU`) is slow and unreliable: no reply can mean open *or* filtered. Only an **ICMP port unreachable** clearly means closed.
- No handshake = **source address is trivially spoofed** → basis of **amplification/reflection DDoS** (DNS, NTP, SNMP, memcached): small spoofed request, huge reply sent to the victim.
- The PDU is called a **datagram**, not a segment.

# ICMP

**Internet Control Message Protocol** — carries no user data. It works alongside the other protocols to provide **error and control messaging**. When something unexpected happens on the network, devices generate ICMP messages back to the originating device to signal the problem.

Lives at **layer 3**, encapsulated inside an IP packet (**IP protocol number 1**). No ports.

## Header — 8 bytes

| Field | Size | Role |
| --- | --- | --- |
| **Type** | 1 byte | The kind of message being sent |
| **Code** | 1 byte | Sub-reason within that type |
| **Checksum** | 2 bytes | Integrity of the ICMP message |
| **Rest of header** | 4 bytes | Varies by type (identifier + sequence number for echo, next-hop MTU, etc.) |

The payload usually includes the **IP header + first 8 bytes** of the packet that caused the error, so the sender knows which connection failed.

## Types to know

| Type | Meaning |
| --- | --- |
| **0** | Echo Reply |
| **3** | Destination Unreachable (code 1 = host, code 3 = **port unreachable**, code 13 = administratively filtered) |
| **5** | Redirect |
| **8** | Echo Request |
| **11** | Time Exceeded (TTL reached 0) |
| **13 / 14** | Timestamp Request / Reply |

## Tools built on ICMP

- **ping** — echo request (type 8) → echo reply (type 0). Confirms a host is alive and measures round-trip time.
- **traceroute** — maps the route to a destination using two ICMP messages: it sends packets with increasing TTL, each router that drops one returns **type 11 (Time Exceeded)**, and the final host returns **type 3 (Destination Unreachable)**. Linux traceroute uses UDP by default, Windows `tracert` uses ICMP echo.

## Why it matters for attacks

- **Host discovery**: `nmap -sn` (ping sweep). But ICMP is often blocked at the perimeter → no reply doesn't mean the host is down.
- **Type 3 code 3 (port unreachable)** is what makes UDP scanning work — it's the only clear "closed" answer.
- ICMP is also used for **OS fingerprinting** and as a **covert channel** (data hidden in the echo payload → ICMP tunneling, exfiltration).
- Old attacks: Smurf (spoofed broadcast pings), Ping of Death (oversized packet).

# Network Architecture

## Network Types (by geography)

| Type | Definition |
| --- | --- |
| **LAN** (Local Area Network) | All systems are nearby, sharing the same broadcast domain, communicating at **layer 2** (MAC addresses) — unless they're in a separate network segment |
| **VLAN** (Virtual LAN) | Same as a LAN, but the layer 2 isolation is done by **software/firmware** in the switch rather than physically. Crossing from one VLAN to another requires a **layer 3 boundary** (router or L3 switch) |
| **WAN** (Wide Area Network) | Nodes more than ~10 miles apart. Going through a service provider, or linking different office locations → WAN (often over a VPN today) |
| **MAN** (Metropolitan Area Network) | Between a LAN and a WAN — e.g. a company with several buildings across a city or campus |

Also worth knowing: **PAN** (Personal Area Network — Bluetooth, a few metres) and **CAN** (Campus Area Network).

## Isolation

Separating the network to protect sensitive data and to keep externally accessible systems away from strictly internal ones.

- **DMZ** (Demilitarized Zone) — where any system reachable from outside is placed (web, mail, DNS servers). Typical layout: WAN → firewall → one leg to the DMZ, another to the internal servers.
    - Firewalls and **ACLs** prevent outsiders from reaching internal servers. The key rule: the DMZ can be reached from the internet, but the DMZ should **not** be able to initiate connections into the internal network — so a compromised DMZ host doesn't hand over the LAN.
- **Enclaves** — isolated network segments with tight controls, created for a specific type of data or population (payment data, industrial systems, R&D). Increasingly done in **software** rather than with physical separation (microsegmentation, SDN).

## Remote Access

Remote access is handled over the internet.

- **VPN** (Virtual Private Network) — a way to reach the internal network from a remote location, by building an encrypted tunnel across an untrusted network.
    - **Site-to-site**: links two offices permanently (usually IPSec).
    - **Remote access / client**: one user connecting in (usually TLS-based today).
- **MPLS** (Multiprotocol Label Switching) — a carrier technology that forwards traffic using short **labels** instead of doing a full IP routing lookup at each hop. It's not layer 2 or 3 but "layer 2.5". Used by providers to build private WANs between company sites, with traffic separation and guaranteed performance (QoS). Important point: **MPLS is not encrypted** — the isolation is logical, provided by the carrier, so IPSec is often layered on top.

## IPSec vs TLS

**IPSec** — operates at **layer 3**, protects everything above it. Provides:

- Encryption from one location to another (confidentiality)
- User authentication
- Message authentication and integrity
- Key exchange (IKE)

Two protocols: **AH** (authentication and integrity only, no encryption) and **ESP** (encryption + authentication — the one actually used).
Two modes: **transport** (only the payload is encrypted, host-to-host) and **tunnel** (the whole original packet is encrypted and wrapped in a new IP header — used for site-to-site VPNs).

**TLS VPN** (SSL VPN) — operates at the **transport/session layer**. The tunnel is built over TCP 443, which means it looks like normal HTTPS traffic and passes through firewalls, proxies and NAT without special configuration. Often clientless (just a browser) or with a lightweight client. OpenVPN, AnyConnect, GlobalProtect work this way.

|  | IPSec | TLS |
| --- | --- | --- |
| Layer | 3 (network) | 4/5 (transport/session) |
| Typical use | Site-to-site tunnels | Remote user access |
| Client | Dedicated client / router config | Browser or light client |
| Firewall & NAT friendly | Poorly (needs NAT-T, ports 500/4500, ESP) | Yes — port 443 looks like normal HTTPS |
| Scope | Protects all IP traffic | Usually per-application |
| Granularity | Coarse — full network access | Fine — can restrict to specific apps |

# Cloud Computing

Outsourcing infrastructure to a provider. Benefits: reduces traffic to your own systems, and adds a security boundary — if the hosted system is breached, the attacker doesn't automatically reach the rest of your internal network or workstations.

## The four service types

⚠️ Careful with the abbreviation: **Storage as a Service = STaaS**, **Software as a Service = SaaS**. Don't confuse them on the exam.

### Storage as a Service (STaaS)

- Basically a remote disk.
- Examples: iCloud, Google Drive, Dropbox, S3
- Uses: backups, access to your data from anywhere and from any device
- Downside: if the storage provider is compromised, your data goes with it

### Infrastructure as a Service (IaaS)

- The provider supplies everything needed to support the hardware: power, floor space, networking, cooling, fire suppression — all costly to run yourself.
- You get bare compute/storage/network; spinning up an instance takes no time.
- Examples: Amazon EC2, Microsoft Azure, Google Cloud, DigitalOcean

### Platform as a Service (PaaS)

- Sometimes you need a piece of software — a database server, a groupware server. Normally you'd have to buy it, license it, install and configure it.
- With PaaS you get an instance already configured and maintained; you just deploy your app or data on it.
- Examples: Azure SQL, Heroku, App Engine, RDS

### Software as a Service (SaaS)

- The finished application, delivered over the network. Nothing to install or manage.
- Examples: Google Docs, Office Online, Salesforce, Microsoft 365

## Comparing them

|  | You manage | Provider manages |
| --- | --- | --- |
| **On-prem** | Everything | Nothing |
| **IaaS** | OS, runtime, apps, data | Hardware, virtualization, network |
| **PaaS** | Apps and data only | + OS, runtime, middleware |
| **SaaS** | Your data and access only | Everything else |

Analogy: IaaS = you rent the kitchen. PaaS = you rent the kitchen with the appliances and ingredients. SaaS = you order the meal.

**Shared responsibility model:** the provider secures *the* cloud (infrastructure), the customer secures what's *in* the cloud (data, identities, configuration). Most real cloud breaches are customer-side misconfigurations — open S3 buckets, exposed management ports, over-permissive IAM roles.

**Deployment models:** public, private, hybrid, community.

## Internet of Things (IoT)

Everyday devices with **embedded software + network access**: refrigerators, toasters, coffee machines, home automation, DVRs, cable/satellite set-top boxes, cameras, thermostats.

- Cloud providers offer hubs to connect them to — e.g. **Microsoft Azure IoT Hub** — for data management and analytics.
- Security problems: default or hardcoded credentials, no patching mechanism, long lifespan, no visibility once deployed.
- Consequence: IoT devices are prime targets for **botnets** (Mirai) and are often the weakest entry point into a network.
- Related: **OT/ICS/SCADA** — the industrial equivalent, same weaknesses with physical consequences.