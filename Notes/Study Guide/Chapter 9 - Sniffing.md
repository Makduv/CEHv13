# Chapter 9 - Sniffing

**Sniffing** = the process of **capturing and analysing network traffic**. You use software or hardware tools — called a **packet sniffer** or **network analyser** — to capture data packets transmitted over a network.

**Ethereal** was the first free packet-capture tool that made network analysis **accessible to everyone**. It was renamed to **Wireshark** (still free, still the standard).

## Challenges for sniffing

Three obstacles stand between you and the traffic you want to see:

- **Switches** — unlike a hub, a switch sends traffic **only to the correct port**. You don't see other hosts' traffic by default. You need workarounds: port mirroring (SPAN), ARP spoofing, MAC flooding, or a network TAP
- **Segmentation** — VLANs and subnets divide the network. Traffic in another segment never reaches your interface unless you're on a trunk port or have pivoted into that segment
- **Encryption** — even if you capture the packets, encrypted traffic (TLS/HTTPS, IPSec, SSH) means the **payload is unreadable**. You can see headers (source/destination, ports) but not the content without the keys

# Packet Capturing

Capturing network traffic provides visibility into **sensitive communications** — passwords, usernames, authentication details — and useful information for navigating and assessing an organization's assets.

**Packet capturing** = acquiring network traffic that is addressed to **systems other than your own**.

## Promiscuous mode

A **Network Interface Card (NIC)** is programmed to only forward frames whose destination MAC address matches its own. To capture **all** traffic on the wire (not just yours), the NIC must be put in **promiscuous mode** — it then forwards every frame it sees up the stack.

## What you see

Once intercepted, the capturing software **parses the message** and extracts information about each **protocol header**. Each header has **fields specific to its protocol** describing how that protocol behaves.

The actual data carried between endpoints is the **payload**, which can be broken across **multiple packets and multiple frames**. Fragmentation can be forced by the **MTU** (Maximum Transmission Unit) at layer 2.

## tcpdump

A **command-line** packet capture tool for **Linux** and BSD systems. The Windows equivalent is **WinDump**.

Gives you a quick view of what's happening on the network, and can capture traffic and store it to a file.

```
tcpdump
```

Addresses are listed as `w.x.y.z.p` where `w.x.y.z` is the IP address and `p` is the port.

**No name resolution** (faster, cleaner output):

```
tcpdump -n
```

**Verbosity** — more detail with `-v`, `-vv`, `-vvv`:

```
tcpdump -vv -s 0
```

- Default mode shows **layer 3** info
- With verbosity you get **layer 4 header** details:
    - **UDP** → checksum info
    - **TCP** → sequence/acknowledgement numbers and **flags**

**Payload inspection** — hex dump of the payload with `-X`:

```
tcpdump -i eth0 -X
```

**Key parameters:**

| Flag | Purpose |
| --- | --- |
| `-i eth0` | Capture on a specific **interface** (default if not set) |
| `-X` | **Hex dump** of the payload |
| `-n` | No name resolution |
| `-v` / `-vv` / `-vvv` | Increasing **verbosity** |
| `-s 0` | Capture the **full packet** (no truncation) |
| `-w file.pcap` | **Write** to a PCAP file (includes session metadata) |
| `-r file.pcap` | **Read** a capture file back in |

```
tcpdump -w file.pcap
tcpdump -r file.pcap
```

## tshark

Wireshark's **command-line** version. More **granular detail** than tcpdump.

```
tshark
```

**Print specific fields:**

```
tshark -Tfields -e frame.number -e ip.src -e ip.dst -e ip.tos -e ip.ttl
```

This extracts: frame number, source/destination IP, type of service, TTL — useful for scripting and piping into other tools.

## Wireshark

**GUI-based** packet capture program — the standard.

**Advantages:**

- View packets easily with a visual interface
- See the **entire network stack** broken down for each packet
- Scroll through all captured frames
- Knows how to **decode any protocol**
- Can open files written by **tcpdump or tshark**
- Can **save captures** and apply **display filters**

### Decrypting TLS traffic

**Encryption** means only sender and recipient can read the message. **Session keys** are derived at session time using certificates (public/private key pairs) — symmetric keys are negotiated during the handshake.

To decrypt: you need the **session key**, which means you need the **certificate information** — or you need to sit **in the middle** of the conversation to capture the key exchange.

**SSL** is deprecated → now it's **TLS**.

