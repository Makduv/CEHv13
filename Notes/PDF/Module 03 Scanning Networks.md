# Module 03: Scanning Networks

> **Exam:** 312-50 | **Phase in Hacking:** Phase 1 (Active Recon) → Phase 2 (Vulnerability Scanning)
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Network Scanning Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-network-scanning-concepts)
2. [Host Discovery Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-host-discovery-techniques)
3. [Port and Service Discovery](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-port-and-service-discovery)
4. [OS Discovery / Banner Grabbing](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-os-discovery--banner-grabbing)
5. [Scanning Beyond IDS and Firewall](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-scanning-beyond-ids-and-firewall)
6. [Network Scanning Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-network-scanning-countermeasures)
7. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-quick-exam-cheat-sheet)

---

## 1. Network Scanning Concepts

### 🎯 What is Network Scanning?

> **Network Scanning** = set of procedures for **identifying hosts, ports, and services** in a network. It is an extended form of active reconnaissance where the attacker learns about OSes, services, and configuration lapses — to develop an attack strategy.
> 
- Sends **TCP/IP probes** → analyzes responses to create a profile of the target
- One of the **most important phases** of information gathering
- Primary tools: **Nmap, Hping3, Metasploit, NetScanTools Pro**

---

### 🗂️ Three Types of Scanning

| Type | Purpose |
| --- | --- |
| **Port Scanning** | Lists open ports and services — determines whether services are running/listening |
| **Network Scanning** | Lists active hosts and IP addresses in a network |
| **Vulnerability Scanning** | Identifies known weaknesses — checks whether a system is exploitable |

> 💡 **Key rule:** The more open ports on a system, the more vulnerable it is in general — but some systems with fewer open ports may still be highly vulnerable.
> 

---

### 🎯 Objectives of Network Scanning

- Discover **live hosts, IP addresses, and open ports** of live hosts → determines entry points
- Discover the **OS and system architecture** (also called fingerprinting)
- Discover **services running/listening** on target → reveals vulnerabilities per service
- Identify **specific application versions**
- Identify **vulnerabilities** in network systems
- Map out the **network topology** (devices, routers, switches, interconnections)

---

### 🚩 TCP Communication Flags (CRITICAL)

> The TCP header contains **6 control flags** (each 1 bit). When a flag is set to "1," it is active.
> 

| Flag | Full Name | Function |
| --- | --- | --- |
| **SYN** | Synchronize | **Initiates a connection** — signals new sequence number in 3-way handshake |
| **ACK** | Acknowledgement | **Confirms receipt** — identifies next expected sequence number |
| **PSH** | Push | **Sends buffered data immediately** — set at start and end of data transfer to prevent buffer deadlocks |
| **URG** | Urgent | **Process data immediately** — stops other processing; gives priority to urgent data |
| **FIN** | Finish | **No more transmissions** — terminates connection established by SYN |
| **RST** | Reset | **Aborts connection** on error — attackers use RST to scan hosts and identify open ports |

> 💡 **Exam tip:** SYN scanning primarily uses **SYN, ACK, and RST** — these 3 flags are most important for port scanning.
> 

**TCP Header Structure:**

```
| Source Port      | Destination Port |
|         Sequence Number              |
|       Acknowledgement Number         |
| Offset | Res | TCP Flags  | Window  |
|    TCP Checksum   | Urgent Pointer   |
|               Options                |
|←————————— 0–31 Bits ————————————→|
```

---

### 🤝 TCP/IP Communication

**TCP is connection-oriented** — must establish connection before data transfer.

**3-Way Handshake (Session Establishment):**

```
Client (10.0.0.2:21)              Server (10.0.0.3:21)
     ──── SYN, SEQ#10 ──────────────>
     <─── SYN+ACK, ACK#11, SEQ#142 ──
     ──── ACK, ACK#143, SEQ#11 ──────>
            [CONNECTION OPEN]
```

**4-Way Termination (Session Termination):**

```
Client                              Server
     ──── FIN, SEQ#50 ─────────────>
     <─── ACK, ACK#51, SEQ#170 ─────
     <─── FIN, SEQ#171 ─────────────
     ──── ACK, ACK#172, SEQ#51 ─────>
            [CONNECTION CLOSED]
```

---

### 🛠️ Core Scanning Tools

#### Nmap (Network Mapper)

> Source: https://nmap.org | GUI: **Zenmap**
> 
- Discovers hosts, ports, services; creates network "map"
- Sends specially crafted packets → analyzes responses
- Capabilities: port scanning (TCP/UDP), OS detection, version detection, ping sweeps, NSE scripting
- Syntax: `nmap <options> <Target IP address>`

#### Hping3

