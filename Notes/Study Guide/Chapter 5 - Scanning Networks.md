# Chapter 5 - Scanning Networks

Scanning **requires the data from the footprinting and reconnaissance step**. Without it you have no idea what to scan.

**The progression:**

1. **Port scan** → identify open ports
2. Open ports are the **starting point** for identifying the services and applications listening on them
3. From service + version → **dig into potential vulnerabilities**

**Vulnerability scanners** save time: a port scan gives you a lot, but knowing the application name and even its version means you'd otherwise have to research vulnerabilities by hand. A scanner automates that. But they're **not infallible** — they can **miss** real vulnerabilities (false negatives) and **report ones that don't exist** (false positives). Results always need manual validation.

**Obstacles — firewalls and IDS/IPS:**

- They can **inhibit or deter** attempts to reach into the network to gather information
- Your scan attempts may be **detected**, alerting security and operations staff to what you're doing

**Packet crafting** — building packets manually to **bypass the operating system's** normal mechanisms for assembling network data structures. This can let you reach an endpoint in ways a normal connection wouldn't, and evade some filtering.

**MITRE ATT&CK** classifies this as **Active Scanning** (T1595), with two sub-techniques:

- **Scanning IP Blocks**
- **Vulnerability Scanning**

# Ping Sweeps

A **ping sweep** sends ping messages to **every system on a network** to find out which hosts are alive. Ping = **ICMP echo request** (type 8) → **echo reply** (type 0).

**Two caveats:**

- Unless you send a *lot* of messages, a sweep is fairly quiet — but volume gets noticed
- Not guaranteed to work: **firewall rules may block ICMP** from outside the network, so a silent host isn't necessarily down

## Using fping

Tool designed to send ICMP echo requests to **multiple systems** at once.

Key flags (`-aeg`):

- **`a`** → show only hosts that are **alive**
- **`e`** → show **elapsed time** (round-trip)
- **`g`** → **generate** the target list from an address block

**Unreachable** = the host is down *or* something is blocking the message (firewall drop, host unreachable). fping sends **several messages before giving up** and declaring a host down.

```
fping -aeg 192.168.86.0/24
```

## Using MegaPing

**GUI tool for Windows** that can perform ping sweeps, via its **IP scanner** tool.

- By default not all information is shown (MAC addresses, hostnames…), but ticking the right boxes adds it at the **end of the scan**
- On the left it also offers **network troubleshooting** tools and a **port scanning** tool