Wireshark can take **RSA private keys** to decrypt TLS-encrypted messages — you enter the key in Wireshark's TLS settings. You can also enter a **pre-shared key** or a **pre-master secret log file** (the modern method, since RSA key exchange is being replaced by ephemeral Diffie-Hellman).

## Berkeley Packet Filter (BPF)

An **interface to the data-link layer** used across many systems and applications including tcpdump, tshark and Wireshark.

BPF syntax uses **primitives** (`host`, `ether`, `net`, `proto`, `port`) with **modifiers** (`src`, `dst`). Only packets matching the filter are captured or displayed.

**Capture everything except SSH:**

```
tcpdump not port 22
```

**Capture all TCP from/to a specific host:**

```
tcpdump tcp and \(host 192.168.86.1\)
```

- `\( \)` — escaped parentheses so Linux doesn't interpret them as shell syntax
- Parentheses **isolate** one part of the filter expression

## Port Mirroring / SPAN

Switches forward traffic **only to the correct port** — you can't sniff other hosts' traffic by default.

**Bypass: port mirroring** — get access to the switch and configure it to **copy traffic from one or more ports to your monitoring port**.

If you could only mirror **one port**, choose the **gateway/router port** — you'll see all traffic entering and leaving the network.

On **Cisco**, port mirroring is called **SPAN** (Switched Port Analyzer). Setting up mirroring = configuring a **SPAN port**. The process is called **port spanning**.

**Oversubscription risk:** if you mirror five **1 Gbps** ports to one **1 Gbps** monitoring port, you can easily exceed its capacity → **packets get dropped randomly**, and your capture is incomplete.