- Crafts and sends custom TCP/IP packets (ICMP, UDP, TCP, RAW-IP modes)
- Useful for: firewall testing, advanced port scanning, OS fingerprinting, idle scanning
- Syntax: `hping3 <options> <Target IP address>`

**Key Hping3 Commands:**

```bash
hping3 -1 10.10.1.11                     # ICMP ping scan
hping3 -A 10.10.1.11 -p 80              # ACK scan on port 80
hping3 -8 50-60 -S 10.0.0.25 -V         # SYN scan on ports 50-60
hping3 -F -P -U 10.0.0.25 -p 80         # FIN+PUSH+URG scan (Xmas-style)
hping3 -1 10.0.1.x --rand-dest -I eth0  # ICMP scan entire subnet
hping3 -S 72.14.207.99 -p 80 --tcp-timestamp  # Bypass timestamp firewalls
hping3 -9 HTTP -I eth0                   # Listen mode — intercept HTTP traffic
```

#### NetScanTools Pro

> Source: https://www.netscantools.com
> 
- Lists IPv4/IPv6, hostnames, domain names, email addresses, open ports
- Categories: active, passive, DNS, local computer tools

**Other Tools:** sx, RustScan, MegaPing, SolarWinds Engineer's Toolset, PRTG Network Monitor

---

## 2. Host Discovery Techniques

> Host discovery = identify the **active/live systems** in the network before scanning ports.
> 

### 📊 Host Discovery Map

```
Host Discovery Techniques
├── ARP Ping Scan           nmap -sn -PR <Target>     [Layer 2 — most reliable on LAN]
├── UDP Ping Scan           nmap -sn -PU <Target>
├── ICMP Ping Scan
│   ├── ICMP ECHO Ping      nmap -sn -PE <Target>
│   ├── ICMP ECHO Sweep     nmap -sn -PE <IP Range>   [Ping sweep]
│   ├── ICMP Timestamp      nmap -sn -PP <Target>
│   └── ICMP Address Mask   nmap -sn -PM <Target>
├── TCP Ping Scan
│   ├── TCP SYN Ping        nmap -sn -PS <Target>     [Port 80 default]
│   └── TCP ACK Ping        nmap -sn -PA <Target>     [Port 80 default]
└── IP Protocol Scan        nmap -sn -PO <Target>
```

---

### 🔍 ARP Ping Scan

**Command:** `nmap -sn -PR 10.10.1.11`**Zenmap option:** `-PR`

- Sends **ARP request probes** → if ARP response received, host is active
- Works even when ICMP is **blocked by firewalls** (operates at Layer 2)
- **Nmap default** on local networks — use `-disable-arp-ping` to override
- Shows MAC address and vendor (e.g., Microsoft)

**Advantages:**

- More **efficient and accurate** than other host discovery techniques
- Automatically handles ARP retransmission and timeouts
- Useful for scanning **large address spaces**
- Displays response time/latency per device

---

### 🔍 UDP Ping Scan

**Command:** `nmap -sn -PU 10.10.1.11`**Default port:** 40,125

- Sends **UDP packets** to target
- **Host active:** UDP response
- **Host inactive:** "host/network unreachable" or "TTL exceeded" ICMP error

---

### 🔍 ICMP Ping Techniques

| Technique | Nmap Command | How It Works |
| --- | --- | --- |
| **ICMP ECHO Ping** | `nmap -sn -PE 10.10.1.11` | Sends ICMP ECHO request → expects ICMP ECHO reply |
| **ICMP ECHO Ping Sweep** | `nmap -sn -PE 10.10.1.0/24` | Sends ICMP ECHO to multiple hosts → finds live IPs in range |
| **ICMP Timestamp Ping** | `nmap -sn -PP 10.10.1.11` | Queries timestamp — useful when ECHO is blocked |
| **ICMP Address Mask Ping** | `nmap -sn -PM 10.10.1.11` | Queries subnet mask information |

> **Ping sweep** = also known as ICMP sweep — oldest and slowest but still widely used. Active hosts send ICMP ECHO reply.
> 

**AI-assisted ping sweep:**

```bash
# Prompt: "Use Nmap to perform ICMP ECHO ping sweep on 10.10.1.0/24"
nmap -sn -PE 10.10.1.0/24

# Save live IPs to file
nmap -sn 10.10.1.0/24 -oG- | awk '/Up$/{print $2}' > scan1.txt
```

---

### 🔍 TCP Ping Scans

#### TCP SYN Ping — `nmap -sn -PS 10.10.1.11`

- Sends **empty SYN packet** to port 80 (default)
- Target responds with **SYN+ACK** (port open) or **RST** (port closed)
- Either response confirms **host is alive** → attacker sends RST
- Port range: `PS22-25,80,113,1050,35000`
- **Advantage:** Logs NOT recorded — leaves no traces

