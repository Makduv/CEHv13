# Module 08: Sniffing

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Sniffing Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-sniffing-concepts)
2. [Sniffing Technique: MAC Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-sniffing-technique-mac-attacks)
3. [Sniffing Technique: DHCP Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-sniffing-technique-dhcp-attacks)
4. [Sniffing Technique: ARP Poisoning](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-sniffing-technique-arp-poisoning)
5. [Sniffing Technique: Spoofing Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-sniffing-technique-spoofing-attacks)
6. [Sniffing Technique: DNS Poisoning](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-sniffing-technique-dns-poisoning)
7. [Sniffing Tools](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-sniffing-tools)
8. [Sniffing Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#8-sniffing-countermeasures)
9. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#9-quick-exam-cheat-sheet)

---

## 1. Sniffing Concepts

### 🎯 Network Sniffing

> **Packet sniffing** = process of monitoring and capturing all data packets passing through a network using a software application or hardware device. Allows an attacker to observe/access entire network traffic from one point.
> 
- Sniffers capture data such as: Telnet passwords, email traffic, syslog traffic, router config, web traffic, DNS traffic, FTP passwords, chat sessions, account info
- **Hub vs Switch:** A hub transmits data to every port (no line mapping); a switch reads the MAC address in each frame and sends data only to the required port
- A sniffer placed in **promiscuous mode** can capture and analyze all network traffic — turns off the NIC filter that normally blocks non-addressed traffic

---

### 📡 How a Sniffer Works

- Every LAN-connected computer has a **MAC address** and an **IP address**
- Data link layer uses Ethernet header with destination MAC; network layer maps IP to MAC via **ARP cache**
- If no ARP entry exists, an ARP broadcast request goes to all machines on the subnet; the target responds with its MAC address

**Two basic Ethernet environments:**

| Environment | Description |
| --- | --- |
| **Shared Ethernet** | Single bus connects all hosts competing for bandwidth; all machines receive all packets (hub-based) |
| **Switched Ethernet** | Switch regulates data flow between ports using a **CAM table**; more secure |

---

### 📊 Types of Sniffing

| Type | Description |
| --- | --- |
| **Passive Sniffing** | Sniffing through a hub — traffic sent to all ports. Involves monitoring packets sent by others WITHOUT sending additional data packets. Works in a "common collision domain." Provides significant stealth advantage |
| **Active Sniffing** | Used to sniff a **switch-based network**. Involves injecting ARP packets to flood the switch's CAM table |

**6 Active Sniffing Techniques:**

```
MAC Flooding       |  DHCP Attacks
DNS Poisoning      |  Switch Port Stealing
ARP Poisoning      |  Spoofing Attack
```

---

### 🎯 How an Attacker Hacks the Network Using Sniffers — 6 Steps

```
1. Discover the appropriate switch, connect a system to a port
2. Determine network info (topology) using network discovery tools
3. Analyze topology, identify the victim's machine
4. Use ARP spoofing to send fake ARP messages (MITM setup)
5. Redirect traffic from victim to attacker's computer
6. Extract sensitive info from captured packets (passwords, credit cards, PINs)
```

---

### ⚠️ Protocols Vulnerable to Sniffing

> Main reason for sniffing these protocols: **to acquire passwords**
> 

| Protocol | Vulnerability |
| --- | --- |
| **Telnet & Rlogin** | No encryption — keystrokes, usernames, passwords sent in plaintext |
| **HTTP** | Default version transfers user data in plaintext |
| **SNMP** | SNMPv1/v2 offer no strong security — cleartext data |
| **SMTP** | Messages transmitted in cleartext; no sniffing protection |
| **NNTP** | Fails to encrypt data |
| **POP** | Weak security — cleartext data flow |
| **FTP** | No encryption; attackers sniff credentials with tools like hashcat |
| **IMAP** | Inadequate security — cleartext data/credentials |
| **TFTP** | No authentication/encryption — built on UDP/IP |

---

### 🔌 Hardware Protocol Analyzers

> A device that interprets traffic passing over a network WITHOUT altering the traffic segment. Captures packet, decodes it, analyzes content per predetermined rules.
> 
- More capable than software analyzers (no packet drops during overload)
- Wide range of connections: LAN, WAN, wireless, telco lines
- More expensive — out of reach for hobbyists/ordinary hackers
- Examples: **Xgig 1000** (VIAVI), **SierraNet M1288** (Teledyne LeCroy)

---

## 2. Sniffing Technique: MAC Attacks

### 🔑 MAC Address Structure

```
| 3 Bytes: OUI (Organizationally Unique Identifier) | 3 Bytes: NIC Specific |
                        8 Bits: a8 a7 a6 a5 a4 a3 a2 a1
a1 bit: 0=Unicast, 1=Multicast
a2 bit: 0=Globally unique, 1=Locally administered
```

### 📊 CAM Table

> **CAM (Content Addressable Memory) Table** = dynamic, fixed-size table storing MAC addresses on physical ports + VLAN parameters. Switch searches CAM table for destination MAC in Ethernet frame, forwards data to the bound port. More secure than hub-based forwarding.
> 

| vlan | MAC Add | Type | Learn | Age | Ports |
| --- | --- | --- | --- | --- | --- |
| 255 | 00:d3:ad:34:12:3g | Dynamic | Yes | 0 | Gi5/2 |
| 5 | as:23:df:45:45:t6 | Dynamic | Yes | 0 | Gi2/5 |

---

### 💥 MAC Flooding

> Attacker floods the switch's CAM table with **fake MAC addresses** until it fills up. Once full, the switch fails and converts to **hub-like operation** (fail-open mode), broadcasting all traffic to all ports — enabling the attacker to sniff.
> 

**Tool: macof** (part of dsniff collection, Unix/Linux)

```bash
macof -i eth0 -n 10
```

- Floods local network with random MAC/IP addresses
- Can flood CAM tables at **131,000 entries/min**

---

### 🔒 Defending Against MAC Flooding — Port Security

**Configure Port Security on Cisco Switch:**

```
1. interface interface_id          # Enter interface config mode
2. switchport mode access          # Set interface as access mode
3. switchport port-security        # Enable port security
```

- Limits MAC addresses allowed on a port to **one**
- Excess MAC requests recognized as flooding → port locks down, sends SNMP trap

---

### 🔀 Switch Port Stealing

> A sniffing technique where the attacker spoofs BOTH the IP and MAC address of the target machine (Host B).
> 

**Process:**

1. Attacker's machine runs sniffer in promiscuous mode
2. Host A sends ARP request for Host B's IP (10.0.0.2)
3. Switch broadcasts the ARP request
4. Before Host B responds, attacker sends spoofed ARP reply with Host B's IP but attacker's MAC
5. Switch's CAM table gets a **spoofed entry** — updates Host B's IP to the attacker's port
6. Host A's traffic to Host B now reaches the attacker instead

---

## 3. Sniffing Technique: DHCP Attacks

### 🔄 How DHCP Works (DORA Process)

1. Client broadcasts **DHCPDISCOVER/SOLICIT** request
2. DHCP-relay agent captures and unicasts to servers
3. (Server sends **DHCPOFFER**)
4. (Client sends **DHCPREQUEST**)
5. (Server sends **DHCPACK**)

### 📋 DHCP Message Types (Table)

| Message | Purpose |
| --- | --- |
| DHCPRelease | Client to server — relinquish network address |
| DHCPDecline | Client to server — network address already in use |
| Reconfigure | Server to client — new/updated config settings |
| DHCPInform | Client to server — asking for local config only |
| Relay-Forward | Relay agent → server, forwards messages |
| Relay-Reply | Server → relay agent |
| DHCPNAK | Server to client — indicates incorrect network address notion |

---

### 💥 DHCP Starvation Attack

> A **DoS attack** on DHCP servers — attacker broadcasts forged DHCP requests, trying to lease ALL DHCP addresses available in the scope. Legitimate users can't obtain/renew an IP address → fails to access the network.
> 

**Tool: Yersinia**

```
yersinia -I    # Interactive DHCP mode
```

Shows: Total Packets, DHCP Packets, MAC Spoofing indicator

**DHCP Starvation Attack Tools:** dhcpStarvation.py, Metasploit, Hyenae, DHCPig

---

### 🎭 Rogue DHCP Server Attack

> Attacker sets up a **rogue DHCP server** that responds to DHCP requests with bogus IP addresses, resulting in compromised network access. Often works in conjunction with DHCP starvation (knocks victim off genuine server first).
> 

**Attack flow:**

```
1. DHCPDISCOVER (broadcast) sent by user
2. DHCPOFFER from Rogue Server (unicast) — reaches user faster
3. DHCPREQUEST (broadcast)
4. DHCPACK from Rogue Server (unicast)
```

**Malicious settings the rogue server can push:**

- **Wrong Default Gateway** → Attacker becomes the gateway
- **Wrong DNS server** → Attacker becomes the DNS server
- **Wrong IP Address** → DoS with spoofed IP

---

### 🔒 Defending Against Rogue DHCP — DHCP Snooping

**IOS Global Commands (Cisco):**

```
1. ip dhcp snooping                                    # Enable globally
2. ip dhcp snooping vlan number [number] | vlan {range} # Enable per VLAN
   ip dhcp snooping vlan 4,104
3. ip dhcp snooping trust                               # Configure trusted interface
4. ip dhcp snooping limit rate                          # Limit DHCP packets/sec
5. end
6. show ip dhcp snooping                                # Display VLANs with snooping enabled

no ip dhcp snooping information option                 # Disable option-82 insertion
```

> 💡 All ports in the VLAN are **untrusted by default**.
> 

---

## 4. Sniffing Technique: ARP Poisoning

### 🎯 What is ARP?

> **ARP (Address Resolution Protocol)** = stateless TCP/IP protocol that maps IP addresses to MAC (hardware) addresses used by the data link protocol.
> 

**Process to obtain MAC via ARP:**

1. Source generates ARP request packet (source MAC, source IP, destination IP) → sends to switch
2. Switch reads source MAC, searches CAM table
3. Switch updates CAM table if entry not found; broadcasts ARP request

---

### ☠️ ARP Spoofing Attack — Working (6-Step Diagram — CRITICAL)

```
1. User A: "I want to connect to 10.1.1.1, but I need a MAC address"
2. User A sends ARP request
3. Switch broadcasts ARP request onto the wire
4. Legitimate User B responds: "Yes I am 10.1.1.1, MAC is A1-B1-C1-D1-E1-F1"
5. Attacker eavesdrops on the ARP request/response, spoofs as legitimate user
6. Attacker sends malicious reply: "I am 10.1.1.1, MAC is 11-22-33-44-55-66"
   → Poisoned ARP cache now routes 10.1.1.1 traffic to attacker
```

> ARP spoofing succeeds by changing the IP address of the attacker's computer to that of the target. A forged ARP request/reply finds its way into the target's ARP cache.
> 

---

### ⚠️ Threats of ARP Poisoning

| Threat | Description |
| --- | --- |
| **Packet Sniffing** | Sniffs traffic over the network or a network segment |
| **Session Hijacking** | Steals valid session info to gain unauthorized application access |
| **VoIP Call Tapping** | Uses port mirroring to monitor traffic, picks VoIP traffic by MAC address |

**ARP Poisoning Tools:** bettercap, Ettercap, RITM, ARP Spoofer, Iarp, Habu (`habu.arp.poison <IP1> <IP2>`)

---

### 🔒 Defending Against ARP Poisoning — Dynamic ARP Inspection (DAI)

```
show ip arp inspection    # Shows source MAC/dest MAC/IP validation status per VLAN
ip arp inspection validate <address-type>   # Enable additional validation checks
```

**DAI process:** Switch inspects ARP reply packets, compares against **DHCP snooping table**. If no matching entry for source IP on the interface → packet is **discarded**.

```
%SW_DAI-4-DHCP_SNOOPING_DENY: 1 Invalid ARPs (Res) on Fa0/5, vlan 10
```

---

## 5. Sniffing Technique: Spoofing Attacks

> Besides ARP spoofing, attackers use: **MAC spoofing, IRDP spoofing, VLAN hopping, and STP attacks**.
> 

### 🎭 MAC Spoofing/Duplicating

> Attack involves sniffing a network for MAC addresses of clients actively associated with a switch port, then **reusing one of those addresses**. Bypasses the switch rule "allow access only if MAC = X" and also bypasses **Wireless AP MAC filtering**.
> 

**Tools:** MAC Address Changer (appsvoid.com) — generates randomized MAC, can restore original

---

### 🔀 VLAN Hopping

**Two techniques:**

| Technique | Description |
| --- | --- |
| **Switch Spoofing** | Attacker's rogue switch establishes an **unauthorized trunk** link with the legitimate switch, gaining access to all VLANs |
| **Double Tagging** | Attacker's Ethernet frame contains TWO 802.1Q tags (inner = target VLAN, outer = native VLAN of attacker). Switch strips the outer tag (matches native VLAN) and forwards with the inner tag onto ALL trunk interfaces — attacker jumps from native VLAN to victim's VLAN. Only works if switch ports use native VLANs |

---

### 🌲 STP (Spanning Tree Protocol) Attack

> Attacker connects a **rogue switch** into the network to change STP operations and sniff all traffic. STP removes network loops; a **root bridge** is elected via **BPDUs (Bridge Protocol Data Units)**, each with a **BID** (Bridge Priority + MAC address). Default Bridge Priority = **32769**.
> 

**Attack:** Attacker introduces a rogue switch with **priority = 0** (lower than any switch) → becomes the root bridge → all traffic routes through attacker.

**Defenses:**

```
BPDU Guard:   spanning-tree portfast bpduguard        # Shuts port (errdisable) if BPDU received on PortFast port
Root Guard:   spanning-tree guard root                # Forces port to stay a designated port; loop-inconsistent state if superior BPDU received
Loop Guard:   (on PortFast edge ports)
UDLD:         udld { enable | disable | aggressive }  # Unidirectional Link Detection
```

---

### 🔒 Defending Against MAC Spoofing

- Use **DHCP Snooping Binding Table** (MAC, IP, lease time, binding type, VLAN, interface — acts as firewall between untrusted interfaces)
- Combine with **Dynamic ARP Inspection** and **IP Source Guard**
- Best defense: place server behind router (routers depend on IP, not MAC)
- Enable port-security to specify allowed MAC per port

---

## 6. Sniffing Technique: DNS Poisoning

### 📊 4 DNS Poisoning Techniques

```
1. Intranet DNS Spoofing
2. Internet DNS Spoofing
3. Proxy Server DNS Poisoning
4. DNS Cache Poisoning
```

---

### 🌐 Intranet DNS Spoofing (5-Step Diagram)

> Performed on a switched LAN using **ARP poisoning**. Attacker must be connected to the LAN and able to sniff packets.
> 

```
1. Attacker poisons the router (via arpspoof/dnsspoof), redirects DNS requests to own machine
2. Client (John) sends DNS request to router
3. Poisoned router sends DNS response with fake IP (attacker's fake website)
4. Client's browser connects to the fake IP instead of real site
5. Attacker sniffs credentials, forwards request to real website (transparent MITM)
```

---

### 🔒 Defending Against DNS Spoofing — 14-Point List (CRITICAL)

1. Implement **DNSSEC** (Domain Name System Security Extensions)
2. Use **SSL** for securing traffic
3. Resolve all DNS queries to a **local DNS server**
4. Block DNS requests to external servers
5. Configure firewall to restrict external DNS lookup
6. Implement **IDS** and deploy correctly
7. Configure DNS resolver to use a **new random source port** per query
8. Restrict DNS recursing service to authorized users
9. Use **DNS NXDOMAIN rate limiting**
10. Secure internal machines
11. Use static ARP and IP tables
12. Use **SSH encryption**
13. Do not allow outgoing traffic to use **UDP port 53** as default source port
14. Audit the DNS server regularly to remove vulnerabilities

---

## 7. Sniffing Tools

### 🦈 Wireshark

> Source: https://www.wireshark.org — Lets you capture and interactively browse traffic. Uses **WinPcap**. Captures live traffic from Ethernet, IEEE 802.11, PPP/HDLC, ATM, Bluetooth, USB, Token Ring, Frame Relay, FDDI.
> 
- **Follow TCP Stream** feature — reveals full session content (e.g., cleartext passwords in HTTP POST)
- Supports display filters for customized data view

### 🛠️ Other Sniffing Tools

| Tool | Description |
| --- | --- |
| **Capsa Portable Network Analyzer** | Portable performance analysis/diagnostics, packet capture |
| **OmniPeek** | Displays Google Map of public IPs of captured packets |
| **RITA** | Real Intelligence Threat Analytics |
| **Observer Analyzer** | Network monitoring |
| **PRTG Network Monitor** | Network performance monitoring |
| **Network Performance Monitor** (SolarWinds) | Network performance monitoring |

---

## 8. Sniffing Countermeasures

### 🔒 General Sniffing Countermeasures (CRITICAL LIST)

- Permanently add gateway's MAC address to the ARP cache
- Use **static IP addresses and ARP tables**
- Turn off network identification broadcasts; restrict to authorized users
- Use **IPv6** instead of IPv4 (IPsec mandatory in IPv6, optional in IPv4)
- Use encrypted sessions: **SSH instead of Telnet, SCP instead of FTP**, SSL for email
- Use **HTTPS instead of HTTP**
- Use a **switch instead of a hub**
- Use **SFTP** instead of FTP
- Use **PGP and S/MIME, VPN, IPSec, SSL/TLS, SSH, OTPs**
- Use **POP3** instead of POP; **SNMPv3** instead of SNMPv1/v2
- Encrypt wireless traffic with **WPA2 or WPA3**
- Retrieve MAC addresses directly from NICs (not OS) to prevent spoofing
- Use tools to detect NICs running in promiscuous mode
- Use **ACLs** to allow only trusted IP ranges
- Change default passwords; avoid broadcasting SSIDs
- Implement **MAC filtering** on the router
- Use network scanning/monitoring tools for malicious intrusions, rogue devices, sniffers
- Avoid unsecured/open Wi-Fi networks
- Use **VLANs** for network segmentation
- Regularly monitor/audit traffic for unusual patterns
- Use **VPNs** for secure tunnels over public networks

---

### 🔍 Sniffer Detection Techniques (CRITICAL TABLE)

| Method | How It Works |
| --- | --- |
| **Ping Method** | Send ping with **correct IP but incorrect (fake) MAC address** to suspect machine. Normal NIC rejects it (MAC mismatch) — no response. A NIC in **promiscuous mode** doesn't check MAC properly and responds anyway → reveals the sniffer |
| **DNS Method** | Most sniffers perform reverse DNS lookups to resolve captured IPs into hostnames. A machine generating **reverse DNS lookup traffic** is very likely running a sniffer |
| **ARP Method** | Send a **non-broadcast ARP** to all nodes. Only the machine in promiscuous mode caches the ARP info. Then broadcast a ping with the same IP but different MAC — only the node that cached the ARP (promiscuous mode machine) responds |

**Detection Tool:** NetScanTools Pro — Promiscuous Mode Scanner (scans IP range, flags "Promiscuous Mode" or "Maybe" in analysis column)

---

## 9. Quick Exam Cheat Sheet

### 🔑 Key Definitions

```
CAM Table   = MAC address ↔ port mapping on a switch
ARP Cache   = IP ↔ MAC mapping maintained by OS
DORA        = Discover, Offer, Request, Acknowledge (DHCP process)
BID         = Bridge ID (Bridge Priority + MAC) — used in STP root bridge election
Default Bridge Priority = 32769
```

---

### 📊 6 Active Sniffing Techniques

```
MAC Flooding | DHCP Attacks | DNS Poisoning |
Switch Port Stealing | ARP Poisoning | Spoofing Attack
```

---

### 📊 4 Spoofing Attack Types

```
MAC Spoofing | IRDP Spoofing | VLAN Hopping | STP Attack
```

---

### 📊 4 DNS Poisoning Techniques

```
Intranet DNS Spoofing | Internet DNS Spoofing |
Proxy Server DNS Poisoning | DNS Cache Poisoning
```

---

### 🔍 3 Sniffer Detection Methods

```
Ping Method → fake MAC, correct IP → promiscuous NIC responds anyway
DNS Method  → detect excessive reverse DNS lookup traffic
ARP Method  → non-broadcast ARP + broadcast ping → only cached node responds
```

---

### 🛠️ Key Tools by Attack

| Attack | Tool |
| --- | --- |
| MAC Flooding | macof |
| DHCP Starvation | Yersinia, dhcpStarvation.py, DHCPig |
| ARP Poisoning | bettercap, Ettercap, Habu |
| MAC Spoofing | MAC Address Changer |
| General Sniffing | Wireshark, Capsa, OmniPeek |
| Promiscuous Detection | NetScanTools Pro |

---

### 🔥 Common Exam Scenarios

**Q: What's the main difference between passive and active sniffing?**
→ Passive = hub-based, no packets sent; Active = switch-based, injects ARP packets to flood CAM table

**Q: What tool floods a switch's CAM table?**
→ **macof**

**Q: What's the default Bridge Priority value in STP?**
→ **32769**

**Q: What Cisco command enables port security globally on an interface?**
→ `switchport port-security`

**Q: What are the 4 spoofing techniques besides ARP spoofing?**
→ **MAC Spoofing, IRDP Spoofing, VLAN Hopping, STP Attack**

**Q: What are the two VLAN hopping techniques?**
→ **Switch Spoofing** (rogue trunk) and **Double Tagging** (two 802.1Q tags)

**Q: What DHCP snooping command enables the feature globally?**
→ `ip dhcp snooping`

**Q: What technique detects sniffers by sending a ping with correct IP but wrong MAC?**
→ **Ping Method**

**Q: What technique detects sniffers via excessive reverse DNS lookups?**
→ **DNS Method**

**Q: What Cisco feature protects the root bridge from STP attacks?**
→ **Root Guard**

**Q: What table does Dynamic ARP Inspection compare ARP replies against?**
→ **DHCP Snooping table**

**Q: What port is commonly abused as a DNS source port that should be randomized?**
→ **UDP port 53**

**Q: What Wireshark feature reveals full session content like cleartext passwords?**
→ **Follow TCP Stream**

**Q: What's the main threat enabled by ARP poisoning related to VoIP?**
→ **VoIP Call Tapping** (via port mirroring)

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 08*