Alternative to SPAN: a **network TAP** (Test Access Point) — a hardware device inline on the cable that copies traffic passively. No switch configuration needed, no packet drops (within the TAP's rated speed).

---

# Cheatsheet

**Tools**

| Tool | Type | OS |
| --- | --- | --- |
| **tcpdump** | CLI packet capture | Linux / BSD |
| **WinDump** | CLI packet capture | Windows |
| **tshark** | CLI (Wireshark's CLI version), more granular than tcpdump | Cross-platform |
| **Wireshark** | GUI packet capture and analysis | Cross-platform |

**tcpdump commands**

```
tcpdump                              # basic capture
tcpdump -n                           # no name resolution
tcpdump -vv -s 0                     # verbose, full packets
tcpdump -i eth0 -X                   # hex dump on specific interface
tcpdump -w file.pcap                 # write to PCAP
tcpdump -r file.pcap                 # read PCAP
tcpdump not port 22                  # exclude SSH
tcpdump tcp and \(host 192.168.86.1\)  # TCP to/from a host
```

**tshark commands**

```
tshark                               # basic capture
tshark -Tfields -e frame.number -e ip.src -e ip.dst -e ip.tos -e ip.ttl
```

**tcpdump flags**

| Flag | Purpose |
| --- | --- |
| `-n` | No DNS resolution |
| `-i` | Interface |
| `-X` | Hex payload dump |
| `-v/-vv/-vvv` | Verbosity |
| `-s 0` | Full packet (no truncation) |
| `-w` | Write to PCAP |
| `-r` | Read from PCAP |

**BPF primitives:** `host`, `port`, `net`, `proto`, `ether` + modifiers `src`, `dst` + logic `and`, `or`, `not`.

**Key concepts**

- **Promiscuous mode** = NIC captures all frames, not just its own
- **PCAP** = Packet Capture file format (includes session metadata)
- **BPF** = Berkeley Packet Filter — filter syntax shared by tcpdump/tshark/Wireshark
- **SPAN** = Cisco's port mirroring (Switched Port Analyzer)
- **TAP** = hardware alternative to SPAN — passive, no drops
- **Oversubscription** = mirroring more bandwidth than the monitor port can handle → random packet drops
- **TLS decryption** in Wireshark requires the RSA key or a pre-master secret log

# Detecting Sniffers

Detecting sniffing means finding interfaces running in **promiscuous mode** — where the NIC stops filtering by MAC address and forwards **all** traffic to the OS.

## Detection methods

**Check the interface locally** — on **macOS**, `ifconfig` will show if an interface is in promiscuous mode (the `PROMISC` flag appears). This **doesn't work reliably on modern Linux or Windows** — the flag may not be exposed the same way.

**Probe from the network** — send a packet with the **correct IP address but a wrong MAC address**:

- **Not in promiscuous mode** → the NIC drops it (MAC doesn't match) → no response
- **In promiscuous mode** → the NIC forwards it anyway → the OS processes it and **responds**

A response to a packet with a bad MAC = that system is sniffing. **However**, this technique is **not fully reliable** — the message might not be delivered anyway due to switch behaviour (a switch won't forward a frame to a port whose MAC doesn't match its CAM table entry).

**Watch for ARP anomalies** — if someone is using **ARP spoofing** to redirect traffic to their interface for capture, there will be a **large number of ARP messages** on the network (gratuitous ARPs, ARP replies nobody asked for). An unusual volume of ARP traffic **may indicate** someone is capturing packets.

Other indicators: tools like **arpwatch** monitor MAC-to-IP mappings and alert on changes — a MAC that keeps flipping between IPs suggests spoofing.

*(No specific tools or commands introduced in this section — no cheatsheet.)*

# Packet Analysis

## Wireshark analysis features

Wireshark **identifies problems** in the frame list based on **default rules**:

- **Errors** are shown with a **black background and red text**
- [ ]  **`[ ]` brackets** in the packet details = information **added by Wireshark** to help you (not part of the actual packet)

**Relative sequence numbers** — TCP sequence numbers start at large random values, which are hard to follow. Wireshark **recalculates them for you**, starting at **1** and incrementing as you'd normally expect (based on the number of bytes transmitted). Much easier to track a conversation.

**Follow TCP Stream** — right-click a packet and select this to **filter out everything except the frames belonging to that conversation**. Shows the full exchange between client and server, colour-coded by direction.

**Statistics menu** — extensive options for understanding the capture:

- **Protocol hierarchy** — what percentage of traffic belongs to each protocol
- **Conversation statistics** — traffic volume between each pair of endpoints
- **I/O graphs**, endpoints, packet lengths, flow graphs, etc.

**Expert Information** — shows **all frames Wireshark has flagged as problematic**: errors, warnings, notes, and chat-level messages. A quick way to find issues without scrolling through thousands of packets.

**Time display** — by default Wireshark shows **relative time** (seconds since capture start). You can switch to **absolute time** (real clock time) for correlation with logs and other evidence.

## Packet Analysis using AI

**AI-powered packet analysis** = applying machine learning to inspect, classify and derive insight from network packets **at scale**.

Traditional capture analysis looks for **anomalies, security threats and performance issues** manually. AI enhances this by analysing:

- **Packet timing and round-trip delays**
- **Traffic flow patterns**
- **Protocol misuse**
- **Unexpected communication patterns**

**Tools and platforms:**

- **NetNerve** — AI-powered platform that turns hours of manual analysis into seconds of **plain-text threat intelligence**
- **AGILITY** — uses AI to process large volumes of packet data for **4G and 5G networks**
- AI can also be integrated into tools like **Scapy**, where packets are extracted and analysed in **real time**

AI-powered analysis enhances **visibility, troubleshooting and detection** — it spots patterns a human would take hours to find, and scales to volumes no analyst could handle manually.

# Spoofing Attacks

**Spoofing** = pretending to be a system you're not. The goal is usually to **sit in the middle** of a conversation between two endpoints (man-in-the-middle).

## ARP Spoofing (ARP Poisoning)

### How ARP works (2 stages)

1. **Request** — a system knows an IP address but not the corresponding MAC → broadcasts "who has this IP?"
2. **Reply** — the system with that IP responds with its MAC address

**The problem:** there is **no authentication** in ARP. Anyone can send a reply claiming to be any IP, and the receiver will cache the mapping — even if they **never asked** (this is called a **gratuitous ARP**).

### Cache timing

The attacker needs to keep sending gratuitous replies because cached ARP entries **expire**:

- **Linux:** 60 seconds (check in `/proc` filesystem)
- **Windows:** 30,000 ms × a random value between 0.5 and 1.5 → different from one system to another and from one boot to the next

That's why ARP spoofing tools **continuously resend** gratuitous responses.

### Forwarding

Once you're intercepting traffic, you need to **forward it back to the real destination** — otherwise the connection breaks and the victim notices. You must **enable IP forwarding** on your system so hijacked traffic continues to its intended destination.

### Tools

**arpspoof** (part of **dsniff** package on Linux) — injects you between two systems:

```
sudo arpspoof -i eth0 -c both 192.168.86.1
```

This places you between the **entire network** and the default gateway — you see all traffic heading out. Add `-t` for a specific target and `-r` to capture the reverse direction too.

**Ettercap** — more feature-rich, with a **console mode** and a **GUI mode**. Can run full MitM attacks:

- **Bridge sniff** — uses multiple interfaces (passive, very stealthy)
- **Unified sniff** — uses one interface
- Workflow: scan hosts → build a host list → select targets → launch **ARP poisoning**

## DNS Spoofing

Goal: get the target to visit **systems you control** by intercepting their DNS requests and responding with **your own IP addresses**. When the victim asks for a website, you redirect them to **your server** with your own fake site.

**Poison the cache once** and the system continues sending requests to the wrong address for the lifetime of the TTL.

**Using Ettercap:**

1. Create a config file that looks like a **DNS zone file** — record name, record type, mapped address → stored in `/etc/ettercap/`
2. Start sniffing traffic (requires **ARP spoofing** already in place to see the requests)
3. Go to the **plugin menu** → manage plugins → enable the **dns_spoof** plugin

**Why it works:** DNS uses **UDP** — connectionless, no handshake, easy to inject a faster reply before the real server responds.

**Requires local network access** to capture the requests.

## DHCP Starvation Attack

Works against **IPv4** addresses that are dynamically provided to endpoints.

**The attack:**

1. The attacker floods the DHCP server with many **DHCPDISCOVER** messages (sent to broadcast `255.255.255.255`)
2. The DHCP server responds with an **offer** for each, reserving IP addresses and expecting an acknowledgement
3. The acknowledgement **never comes** — the server keeps addresses reserved
4. The DHCP server **runs out of addresses** → legitimate endpoints can't get an IP → **denial of service**

**The follow-up:** the attacker can now spin up their **own rogue DHCP server** — it works via broadcast, so no need to pretend to be a specific address. In addition to IPs, DHCP provides other configuration: the attacker can hand out **bogus DNS servers** or a **default gateway they control** → full MitM.

## sslstrip

Encrypted messages are problematic for sniffing — end-to-end encryption means **no way to sit in the middle** and read the content.

**sslstrip** acts as a **transparent proxy** — it intercepts traffic and **strips the encryption**:

- When the server redirects HTTP → HTTPS, sslstrip **changes the links from HTTPS back to HTTP**
- The victim talks HTTP to the attacker; the attacker talks HTTPS to the server
- The victim doesn't get the padlock, but many users don't notice

**Requirements:**

- **ARP spoofing must already be in place** (so traffic routes through you)
- Works on **SSL** and **TLS versions up to 1.2**. Modern TLS 1.3 and HSTS (HTTP Strict Transport Security) defeat it

## Spoofing Detection and Defence

| Attack | Detection / Defence |
| --- | --- |
| **ARP spoofing** | IDS/IPS, **reverse path verification** on the router (checks that the source IP is reachable via the interface the packet arrived on), **Dynamic ARP Inspection (DAI)** on the switch, **arpwatch** to monitor MAC-IP changes |
| **DNS spoofing** | Use **DNS over TCP** instead of UDP (harder to inject), implement **DNSSEC** (DNS Security Extensions — cryptographically signs DNS responses so they can't be forged) |
| **DHCP starvation** | **DHCP snooping** on the switch — limits the rate of DHCP messages per port and only trusts DHCP replies from authorised (trusted) server ports |

---

# Cheatsheet

**Tools**

| Tool | Purpose | Command |
| --- | --- | --- |
| **arpspoof** (dsniff) | ARP spoofing between two hosts | `sudo arpspoof -i eth0 -c both 192.168.86.1` |
| **Ettercap** | ARP spoofing + DNS spoofing + MitM (CLI or GUI) | GUI: scan → select targets → ARP poisoning → enable dns_spoof plugin |
| **sslstrip** | Strip TLS, downgrade HTTPS → HTTP | Requires ARP spoof in place first |

**ARP spoofing requirements:**

- Continuous gratuitous ARPs (cache expires: Linux ~60s, Windows ~15-45s)
- **IP forwarding** enabled so traffic reaches its real destination

**DNS spoofing with Ettercap:**

- Config file in `/etc/ettercap/` (DNS zone format)
- ARP spoof must be running first
- Enable the `dns_spoof` plugin

**DHCP starvation:**

- Flood DHCPDISCOVER to broadcast → exhaust the pool → DoS
- Follow up with a **rogue DHCP server** handing out attacker-controlled DNS/gateway

**Key points**

- ARP has **no authentication** → gratuitous ARP = poison the cache without a request
- DNS works because it's **UDP** → easy to inject a faster reply
- sslstrip works up to **TLS 1.2** — defeated by **HSTS** and **TLS 1.3**
- All spoofing attacks require **local network access**
- Defences: **DAI** (ARP) · **DNSSEC** (DNS) · **DHCP snooping** (DHCP) · **HSTS** (SSL strip)