#### TCP ACK Ping — `nmap -sn -PA 10.10.1.11`

- Sends **empty ACK packet** to port 80 (default)
- Unsolicited ACK → target sends **RST** → confirms host is alive
- Good for bypassing **stateless firewalls** that block SYN packets

---

### 🔍 Ping Sweep Tools

| Tool | Source |
| --- | --- |
| **Angry IP Scanner** | https://angryip.org |
| **SolarWinds Engineer's Toolset** | https://www.solarwinds.com |
| **NetScanTools Pro** | https://www.netscantools.com |
| **Colasoft Ping Tool** | https://www.colasoft.com |
| **Advanced IP Scanner** | https://www.advanced-ip-scanner.com |
| **OpUtils** | https://www.manageengine.com |

---

## 3. Port and Service Discovery

### 🗂️ Port Number Categories

| Category | Range | Description |
| --- | --- | --- |
| **Well-known ports** | 0 – 1023 | Assigned by IANA (FTP, SSH, HTTP, etc.) |
| **Registered ports** | 1024 – 49151 | Registered with IANA for specific uses |
| **Dynamic/Private ports** | 49152 – 65535 | Temporary client-side ports |

---

### 🔑 Critical Ports — Must Know for Exam

| Port | Protocol | Service |
| --- | --- | --- |
| 20 | TCP | FTP Data Transfer |
| 21 | TCP | FTP Command |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP (Email server) |
| 53 | TCP/UDP | DNS |
| 67 | UDP | DHCP Server (BOOTP) |
| 68 | UDP | DHCP Client (BOOTP) |
| 69 | UDP | TFTP |
| 79 | TCP | Finger |
| 80 | TCP/UDP | HTTP |
| 88 | TCP/UDP | Kerberos |
| 110 | TCP | POP3 |
| 111 | TCP/UDP | RPC (sunrpc) |
| 119 | TCP | NNTP |
| 123 | UDP | NTP |
| 135 | TCP/UDP | Microsoft RPC |
| 137 | TCP/UDP | NetBIOS Name Service |
| 138 | TCP/UDP | NetBIOS Datagram Service |
| 139 | TCP/UDP | NetBIOS Session Service |
| 143 | TCP/UDP | IMAP |
| 161 | TCP/UDP | SNMP |
| 162 | TCP/UDP | SNMP Trap |
| 389 | TCP/UDP | LDAP |
| 443 | TCP | HTTPS |
| 445 | TCP/UDP | Microsoft DS (SMB) |
| 500 | UDP | IKE/ISAKMP (VPN) |
| 1080 | TCP/UDP | SOCKS Proxy |
| 1433 | TCP/UDP | Microsoft SQL Server |
| 1434 | TCP/UDP | Microsoft SQL Monitor |
| 1723 | TCP/UDP | PPTP |
| 2049 | TCP/UDP | NFS |
| 3389 | TCP | RDP (Remote Desktop) |
| 5060 | TCP/UDP | SIP (VoIP) |
| 6000-6063 | TCP/UDP | X Window System |
| 6667 | TCP | IRC |

---

### 📊 Port Scanning Techniques Hierarchy

```
TCP Scanning
├── Open TCP Methods
│   └── TCP Connect / Full-Open Scan    -sT
├── Stealth TCP Methods
│   ├── Half-Open / SYN Scan             -sS  ← Most popular
│   ├── Inverse TCP Flag Scans
│   │   ├── Xmas Scan                    -sX  (FIN+URG+PSH)
│   │   ├── FIN Scan                     -sF  (FIN only)
│   │   ├── NULL Scan                    -sN  (No flags)
│   │   └── Maimon Scan                  -sM  (FIN+ACK)
│   └── ACK Flag Probe Scan
│       ├── TTL-based Scan
│       └── Window-based Scan             -sA
└── Third Party / Spoofed
    └── IDLE / IPID Header Scan          -sI <Zombie> <Target>

UDP Scanning                              -sU
SCTP Scanning
├── SCTP INIT Scan                        -sY
└── SCTP COOKIE ECHO Scan                 -sZ
IPv6 Scanning                             -6
```

---

### 📋 Detailed Scan Reference

#### ① TCP Connect / Full-Open Scan — `nmap -sT`

|  |  |
| --- | --- |
| **Method** | Completes full 3-way handshake (SYN → SYN+ACK → ACK) then sends RST |
| **Port Open** | SYN → SYN+ACK → ACK → RST |
| **Port Closed** | SYN → RST |
| **Stealth** | ❌ No — easily **logged** by target |
| **Works on Windows** | ✅ Yes |
| **Privileges needed** | No root/admin required |
| **Notes** | Most **reliable** but most **detectable**. Uses OS `connect()` system call. |