**Key point:** knowing a host is **up** is useful but not enough — next you need to know **what services are running** on it (that's port scanning).

---

# Cheatsheet — Tools

| Tool | Purpose | Command / Notes |
| --- | --- | --- |
| **ping** | Check a single host is alive (ICMP echo) | `ping <host>` |
| **fping** | Ping sweep across an address block | `fping -aeg 192.168.86.0/24` (`-a` alive, `-e` elapsed, `-g` generate list) |
| **MegaPing** | Windows GUI — ping sweep + IP scanner, port scan, troubleshooting | GUI (tick boxes to add MAC/hostname) |
| **nmap** (alt.) | Ping sweep / host discovery, no port scan | `nmap -sn 192.168.86.0/24` |

# Port Scanning

A **port** is a construct within the OS network stack at the **transport layer (layer 4)**. When an application has network service functionality, it **binds** to a port — reserving it and registering to receive messages arriving on it.

A port with an application **listening** on it is considered **open**.

**Objective of port scanning:** identify the software bound to the ports found open.

## How the protocols respond

**TCP** (3-way handshake, SYN/ACK flags):

- **Open** → responds to a SYN with a **SYN-ACK**
- **Closed** → responds to a SYN with a **RST**

**UDP** — it's up to the server to respond. With no reply, it's hard to tell whether the port is closed, the application didn't receive the message, or the service just doesn't answer → messages are resent with a short delay between them → UDP scans take **longer** than TCP.

Usually an **ICMP request is sent before** the port scan (host discovery first).

## Nmap

Short for **Network Mapper**. Can do UDP scans and multiple TCP scan types, plus:

- **OS detection**, application and **version** detection
- **Scripting** support (NSE, written in **Lua**) to extend functionality
- Needs **administrative privileges** (`sudo`)
- GUI version = **Zenmap**

### TCP Scanning

The port number is **2 bytes** → **65,536 ports** possible (0–65535). Nmap scans only the **1000 most common** ports by default (scanning all is too slow); you can specify any port(s).

| Scan | Flag | How it works | Result reading |
| --- | --- | --- | --- |
| **SYN scan** (half-open) | `-sS` | Sends SYN; open → SYN-ACK, nmap replies **RST** (never completes). Closed → RST. Stealthier, default when root | open / closed |
| **Full connect** | `-sT` | Completes the full handshake (ACK) then tears it down. Used when you lack raw-socket privileges | open / closed |
| **Xmas** | `-sX` | Sets **URG + PSH + FIN**. Closed → RST; no reply → open **or filtered** (can't tell a drop from a non-response to an illegal packet) | open|filtered / closed |
| **Null** | `-sN` | **No flags** set. Same logic as Xmas | open|filtered / closed |
| **FIN** | `-sF` | Only **FIN** set — unexpected outside an established connection. Same logic | open|filtered / closed |

*Xmas/Null/FIN exploit unexpected input to provoke a distinguishing response, and can slip past simple stateless filters — but they can't confirm "open" on their own, only rule out "closed."*

```
sudo nmap -sS 192.168.12.3
sudo nmap -sT -p 80,443 192.168.1.0/24
sudo nmap -sX -p- 192.168.2.35
```

### UDP Scanning

No scan-type variants — nmap just sends UDP messages and watches for replies:

- **Closed** → **ICMP port unreachable**
- **Open** → the service may respond, or **no response at all**
- `T` = **throttle / timing** (0–5): default **3**, **5** faster, **1** slower for less detection.

```
sudo nmap -sU -T 4 192.168.12.3
```

## Detailed Information

**Version scan** (`-sV`) — connects to the port, issues the correct protocol commands, and grabs the **application banner** (software name and version). Nmap must understand how to speak each protocol.

```
sudo nmap -sV 192.168.1.62
```

**OS scan** (`-O`) — uses a **fingerprint database** describing how each OS behaves (IP ID generation, initial sequence number, initial window size, etc.). Needs at least **one open and one closed port**.

```
sudo nmap -O 192.168.1.23 192.168.1.24
```

Limitations: hard to pin an exact version — e.g. Linux distros share the same kernel, so nmap can't tell **CentOS from Ubuntu**, only the kernel version; and it can't identify an OS newer than its database.

## Scripting (NSE)

A scripting engine to extend nmap in any way — pick from hundreds of scripts, grouped in categories: **auth, broadcast, brute, default, discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, vuln**. You can run every script in a category.

- Linux scripts: `/usr/share/nmap/scripts`
- Windows: the Program Files nmap install directory
- Files end in **`.nse`** — plain text, readable, and a base for writing your own

```
sudo nmap -sS --script=discovery 192.168.23.0/24
sudo nmap --script-help=http-waf-detect.nse
sudo nmap -sS --script "smbv2*" -T 4 192.168.2.3
```

## Zenmap

GUI that runs nmap commands. Uses its own **profile names** for scans (mapped to timing):

- **Intense scan** — throttle higher (faster)
- **Regular scan** — default throttle
- **Slow comprehensive scan** — turns the throttle up (thorough)

You can always drop to the command line for anything else. Advantages: **visualised output** (hosts view / services view), and **saved scans** (XML) that can be **compared** against each other.

## Masscan

Scans as **fast as your system and network** allow.

- Same command-line parameters as nmap, **except** it uses a **`-rate`** parameter (packets per second)
- **`-randomize-hosts`** — tests IPs out of numerical order to evade network monitoring
- Can grab banners with **`-banners`**
- Automatically uses a SYN (`sS`) scan
- Weaker than nmap: **no Xmas, FIN, ACK or UDP** scan types

```
sudo masscan --rate=100000 -p80,443 192.168.1.0/24
sudo masscan -sS --banners --rate=100000 -p80,443 192.168.2.0/24
```

## MegaPing

Port scanner with **preselected port collections**:

- **Hostile ports** — commonly misused / used by trojans and malware
- **Authorized ports** — the range where applications are expected

Doesn't accept a **CIDR block or address range** — multiple addresses must be entered individually.

## Metasploit

Primarily an **exploit framework**, but has thousands of modules, not only for exploitation.

```
msfconsole
search portscan
use <module path>        # e.g. auxiliary/scanner/portscan/tcp
set RHOSTS 192.168.1.0/24
run
```

Advantages: **stores results in a database** for later use, and you can run **nmap inside msfconsole** to keep everything tracked.

---

# Cheatsheet — Tools & Commands

**Nmap scan types**

| Flag | Scan |
| --- | --- |
| `-sS` | SYN / half-open (default, stealthy) |
| `-sT` | Full connect |
| `-sX` | Xmas (URG+PSH+FIN) |
| `-sN` | Null (no flags) |
| `-sF` | FIN |
| `-sU` | UDP |

**Nmap detection & options**

| Flag | Purpose |
| --- | --- |
| `-sV` | Version / banner detection |
| `-O` | OS detection (needs 1 open + 1 closed port) |
| `-p 80,443` | Specific ports |
| `-T 0–5` | Timing (3 default, 5 fast, 1 stealth) |
| `--script=<cat>` | Run NSE scripts |
| `--script-help=<x>.nse` | Script documentation |
| `-Pn` | bypass firewall, ignore ping |
| `-oA` | save an outpout with 3 formats |
| `-oG` | save an outpout in grep fromat |
| `-oN` | save in normal format |
| `-oX` | save in XML format |

**Commands**

```
sudo nmap -sS 192.168.12.3
sudo nmap -sT -p 80,443 192.168.1.0/24
sudo nmap -sU -T 4 192.168.12.3
sudo nmap -sV 192.168.1.62
sudo nmap -O 192.168.1.23
sudo nmap -sS --script=discovery 192.168.23.0/24

sudo masscan --rate=100000 -p80,443 192.168.1.0/24
sudo masscan -sS --banners --rate=100000 -p80,443 192.168.2.0/24

msfconsole → search portscan → use <module> → set RHOSTS <target> → run
```

**Response logic**

|  | TCP | UDP |
| --- | --- | --- |
| Open | SYN-ACK | service reply or silence |
| Closed | RST | ICMP port unreachable |

**Other:** Zenmap = nmap GUI (visualise + compare XML scans) · Masscan = fastest, SYN only, no Xmas/FIN/UDP · MegaPing = preset hostile/authorized ports, no CIDR · Metasploit = `search portscan`, stores results in DB.

# Vulnerability Scanning

Throwing lots of exploits at a target is a **bad approach**: blind testing can cause **failures** on the target, leads to unexpected results, and is **noisy** — you'd rather not be detected.

**Better idea — a vulnerability scanner:** it identifies open ports and listening applications, determines what vulnerabilities may be possible based on those applications, then runs tests for them.

**Key point:** the objective of a scanner is **not to compromise** a system — only to **identify potential vulnerabilities**.

## The four categories of result

|  | Scanner flags it | Scanner doesn't flag it |
| --- | --- | --- |
| **Vulnerability really exists** | **True positive** (correct find) | **False negative** (missed it — most dangerous) |
| **No vulnerability** | **False positive** (wrong alarm — wastes time) | **True negative** (correctly silent) |

## History

- **1990s:** first scanners — **SATAN** (open source), **SARA**, **SAINT**
- **2000s:** **Nessus**

## OpenVAS

**Open Vulnerability Assessment System** — started as the same code as Nessus (when Nessus went commercial), then built its own architecture.

- **Greenbone Security Assistant (GSA)** = the **web interface** front end to OpenVAS
- **Multi-user** with **permissions** — some users can create scans, some only view results, some administer the install and create users
- Supports **roles** — permissions within a role can be changed, and new roles created
- Install creates an **admin user with a random password**

### Setting up targets

- Need a target or set of targets → **IP addresses** (can also **exclude** addresses)
- Can set the **scope of ports** to scan
- With **credentials** (**grey box**), you get **local** vulnerabilities, not just network/remote ones. Credentials = username+password or SSH key. But only **one credential per protocol** applies → assumes the **same password on every system** → works only with a **centralised account**

### Scan configs

- A **scan config** defines which **plugins** are tested against the target — **8 configs** predefined
- Tests are **Network Vulnerability Tests (NVTs)**, organised into **families**
- A config can be **static** or **dynamic** (dynamic = new NVTs added automatically as they're released)
- You can select which NVTs within a family to run

### Scan tasks

- Create scans, and create **alerts** based on **severity or filters** → sent by **email, HTTP request, SMB, SNMP**, etc.
- Can create a **schedule**, but by **default there's none**
- A scan **doesn't run until you start it**

### Scan results

- Monitor results **while the scan runs**, review completed scans, review historic vulnerabilities
- **Dashboard** — charts showing vulnerabilities **over time** → sense of whether the security posture is improving (but you don't see the vulnerability detail here)
- **Results page** — full list of results across scans
- **QoD (Quality of Detection)** — how confident OpenVAS is that a finding is a **true positive**
- You can **change severity** on a finding depending on context (raise or lower it) and **add notes** → makes it a **vulnerability management tool**

## Nessus

Commercial product, with a free **home license**.

- Create **SSH and Windows credentials** for host authentication, with **multiple configurations** each — **unlimited** SSH and Windows credentials
- Also **database**, **miscellaneous** (Palo Alto firewall, VMware settings) and **plaintext** authentication
- Configure which **plugins** to run

**Tabs within a policy** (all part of the **Basic Network Scan**):

- **Discovery** — which type of discovery; a **port scan of common ports is set by default**
- **Assessment** — parameters for what to scan re: **web vulnerabilities**; **web vulns are off by default**
- **Report** — adjust report **verbosity**
- **Advanced scan** — access to and control over **all** settings

**Results** — hosts listed by **total number of issues** (includes informational items that aren't vulnerabilities). Three tabs:

- **List of hosts** — ordered by total number of vulnerabilities
- **Vulnerabilities** — ordered by **severity** of the finding
- **History**

**vs OpenVAS:** you **can't add notes** to a finding like in OpenVAS, but you get an **audit trail** that OpenVAS doesn't provide (a record of what was done and when).

**Scripts:** Nessus uses **NASL** (Nessus Attack Scripting Language):

- Windows: the Program Files directory
- Linux: `/opt/nessus`, plugins in `/opt/nessus/lib/plugins`

## Verifying findings

**Always verify a vulnerability before presenting it as a finding** — confirm it by exploiting it with a purpose-built tool or **Metasploit**. This is how you eliminate false positives.

## Looking for vulnerabilities with Metasploit

Versatile tool — a **framework** plus many modules, including **scanner modules** (not just exploits). Useful both to **find** and to **confirm** vulnerabilities.

---

# Cheatsheet

**Result types**

| Term | Meaning |
| --- | --- |
| True positive | Flagged, real |
| False positive | Flagged, not real |
| True negative | Not flagged, nothing there |
| False negative | Not flagged, but real — worst case |

**Scanners**

| Tool | Notes |
| --- | --- |
| **OpenVAS** | Open source. Web UI = **GSA**. Tests = **NVTs** in families. **QoD** = detection confidence. Notes + severity editing → vuln management. Random admin password on install |
| **Nessus** | Commercial (home license). Scripts = **NASL**. Web vulns **off by default**. Unlimited SSH/Windows creds. Audit trail, no notes |
| **SATAN / SARA / SAINT** | 1990s originals |
| **Metasploit** | Framework — scanner modules to find + verify |

**Key terms:** NVT (OpenVAS test) · NASL (Nessus script language) · QoD (OpenVAS detection confidence) · GSA (OpenVAS web UI) · grey box = credentialed scan → finds local vulns · always verify before reporting.

# Packet Crafting and Manipulation

## Why craft packets at all

Normally you never build packets yourself. When you type a URL:

1. The **browser** builds the HTTP request headers to send to the server
2. The **application** asks the **operating system** to open a connection to the server
3. The OS builds the packet from what the app gave it — hostname/IP and port number (default **80** for HTTP, **443** for HTTPS)
4. The OS creates the **TCP and IP headers** (layers 4 and 3) itself

So the OS's network stack normally decides how every packet looks, and it always builds **correct, well-formed** packets. That's the limitation: sometimes you need to send something the OS would never produce — a packet with an **illegal flag combination**, a **spoofed source address**, an **oversized payload**, or one that follows an unusual path. This is "sending data from an incoherent path."

**Packet crafting = building packets manually, byte by byte,** to bypass the OS and get full control. Three uses:

- **Craft** packets to look exactly how you want — e.g. **PackETH** (GUI)
- **Interact** with a target in ways you couldn't without writing your own program — e.g. **hping**
- **Mangle** packets against a set of rules to **stress-test** the target's network stack — see if it can handle badly formed packets

**Raw sockets** are what make this possible: they let a programmer **bypass the network stack** and get **full control over what the packet ends up looking like**. That's why a tool like hping can set flags the OS would never set on its own.

## hping

The **"Swiss Army knife of TCP/IP packets."** It can send ICMP echo requests like `ping`, but it's primarily a **packet-crafting** tool — it lets you initiate communication over different protocols with **whatever header settings you want**.

**Defaults:** if you don't specify a target port, hping sends to **port 0** — an **invalid** destination for both TCP and UDP — with a varying source address.

**Modes / key flags:**

| Flag | Meaning |
| --- | --- |
| `-1` | ICMP mode |
| `-2` | UDP mode |
| `-S` | Set the SYN flag |
| `-p <port>` | Target port |
| `-a <IP>` | **Spoof** the source address |
| `--scan` / `-8` | Scan a range of ports |
| `-d <bytes>` | Set payload **size** |
| `--file <name>` | Fill the payload from a **file** |

**Sending SYN packets:**

```
sudo hping3 -S -p 80 192.168.86.1
```

hping reports back **all the flags set in the response**:

- Open port → replies **SA** (SYN + ACK)
- No listener → replies **RA** (RST + ACK) — *not* an "unreachable" or timeout message, just a different flag combination telling you nothing's there

**UDP port scan with a spoofed source:**

```
sudo hping3 --scan 1-1023 -a 10.15.24.5 -2 192.168.1.2
```

Because you spoofed the source with `-a`, **you won't see the responses** — they go to the address you faked (`10.15.24.5`), not to you. Spoofing hides your identity but blinds you to the replies (useful for idle scans, decoys, or provoking traffic toward a victim).

**Oversized / malformed payloads:**

```
sudo hping3 -d 2000 --file payload.txt <target>
```

- `d` sets the payload size in the header; `-file` fills the data portion from a file. Sending data that **violates protocol specifications** may **crash the target application**.

## PackETH

A **GUI** packet crafter. Pick the protocol and it **adjusts the form to show every header field** for that protocol. Beyond headers, you add your own **payload** (there's a **User Defined Payload** field in **hexadecimal**).

**Sending:**

- **Send** button → one packet
- **Gen-b** → specify a **number** of packets to send
- **Gen-s** → generate **streams**: saved, defined patterns of packets

Once a pattern is defined (by choosing which packets to use), you tell PackETH **how** to send them — **burst, continuous or random** — and set the **total count** and the **delay** between packets.

You can **save** packets you create, and **load a PCAP** (packet capture) file to replay or edit captured traffic.

## Fragroute

**Mangles packets before they're sent to a target.** It works by **altering the routing table** so that all messages are routed **through the fragroute application first** — fragroute then rewrites them according to your rules before they leave.

It needs a **configuration file** of **directives** telling it how to handle passing packets. Examples:

| Directive | Effect |
| --- | --- |
| `delay random 1` | Delay a random packet by 1 ms |
| `dup last 30%` | 30% chance of duplicating the last packet |
| `ip_chaff dup` | Insert **duplicate/junk** packets into the queue (layer 3) |
| `ip_frag 128 new` | Force **fragmentation** before sending (128-byte fragments) |
| `tcp_chaff null 16` | Same idea as ip_chaff but at the **transport layer** |
| `order random` | **Randomise** the order of messages |

**Why fragment?** Every data-link protocol sets a **Maximum Transmission Unit (MTU)** — the largest frame it will carry. Anything larger than the MTU gets **fragmented** into pieces (**jumbo frames** are the exception — deliberately larger frames). Fragmentation, reordering and chaff are classic ways to **confuse IDS/IPS and firewalls**: a filter that inspects whole messages may fail to reassemble the pieces correctly and miss the attack, while the target still reassembles them normally.

**The actual goal here is stress-testing:** when you run fragroute, you'll often see your interaction with the target **fail** — and that's what you want to see. A stack that can't handle the malformed input may **take the kernel down with it**, making the whole system unavailable and forcing a **reboot**. You're probing whether badly constructed packets can crash the target.

---

# Cheatsheet

**Concepts**

| Term | Meaning |
| --- | --- |
| **Packet crafting** | Building packets manually to bypass the OS stack |
| **Raw socket** | Mechanism giving full control over packet contents |
| **Spoofing** (`-a`) | Fake the source IP → you don't see the replies |
| **MTU** | Max frame size a data-link protocol carries; larger → fragmented |
| **Jumbo frame** | Frame deliberately larger than the standard MTU |
| **Chaff** | Junk/duplicate packets inserted to confuse inspection |

**Tools**

| Tool | Type | Purpose |
| --- | --- | --- |
| **hping / hping3** | CLI | Craft packets, custom flags, spoofing, UDP/TCP/ICMP scans, oversized payloads |
| **PackETH** | GUI | Build packets field-by-field, custom hex payload, streams, load/save PCAP |
| **Fragroute** | CLI + config | Reroute traffic through itself to fragment/reorder/duplicate — evade IDS, stress the stack |

**hping flags**

| Flag | Use |
| --- | --- |
| `-1` / `-2` | ICMP / UDP mode |
| `-S` | SYN flag |
| `-p` | Port |
| `-a` | Spoof source IP |
| `--scan` / `-8` | Port range scan |
| `-d` | Payload size |
| `--file` | Payload from file |

**hping responses:** `SA` = SYN-ACK (open) · `RA` = RST-ACK (nothing listening).

**Commands**

```
sudo hping3 -S -p 80 192.168.86.1
sudo hping3 --scan 1-1023 -a 10.15.24.5 -2 192.168.1.2
```

# Evasion Techniques

Firewalls, IDS and IPS can block traffic or raise an alert → **discovery of your actions**. Evasion is about getting past them without being stopped or spotted.

## Common evasion techniques

**Hide / obscure the data** — use **encryption or obfuscation** to disguise what you're doing. Encryption protects the content from sender to recipient so an inspecting device can't read it; **encoding** techniques (like **URL encoding**) can hide a payload from a signature that's looking for the plain string.

**Alterations** — IDS/IPS often use **signatures** (compare a hash against a database of known malware; match → drop). But changing a **single character** produces a **completely different hash**, so the signature no longer matches. Malware that rewrites itself this way = **polymorphism** ("many shapes/forms").

**Fragmentation** — split the message into pieces. Reassembly takes time, and some detection engines **don't reassemble** before inspecting (doing so would add latency), so the attack slips through in fragments and is only whole once it reaches the target.

**Overlaps** — send **overlapping fragments** with conflicting data in the overlapping bytes. The IDS and the target may **reassemble them differently** (different OSes resolve the overlap differently). The IDS sees a harmless version, the target reconstructs the malicious one. Classic IDS-evasion trick.

**Malformed data** — protocols have **rules** about how communication should happen. Violating them can produce **unexpected results** or exploit **loopholes** to extract information. This is exactly why **Xmas, FIN and Null scans** work: they set illegal or empty flag combinations that stateless filters don't have a rule for, so the packets pass while a normal SYN might be blocked — and the target's response (or silence) still leaks the port state.

**Low and slow** — spread activity out over a long time so it stays under detection thresholds and blends into normal traffic. In nmap this is the **timing/throttle** parameter (`-T 0` / `-T 1`).

**Resource consumption** — exhaust a device's **CPU or memory** so it **fails open** — once overwhelmed, it may let subsequent traffic **pass through uninspected** (same principle as MAC flooding a switch).

**Screen blindness (smoke screen)** — an IDS issues alerts, so **generate an enormous volume of bogus alerts** to overwhelm the analyst, then send your **real** traffic hidden inside the noise. The signal is buried in false positives.

**Tunneling** — transmit your data **inside another protocol**. Common carriers: **GRE** (Generic Routing Encapsulation), **SSH**, **HTTP**, **ICMP** and **DNS**. The traffic looks legitimate on the outside while carrying your payload within.

## Evasion with nmap

**Malformed packets** — network security may simply **ignore** packets it doesn't know how to handle, letting them through.

**Idle scan** — bounce the scan off an innocent third ("zombie") host so the target **never sees where the scan originated** — your IP never appears.

**Decoys (`-D`)** — nmap generates lots of **bogus traffic** alongside the real port scan, so your real IP is hidden among many fakes and the defender can't tell which source is genuine.

```
sudo nmap -D rnd:3 192.168.12.1     # 3 random decoys
```

**Fragmentation (`-f`)** — split the scan packets so intermediate devices may **not reassemble** them and just forward the pieces.

```
sudo nmap -f -p80,443 192.168.178.1
```

The MTU is about **1500 bytes**; you can force a very small custom MTU so packets fragment heavily. The value **must be a multiple of 8**.

```
sudo nmap --mtu 8 -p80,443 192.168.178.1
```

**MAC spoofing (`--spoof-mac`)** — fake your source MAC address.

```
sudo nmap --spoof-mac 00:11:22:33:44:55 192.168.1.1
```

**Why it only works on the local network:** a MAC address is a **layer 2** identifier and only travels within the **local segment**. The moment a packet crosses a **router**, the source MAC is **rewritten** to that of each hop — so against a **remote** target the spoofed MAC never reaches it and the technique is pointless.

**Source-port spoofing (`-g`)** — send scan traffic **from a chosen source port**. Firewalls are often configured to **trust traffic from certain ports** (e.g. DNS **53**, HTTP **80**), so disguising your scan as coming from port 53 can slip past those rules.

```
sudo nmap -g 53 192.168.1.1
```

---

# Cheatsheet

**Techniques**

| Technique | Idea |
| --- | --- |
| **Encryption / encoding** | Hide the content from inspection (URL encoding, TLS) |
| **Polymorphism** | Change bytes → change hash → defeat signatures |
| **Fragmentation** | Split so the engine doesn't reassemble in time |
| **Overlaps** | Conflicting overlapping fragments → IDS and target reassemble differently |
| **Malformed data** | Break protocol rules (Xmas/FIN/Null) → no signature, unexpected response |
| **Low and slow** | Stay under thresholds (`-T 0/1`) |
| **Resource consumption** | Exhaust CPU/RAM → device fails open |
| **Screen blindness** | Flood bogus alerts → hide real traffic in the noise |
| **Tunneling** | Wrap data in GRE / SSH / HTTP / ICMP / DNS |
| **Idle scan** | Bounce off a zombie → hide your source IP |

**nmap evasion flags**

| Flag | Effect |
| --- | --- |
| `-D rnd:3` | Decoys — bury real IP among fakes |
| `-f` | Fragment packets |
| `--mtu 8` | Custom small MTU (multiple of 8) |
| `-T 0` / `-T 1` | Low and slow timing |
| `--spoof-mac <MAC>` | Fake source MAC (**local segment only** — routers rewrite it) |
| `-g <port>` | Fake source port (e.g. 53 to look like DNS) |
| `-sI <zombie>` | Idle/zombie scan |

**Commands**

```
sudo nmap -D rnd:3 192.168.12.1
sudo nmap -f -p80,443 192.168.178.1
sudo nmap --mtu 8 -p80,443 192.168.178.1
sudo nmap --spoof-mac 00:11:22:33:44:55 192.168.1.1
sudo nmap -g 53 192.168.1.1
```

**Key exam points:** MAC spoofing works **local only** (layer 2, rewritten at each router) · overlapping fragments exploit **different reassembly** between IDS and host · fail-open = the goal of resource exhaustion · Xmas/FIN/Null are malformed-data evasion, not just scan types.

# Protecting and Detecting

Most scanning activity is surprisingly **easy to detect** — the trick from the defender's side is that scanning looks nothing like normal traffic.

## Why scans are easy to detect

Of the **1000 common ports** nmap checks, only around **20** see genuinely common, everyday traffic. So if connection attempts hit the other **~980**, that's abnormal — a **firewall or IDS** can flag it easily. A single host touching many ports in a short window, or many hosts in sequence, is a classic scan signature.

**Vulnerability scanners are even noisier** — they don't just probe ports, they fire hundreds or thousands of tests at each service, generating a large, distinctive volume of traffic that's trivial to spot.

**Packet crafting and spoofing** attacks often use **private (RFC 1918) addresses** as the spoofed source — and those are **not routable over the internet**. So a packet arriving from the internet claiming to come from `10.x` or `192.168.x` is obviously forged, and a properly configured perimeter drops it on sight.

## How to protect against network scanning

**Filter at the perimeter (default-deny)** — the firewall should **drop everything not explicitly allowed**. Fewer exposed ports = smaller attack surface = less for a scan to find. **Drop** rather than **reject**, so no ICMP "unreachable" is returned and the attacker gets less information.

**Ingress and egress filtering (anti-spoofing)** — block inbound packets whose **source is a private/reserved address** (they can't legitimately come from the internet), and block outbound packets whose source **isn't** one of your own addresses. This is **BCP 38** and it kills most spoofing.

**IDS/IPS with scan detection** — tune rules to spot the patterns: many ports from one source, sequential host sweeps, illegal flag combinations (Xmas/Null/FIN), unusual fragmentation. An **IPS** can block the source automatically once a threshold is crossed.

**Rate limiting / thresholds** — cap how many connections or SYNs a single source can open in a time window. This catches fast scans and slows the rest. Tools like **portsentry** or **fail2ban** can auto-block a scanning IP.

**Reduce the information you leak** — turn off unneeded services, **suppress version banners** (or make them generic), disable ICMP responses where practical, and remove PTR records that map IPs to descriptive hostnames. The less each response reveals, the less useful the scan.

**Network segmentation** — divide the network with internal firewalls/VLANs so a scan from one segment can't reach everything. Even if an attacker gets a foothold, segmentation limits what they can enumerate.

**Log, centralise and correlate** — ship logs off the hosts to a **SIEM** so scan patterns across many devices become visible and the evidence survives an attacker's clean-up. Correlation turns "one odd connection here and there" into a clear scan signature.

**Watch for the noise you know they'll make** — since scanning and vuln-scanning are inherently loud, monitoring is genuinely effective here. This is one of the phases where the defender has the advantage — use it.

---

# Cheatsheet — Defences

| Defence | What it stops |
| --- | --- |
| **Default-deny firewall, drop not reject** | Shrinks attack surface, leaks less info |
| **Ingress/egress filtering (BCP 38)** | Spoofed private/reserved source addresses |
| **IDS/IPS scan rules** | Port sweeps, host sweeps, illegal flags, fragmentation |
| **Rate limiting (portsentry, fail2ban)** | Fast scans; auto-blocks the source |
| **Banner suppression, disable services** | Version/OS fingerprinting, reduces recon value |
| **Disable ICMP / remove PTR records** | Ping sweeps, easy host mapping |
| **Segmentation (VLANs, internal FW)** | Limits how far any scan can reach |
| **Centralised logging + SIEM** | Correlates scans, survives track-covering |

**Key points**

- Only ~20 of the 1000 common ports carry normal traffic → the other ~980 make scans easy to spot
- Vulnerability scanners are the noisiest activity of all
- Private addresses (10/8, 172.16/12, 192.168/16) aren't routable → spoofed sources from the internet are obvious fakes
- Scanning is a **loud** phase — detection is where the defender wins