---

#### ② SYN / Stealth / Half-Open Scan — `nmap -sS`

|  |  |
| --- | --- |
| **Method** | Sends SYN → if SYN+ACK received, sends RST (aborts before completing handshake) |
| **Port Open** | SYN → SYN+ACK → RST (connection left half-open) |
| **Port Closed** | SYN → RST |
| **Stealth** | ✅ Yes — **NOT logged** by most applications |
| **Works on Windows** | ✅ Yes |
| **Privileges needed** | Root/admin required (raw sockets) |
| **Notes** | **Most popular scan**. Bypasses firewall rules and logging mechanisms. |

---

#### ③ Xmas Scan — `nmap -sX`

|  |  |
| --- | --- |
| **Flags set** | **FIN + URG + PSH** (all 3 simultaneously — "nonsense" pattern URG-PSH-FIN) |
| **Port Open** | **No response** |
| **Port Closed** | **RST packet** |
| **Stealth** | ✅ Yes |
| **Works on Windows** | ❌ No — shows all ports as open |
| **Notes** | Works only on **RFC 793-compliant** systems. BSD networking code only (UNIX). Not effective on Windows NT. |

---

#### ④ FIN Scan — `nmap -sF`

|  |  |
| --- | --- |
| **Flags set** | **FIN only** |
| **Port Open** | **No response** |
| **Port Closed** | **RST packet** |
| **Stealth** | ✅ Yes |
| **Works on Windows** | ❌ No |
| **Notes** | More stealthy than Xmas — single flag. Same BSD/RFC 793 limitation. |

---

#### ⑤ NULL Scan — `nmap -sN`

|  |  |
| --- | --- |
| **Flags set** | **No flags** (empty TCP packet) |
| **Port Open** | **No response** |
| **Port Closed** | **RST packet** |
| **Stealth** | ✅ Most covert TCP scan |
| **Works on Windows** | ❌ No |
| **Notes** | Most stealthy inverse scan. Same BSD/RFC 793 limitation. |

---

#### ⑥ Maimon Scan — `nmap -sM`

|  |  |
| --- | --- |
| **Flags set** | **FIN + ACK** |
| **Port Open** | All ports in **ignored states** |
| **Port Closed** | **RST packet** |
| **Stealth** | ✅ Yes |
| **Works on Windows** | ❌ No |
| **Notes** | BSD-based OS port discovery technique. |

> ⚠️ **Critical Rule for Xmas, FIN, NULL, Maimon:**
> 
> - Effective **ONLY** on RFC 793-compliant TCP/IP stacks
> - **Do NOT work on any current version of Microsoft Windows**
> - Require raw socket access and **super-user privileges**

---

#### ⑦ ACK Flag Probe Scan — `nmap -sA`

|  |  |
| --- | --- |
| **Method** | Sends ACK probe packets → analyzes **TTL and WINDOW** fields of RST responses |
| **Purpose** | Determines if ports are **filtered or unfiltered** (NOT open/closed) |
| **Subtypes** | TTL-based: TTL < 64 = unfiltered; Window-based: non-zero window = unfiltered |
| **Works on** | BSD-derived TCP/IP stacks only |
| **Notes** | Exploits BSD TCP/IP stack vulnerabilities. Effective for **firewall rule mapping**. |

---

#### ⑧ IDLE / IPID Header Scan — `nmap -sI <Zombie> <Target>`

|  |  |
| --- | --- |
| **Method** | Scans using a third-party "zombie" host — never sends packets from own IP |
| **Port Open** | Zombie's IPID increases by **2** between probes |
| **Port Closed** | Zombie's IPID increases by **1** between probes |
| **Stealth** | ✅✅ **Most stealthy** — attacker's IP completely hidden |
| **Works on Windows** | ✅ Yes (zombie can be any OS) |
| **Notes** | Zombie must have **incrementally assigned IPIDs** on a global basis. Shorter zombie-target distance = faster scan. |

**How IDLE scan works:**

```
Step 1: Probe zombie's IPID → note value (e.g., IPID=100)
Step 2: Send SYN to target spoofed as zombie IP
Step 3: If port OPEN  → target sends SYN+ACK to zombie → zombie sends RST → IPID+2
        If port CLOSED → target sends RST to zombie → zombie ignores → IPID+1
Step 4: Probe zombie's IPID again → compare
```

---

#### ⑨ UDP Scanning — `nmap -sU`

|  |  |
| --- | --- |
| **Method** | Sends UDP packets to target ports |
| **Port Open** | **No response** (or occasional UDP reply) |
| **Port Closed** | ICMP **"Port Unreachable"** message |
| **Speed** | Slower than TCP scanning |
| **Notes** | Default probe port: **40,125**. Useful for DNS (53), DHCP (67/68), SNMP (161). |

---

#### ⑩ SCTP INIT Scan — `nmap -sY`

|  |  |
| --- | --- |
| **Method** | Sends SCTP INIT chunk |
| **Port Open** | INIT+ACK chunk |
| **Port Closed** | ABORT chunk |
| **Notes** | Can clearly differentiate **open, closed, AND filtered** states. SCTP = Stream Control Transmission Protocol. |

#### ⑪ SCTP COOKIE ECHO Scan — `nmap -sZ`

|  |  |
| --- | --- |
| **Method** | Sends SCTP COOKIE ECHO chunk |
| **Port Open** | Silently **drops packets** (no response) |
| **Port Closed** | **ABORT chunk** |
| **Notes** | Not blocked by non-stateful firewalls. Only advanced IDS can detect it. More advanced than INIT scan. |

#### ⑫ IPv6 Scanning — `nmap -6`

```bash
nmap -6 scanme.nmap.org
```

Returns open ports using IPv6 addresses. Same scan types apply as IPv4.

---

### 🔍 Service Version Detection

```bash
nmap -sV 10.10.1.11                        # Detect service name + version
nmap -sV --reason -v -sT 10.10.1.11       # Full detail with reason per port
nmap -sV -sC 10.10.1.11                    # + Default NSE scripts
nmap -A 10.10.1.11                         # Aggressive: OS + version + scripts + traceroute
```

- Reveals exact service software and version (e.g., "Microsoft IIS httpd 10.0")
- Also reveals OS, hostname, workgroup, domain from service banners
- Key flag: `sV` enables version detection

---

### ⏱️ Nmap Scan Time Reduction

| Technique | Details |
| --- | --- |
| **Omit non-critical tests** | Avoid `-sC, -sV, -O, -A, --traceroute` if only a quick scan needed |
| **Limit port range** | `--top-ports 100` or `-p 22,80,443` instead of all 65535 |
| **Use timing templates** | `-T0` (paranoid) to `-T5` (insane); `-T4` = recommended for speed |
| **Skip DNS resolution** | `-n` disables DNS; saves time per host |
| **Scan in parallel** | Nmap does this automatically; adjust with `--min-parallelism` |
| **Skip port scan** | `-sn` if you only need host discovery |

---

## 4. OS Discovery / Banner Grabbing

### 🖥️ What is Banner Grabbing?

> **Banner Grabbing (OS Fingerprinting)** = method to determine the **OS running on a remote target** by analyzing responses to crafted packets. Knowing the target OS allows an attacker to identify OS-specific vulnerabilities and formulate an attack strategy.
> 

---

### 📊 Active vs Passive Banner Grabbing

|  | **Active Banner Grabbing** | **Passive Banner Grabbing** |
| --- | --- | --- |
| **Method** | Send specially crafted packets → compare responses to OS signature database | Capture and analyze existing traffic without interaction |
| **Detection risk** | High — sends traffic to target | Low — no traffic sent to target |
| **Techniques** | Malformed TCP packets, ISN analysis, ICMP response analysis | Error messages, traffic sniffing, page extension analysis |

**Passive Banner Grabbing techniques:**

- **Banner grabbing from error messages** — Error messages reveal server type, OS, SSL tools used
- **Sniffing network traffic** — Capture packets → analyze TTL and Window Size
- **Banner grabbing from page extensions** — `.aspx` → IIS/Windows; `.php` → Apache/Linux; `.jsp` → Java-based

---

### 📋 OS Identification via TTL and TCP Window Size

| **OS** | **TTL Value** | **TCP Window Size** |
| --- | --- | --- |
| **Linux** (Kernel 2.4+) | **64** | 5840 |
| **FreeBSD** | **64** | 65535 |
| **OpenBSD** | **255** | 16384 |
| **Windows** (NT/2000/XP/7/10/11) | **128** | 65535 or 8192 |
| **Cisco Router (IOS)** | **255** | 4128 |
| **Solaris** | **255** | 8760 |
| **AIX** | **255** | 16384 |

**The 4 criteria used for passive OS fingerprinting:**

1. **TTL** — what TTL does the OS set on outbound packets?
2. **Window Size** — what TCP window size does the OS use?
3. **DF bit** — does the OS set the Don't Fragment bit?
4. **ToS** — does the OS set the Type of Service field, and how?

---

### 🛠️ OS Discovery Tools and Commands

**Nmap OS Detection:**

```bash
nmap -O 10.10.1.11                           # OS detection
nmap -O --osscan-guess 10.10.1.11            # Aggressive OS guess
nmap -A 10.10.1.11                           # OS + version + scripts + traceroute
nmap --script smb-os-discovery.nse 10.10.1.22  # SMB OS discovery script
```

**NSE (Nmap Script Engine) smb-os-discovery output reveals:**

- OS name (e.g., "Windows Server 2022 Standard")
- Computer name, Domain, NetBIOS name
- Forest name, FQDN, System time

**Wireshark OS Detection:**

- Capture traffic → filter for target → inspect **TTL field** in IP header
- TTL=128 → **Windows** | TTL=64 → **Linux/FreeBSD**
- Tool shows: "Possible OS is Windows" based on TTL value

**Unicornscan** — Asynchronous OS detection and scanning

**IPv6 Fingerprinting with Nmap:**

- Uses ~18 probes in order: Sequence generation (S1–S6), ICMPv6 echo (IE1, IE2), Node Information Query (NI)
- Same functionality as IPv4 fingerprinting

---

## 5. Scanning Beyond IDS and Firewall

> Techniques to **evade IDS/IPS and firewall detection** during scanning — make traffic look legitimate or untraceable.
> 

### 🛡️ Complete IDS/Firewall Evasion Techniques

#### 1. Packet Fragmentation

```bash
nmap -f target              # Fragment into 8-byte fragments
nmap -ff target             # Fragment into 16-byte fragments
nmap --mtu 24 target        # Custom MTU (must be multiple of 8)
```

- Splits TCP headers into **small fragments** to confuse packet filters
- IDS using signature-based detection **misses signatures** split across fragments
- SYN/FIN scanning method combined with IP fragmentation is very effective
- ⚠️ Some hosts may crash when receiving fragmented packets

#### 2. Source Routing

- Specifies the **packet path** through the network to bypass certain routers/firewalls
- Router follows source route even if it would normally drop the packet
- Two types: Strict (full path) and Loose (partial path)

#### 3. Source Port Manipulation

```bash
nmap --source-port 53 target    # Use DNS port as source
nmap -g 80 target               # Use HTTP port as source
nmap -g 443 target              # Use HTTPS port as source
```

- Many firewalls **allow traffic from ports 53, 80, 443** (trusted services)
- Using these as source ports can **bypass firewall filter rules**

#### 4. IP Address Decoy

```bash
nmap -D RND:10 target                        # 10 random decoy IPs
nmap -D 192.168.1.1,ME,192.168.1.3 target   # Specific decoys + your real IP
nmap -D decoy1,decoy2,decoy3 target          # Manual decoy specification
```

- Generates **multiple fake source IPs** alongside real IP
- Target sees scan from many IPs → cannot identify the real attacker
- IDS/firewall logs show 5–10+ scanning sources → real IP hidden among decoys

#### 5. IP Address Spoofing

```bash
nmap -S spoofed_IP target     # Spoof source IP address
nmap -S 192.168.1.100 target  # Appear to scan from a different host
```

- Changes source IP to **another host's address** → hard to trace
- ⚠️ Attacker won't receive responses (sent to spoofed IP's host)
- **Detection:** Monitor IPID numbers — if IPID in response ≠ close to probe's IPID, packet is spoofed

#### 6. MAC Address Spoofing

```bash
nmap --spoof-mac 0            # Random MAC
nmap --spoof-mac 00:01:02:03  # Specific MAC
nmap --spoof-mac Dell         # Vendor-based MAC
```

- Changes source MAC to bypass **MAC-based access control** lists

#### 7. Creating Custom Packets

- **Colasoft Packet Builder** (https://www.colasoft.com) — select templates, modify parameters in decoder/hex/ASCII
- **NetScanTools Pro** — advanced packet crafting
- Create crafted packets to bypass specific firewall rules or IDS signatures

#### 8. Randomizing Host Order

```bash
nmap --randomize-hosts target_range
```

- Scans hosts in **random order** instead of sequential → avoids detection patterns

#### 9. Sending Bad Checksums

```bash
nmap --badsum target
```

- Sends packets with **invalid TCP/UDP checksums**
- Some firewalls/IDS ignore these → allows stealthy probing
- Legitimate systems reject bad checksums; output shows **all ports as filtered**

#### 10. Proxy Servers

- Route scanning traffic through **proxy chains** → obscures source
- **Proxychains** — chain multiple SOCKS proxies
- **CyberGhost VPN** — hides IP, encrypts connection, no logs
- **Additional tools:** Burp Suite, Tor, Hotspot Shield, Proxifier, IPRoyal Residential Proxy

#### 11. Anonymizers

> An anonymizer is an intermediate server placed between attacker and target that accesses the target on their behalf.
> 
- Eliminates **identifying information (IP)** from the system while surfing
- Encrypts data transferred from computer to ISP
- Can anonymize: HTTP, FTP, gopher Internet services
- **Tor Network** — routes through multiple encrypted relays globally
- **Tools:** Tor Browser, Hotspot Shield, BrowserSpy

---

## 6. Network Scanning Countermeasures

### 🔒 Ping Sweep Countermeasures

- **Block incoming ICMP echo requests** from unknown/untrusted sources at firewall
- Use **IDS/IPS** (e.g., Snort) to detect and prevent ping sweep attempts
- Evaluate type of ICMP traffic flowing through enterprise networks
- **Terminate connection** with any host sending more than **10 ICMP ECHO requests**
- Use **DMZ** and allow only `ICMP_ECHO_REPLY`, `HOST_UNREACHABLE`, and `TIME_EXCEEDED`
- **Limit ICMP traffic** with ACLs to ISP's specific IP addresses
- Implement **rate limiting** for ICMP packets
- **Break network into smaller segments** (segmentation/microsegmentation)
- Use **private IP ranges + NAT** to hide internal addresses from external observers

---

### 🔒 Port Scan Countermeasures

- **Keep as few ports open as possible** — filter these at firewall: 135–159, 256–258, 389, 445, 1080, 1745, 3268
- **Block unwanted services** running on ports and update service versions
- Ensure service versions are **non-vulnerable**
- **Block inbound ICMP** message types and outbound ICMP type-3 unreachable messages
- Ensure firewall and router can **block source-routing** techniques
- Configure routing/filtering mechanisms to **prevent bypass via source port or source routing**
- Regularly test your own IP space using TCP, UDP, and ICMP probes
- Ensure **anti-scanning and anti-spoofing rules** are configured on firewalls
- Ensure commercial firewalls are **patched** with latest updates and have anti-spoofing rules
- Use **TCP wrappers** to limit access based on domain names or IP addresses
- Use **proxy servers** to block fragmented or malformed packets
- Forward open port scans to **honeypots** to make scanning difficult
- Employ **IPS** to identify and blacklist scanning IP addresses
- Implement **port knocking** to hide open ports
- Use **NAT** to hide internal IP addresses
- Implement **egress filtering** to identify malicious internal hosts scanning external targets
- Use **VLANs** to isolate different traffic types and restrict access between them
- Implement **dynamic IPv6 address variation** using random address generators

---

### 🔒 IP Spoofing Countermeasures

- **Encrypt all network traffic** using IPsec, TLS, SSH, HTTPS
- Use **multiple firewalls** for multi-layered defense-in-depth
- **Do NOT rely on IP-based authentication** alone — add password authentication
- Use a **random initial sequence number** to prevent sequence-number-based spoofing
- **Ingress filtering** — filter incoming packets that appear to come from internal IP addresses (at network perimeter)
- **Egress filtering** — filter outgoing packets with invalid local IP as source
- Configure **internal switches to filter DHCP static addresses** for malicious spoofed traffic
- Use **secure communication protocols** (HTTPS, SFTP, SSH) with encryption and authentication
- Configure routers to **hide intranet hosts** using NAT modifications
- Monitor **IPID numbers** to detect spoofed packets (spoofed = IPID very different from probe IPID)

---

### 🔒 Scanning Detection and Prevention Tools

- **ExtraHop** (https://www.extrahop.com) — Real-time detection, auto-discovers all devices including IoT, analyzes all network interactions and SSL/TLS encrypted traffic
- **Splunk Enterprise Security** — SIEM for detecting scanning patterns and anomalies

---

## 7. Quick Exam Cheat Sheet

### 🚩 TCP Flags — Memory Aid

```
SYN = Synchronize   → Starts connection
ACK = Acknowledge   → Confirms receipt
PSH = Push          → Send buffered data now
URG = Urgent        → Process ASAP
FIN = Finish        → End connection
RST = Reset         → Abort on error
```

---

### 🔍 All Scan Types at a Glance

| Scan | Nmap Flag | Flags Sent | Open Response | Closed Response | Stealthy | Windows? |
| --- | --- | --- | --- | --- | --- | --- |
| TCP Connect | `-sT` | SYN | SYN+ACK → ACK → RST | RST | ❌ | ✅ |
| SYN/Half-Open | `-sS` | SYN | SYN+ACK → RST | RST | ✅ | ✅ |
| Xmas | `-sX` | FIN+URG+PSH | No response | RST | ✅ | ❌ |
| FIN | `-sF` | FIN | No response | RST | ✅ | ❌ |
| NULL | `-sN` | None | No response | RST | ✅✅ | ❌ |
| Maimon | `-sM` | FIN+ACK | Ignored states | RST | ✅ | ❌ |
| ACK Probe | `-sA` | ACK | RST (filtered check) | RST | ⚠️ | ❌ |
| IDLE | `-sI zombie` | SYN (spoofed) | IPID+2 on zombie | IPID+1 on zombie | ✅✅ | ✅ |
| UDP | `-sU` | UDP | No response | ICMP Port Unreachable | ⚠️ | ✅ |
| SCTP INIT | `-sY` | INIT chunk | INIT+ACK | ABORT | ✅ | — |
| SCTP COOKIE | `-sZ` | COOKIE ECHO | Silently dropped | ABORT | ✅✅ | — |
| IPv6 | `-6` | (same as above) | same | same | — | ✅ |

---

### 🖥️ OS → TTL Quick Reference

```
Windows  → TTL = 128
Linux    → TTL = 64
FreeBSD  → TTL = 64
OpenBSD  → TTL = 255
Cisco    → TTL = 255
Solaris  → TTL = 255
AIX      → TTL = 255
```

---

### 🛡️ IDS Evasion — Nmap Commands Quick Reference

```bash
nmap -f target                    # Packet fragmentation
nmap --mtu 24 target              # Custom MTU fragmentation
nmap --source-port 53 target      # Source port manipulation
nmap -g 80 target                 # Source port manipulation (alt)
nmap -D RND:10 target             # IP Decoy (10 random)
nmap -D d1,d2,ME target           # IP Decoy (specific)
nmap -S spoofed_IP target         # IP Address Spoofing
nmap --spoof-mac 0 target         # MAC Address Spoofing
nmap --randomize-hosts target     # Randomize host order
nmap --badsum target              # Send bad checksums
nmap -T0 target                   # Slowest timing (paranoid)
nmap -T5 target                   # Fastest timing (insane)
```

---

### 🎯 Host Discovery Commands

```bash
nmap -sn -PR target    # ARP ping (LAN best)
nmap -sn -PU target    # UDP ping (port 40125)
nmap -sn -PE target    # ICMP ECHO ping
nmap -sn -PE range/24  # ICMP ECHO ping sweep
nmap -sn -PP target    # ICMP Timestamp ping
nmap -sn -PM target    # ICMP Address Mask ping
nmap -sn -PS target    # TCP SYN ping (port 80)
nmap -sn -PA target    # TCP ACK ping (port 80)
nmap -sn -PO target    # IP Protocol ping
```

---

### 🔥 Common Exam Scenarios

**Q: Which scan completes the full 3-way handshake?**
→ **TCP Connect scan** (`-sT`)

**Q: Most popular/stealthy TCP scan?**
→ **SYN/Half-Open scan** (`-sS`)

**Q: Which scan sets FIN+URG+PSH flags?**
→ **Xmas scan** (`-sX`)

**Q: Which scan sends NO flags at all?**
→ **NULL scan** (`-sN`)

**Q: Which scan is anonymous (uses a zombie)?**
→ **IDLE/IPID scan** (`-sI`)

**Q: Which scans DON'T work on Windows?**
→ **Xmas, FIN, NULL, Maimon** (not RFC 793 compliant on Windows)

**Q: ACK scan determines what?**
→ If ports are **filtered or unfiltered** — NOT open or closed

**Q: Default UDP probe port in Nmap?**
→ **40,125**

**Q: How to detect OS passively from captured packets?**
→ Check **TTL** and **TCP Window Size** values

**Q: TTL=128 → which OS?**
→ **Windows**

**Q: TTL=64 → which OS?**
→ **Linux** or **FreeBSD**

**Q: How to bypass firewall using a trusted port?**
→ **Source Port Manipulation** (`--source-port 53` or `-g 80`)

**Q: What does `-D RND:10` do?**
→ **IP Address Decoy** — generates 10 random decoy IPs to hide real attacker

**Q: Nmap option for OS detection?**
→ **`-O`** | Aggressive (OS + version + scripts): **`-A`**

**Q: Nmap option for service version detection?**
→ **`-sV`**

**Q: What NSE script discovers OS via SMB?**
→ **`smb-os-discovery.nse`**

**Q: SCTP scan that differentiates open/closed/filtered?**
→ **SCTP INIT scan** (`-sY`)

**Q: Which scan is harder to detect — SCTP INIT or COOKIE ECHO?**
→ **SCTP COOKIE ECHO** (`-sZ`) — not blocked by non-stateful firewalls

**Q: Best IDS evasion technique to completely hide attacker identity?**
→ **IDLE/IPID scan** with a zombie host

**Q: How to detect IP spoofing?**
→ Monitor **IPID numbers** — spoofed if response IPID is far from probe IPID value; also use **TCP flow control** (set SYN-ACK to zero)

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 03*
