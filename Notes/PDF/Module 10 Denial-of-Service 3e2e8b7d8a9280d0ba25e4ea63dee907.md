# Module 10: Denial-of-Service

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [DoS/DDoS Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-dosddos-concepts)
2. [Botnets](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-botnets)
3. [DoS/DDoS Attack Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-dosddos-attack-techniques)
4. [DoS/DDoS Attack Tools](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-dosddos-attack-tools)
5. [DoS/DDoS Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-dosddos-attack-countermeasures)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. DoS/DDoS Concepts

### 🎯 What is a DoS Attack?

> **Denial-of-Service (DoS)** = attack on a computer/network that **reduces, restricts, or prevents** accessibility of system resources to legitimate users. Attackers flood the victim system with non-legitimate service requests/traffic to overload its resources.
> 

> Goal: keep legitimate users from using the system, **NOT** to gain unauthorized access or corrupt data.
> 

**Types of DoS attacks:**

- Flooding the victim's system with more traffic than it can handle
- Flooding a service (e.g., IRC) with more events than it can handle
- Crashing a TCP/IP stack by sending corrupt packets
- Crashing a service by interacting with it in an unexpected manner
- Hanging a system by causing it to go into an infinite loop

**DoS attacks may cause:**

- Consumption of resources (bandwidth, disk space, CPU time, data structures)
- Actual physical destruction/alteration of network components
- Destruction of programming and files

**Impact:** Loss of goodwill, network outages, financial losses, operational disruptions

---

### 🔄 How do DDoS Attacks Work?

> In a DDoS attack, the attacker sends a command to **zombie agents** (Internet-connected computers compromised via malware). Zombie agents send connection requests to reflector systems with the **spoofed IP address of the victim**. Reflector systems presume requests originate from the victim, so they send responses to the victim — flooding it with unsolicited responses from multiple reflectors simultaneously.
> 

**DDoS attack flow:** Attacker → sets Handler system → Handler infects computers (Compromised PCs/Zombies) → Zombie systems instructed to attack a target server

---

## 2. Botnets

### 🤖 What is a Botnet?

> "Bot" = contraction of "robot" — software applications running automated tasks over the Internet. Attackers infect large numbers of computers to form a **botnet**, used to launch DDoS attacks, generate spam, spread viruses, commit crimes.
> 

---

### 🏗️ A Typical Botnet Setup (12-Step Process — CRITICAL)

```
1. Attacker sets a C&C center and Crimeware Toolkit database
2. Attacker recruits affiliates
3. Affiliates contribute malware and release DDoS Toolkit
4. Malicious website redirects users to the Crimeware toolkit database
5. Redirect victims to malicious website using phishing/social engineering
6. Users visit the malicious/compromised legitimate website
7. Compromise legitimate website or create new malicious website
8. Malware infects user systems
9. Bots connect back to C&C center
10. Bots receive instructions from C&C center to attack the primary target
11. Infected systems look for other vulnerable systems and infect them to add to the botnet
12. Attacks the primary target (Organization)
```

---

### 🌐 Botnet Ecosystem

```
Owner → C&C → Botnet (Crimeware Toolkit Database + Trojan Command and Control Center)
      → DDoS, Extortion, Scan & Intrusion, Botnet Market, Zero-Day Market, Malware Market

Botnet → Malicious Site → Data Theft, Phishing, Spam, Mass Mailing
       → Financial Diversion (credit card e-commerce, licenses)
       → Stock Fraud, Scams, Adverts
```

---

### 🔍 Scanning Methods for Finding Vulnerable Machines (CRITICAL TABLE)

| Method | Description |
| --- | --- |
| **Random Scanning** | Infected machine probes IP addresses randomly from target network IP range; checks vulnerabilities. Generates significant traffic; propagation speed reduces over time |
| **Hit-list Scanning** | Attacker first collects a list of potentially vulnerable machines, then scans them. Divides the list in half on infection — exponential growth of compromised machines |
| **Topological Scanning** | Uses information obtained from an infected machine (URLs on hard drive) to find new vulnerable machines. Accurate, similar performance to hit-list scanning |
| **Local Subnet Scanning** | Infected machine searches for new vulnerable machines in its own local network, behind a firewall, using hidden local address info |
| **Permutation Scanning** | Uses a shared pseudorandom permutation list of IP addresses (32-bit block cipher + preselected key). Avoids reinfection; ensures high scanning speed via random restart points |

---

## 3. DoS/DDoS Attack Techniques

### 📊 Basic Categories of DoS/DDoS Attack Vectors (CRITICAL TABLE)

| Category | Description | Measured In | Attack Techniques |
| --- | --- | --- | --- |
| **Volumetric Attacks** | Consume bandwidth of target network/service | **bits-per-second (bps)** | UDP flood, ICMP flood, Ping of Death, Smurf, Pulse wave, Zero-day attack, NTP amplification |
| **Protocol Attacks** | Consume connection state tables in network infrastructure (load balancers, firewalls, app servers) | **packets-per-second (pps)** | SYN flood, Fragmentation attack, Spoofed session flood, ACK flood, TCP SACK panic |
| **Application Layer Attacks** | Consume application resources, unavailable to legitimate users | **requests-per-second (rps)** | HTTP GET/POST attack, Slowloris, UDP application layer flood, DDoS extortion |

> Types of bandwidth depletion attacks: **Flood attacks** and **Amplification attacks**
> 

---

### 💧 Volumetric Attacks — Detail

**UDP Flood Attack:** Attacker sends spoofed UDP packets at a very high rate to a remote host on random ports, forcing the host to repeatedly check for listening applications at that port and reply with ICMP "Destination Unreachable" — exhausting resources.

**ICMP Flood Attack:** Attacker sends ICMP ECHO requests with spoofed source addresses at a rate exceeding the target's capacity to respond, exhausting bandwidth.

**Ping of Death:** Attacker sends malformed/oversized ping packets using `ping` command, crashing target system.

**Smurf Attack:** Attacker sends ECHO request with **spoofed source IP (victim's)** to an IP Broadcast Network — all hosts on the network respond to the victim, flooding it.

**Pulse Wave DDoS Attack:** Attack pattern is **periodic** (not continuous) — highly repetitive strain of packets sent every ~10 min; a single pulse (300+ Gbps) can crowd a network pipe; session lasts ~1 hour to days. Very difficult/impossible to recover from.

**Zero-Day DDoS Attack:** Exploits DDoS vulnerabilities with **no available patches or defensive mechanisms** yet.

---

### 🔗 Protocol Attacks — Detail

**SYN Flood Attack:** Malicious host sends many SYN requests simultaneously, exploiting the connection queue. Incomplete connections held for 75s cumulatively exhaust the queue. Uses spoofed IPs — hard to trace.

**SYN-ACK Flood Attack:** Exploits the SECOND stage of the three-way handshake — sends large number of SYN-ACK packets to exhaust target resources.

**ACK and PUSH ACK Flood Attack:** Attacker sends large amount of spoofed ACK/PUSH ACK packets, making the target non-functional.

**Fragmentation Attack:** Floods target with TCP/UDP fragments that cannot be reassembled, exhausting resources trying to reassemble them.

**Permanent Denial-of-Service Attack ("Phlashing"):**

> Causes **irreversible damage to system hardware**, requiring replacement/reinstallation. Uses "bricking a system" method — attacker sends fraudulent hardware update content (email, IRC, tweets, videos) with modified/corrupted firmware. When victim installs it, attacker gains complete control.
> 

**TCP SACK Panic Attack:**

> Attackers crash target Linux machines by sending **SACK packets with malformed maximum segment size (MSS)**. Exploits an **integer overflow vulnerability in Linux Socket Buffer (SKB)** leading to kernel panic. Sets MSS to lowest value (48 bytes); socket buffer exceeds limit → integer overflow → kernel panic → DoS.
> 

**Distributed Reflection DoS (DRDoS) Attack:** Attacker → Intermediary Victims → Secondary Victims → Primary Target (multi-hop reflection chain).

> Countermeasure: Turn off CHARGEN (Character Generator Protocol) service; download latest server updates/patches.
> 

---

### 🖥️ Application Layer Attacks — Detail

> Attacks unpatched, vulnerable systems; don't require as much bandwidth. Consume app resources by opening connections and leaving them open. Effective with few attacking machines at low traffic rate. **Very difficult to detect and mitigate.** Measured in requests-per-second (rps).
> 

**HTTP GET Attack:** Request with time-delayed HTTP header — target server waits for complete header.
**HTTP POST Attack:** Request with incomplete message body — target server waits for message body.

**HTTP Flood Variants:**

| Type | Description |
| --- | --- |
| **Single-Session HTTP Flood** | Exploits HTTP 1.1 vulnerabilities to bombard target with multiple requests in ONE session |
| **Single-Request HTTP Flood** | Multiple HTTP requests from a single session, concealed within a single HTTP packet — anonymous/invisible |
| **Recursive HTTP GET Flood** | Poses as legitimate user collecting a list of pages/images while stealthily flooding |
| **Random Recursive GET Flood** | Tweaked version targeting forums/blogs — uses random page numbers from a valid range |

**Slowloris Attack:** Layer-7 DDoS tool using **perfectly legitimate HTTP traffic** — sends partial HTTP requests to keep many connections open, exhausting the target server's connection pool.

**DDoS Extortion/Ransom DDoS (RDDoS) Attack:** Attacker threatens organization with a DDoS attack, demanding ransom. Sends ransom note or launches sample attack to prove capability; threatens larger attack unless paid (via digital currency).

---

## 4. DoS/DDoS Attack Tools

```
High Orbit Ion Cannon (HOIC)
Low Orbit Ion Cannon (LOIC)
HULK
Slowloris
UFONet
Packet Flooder Tool (NetScanTools)
```

---

## 5. DoS/DDoS Attack Countermeasures

### 📊 6 DDoS Attack Countermeasure Categories (CRITICAL)

```
1. Protect Secondary Victims
2. Detect and Neutralize Handlers
3. Prevent Potential Attacks
4. Deflect Attacks
5. Mitigate Attacks
6. Post-attack Forensics
```

---

### 1️⃣ Protect Secondary Victims

- Monitor security regularly to remain protected from DDoS agent software
- Install anti-virus and anti-Trojan software; keep updated
- Increase awareness of security issues/prevention techniques among Internet users
- Disable unnecessary services, uninstall unused applications, scan all files from external sources
- Properly configure and regularly update built-in defensive mechanisms

---

### 2️⃣ Detect and Neutralize Handlers

| Technique | Description |
| --- | --- |
| **Network Traffic Analysis** | Analyze communication protocols/traffic patterns between handlers and clients/agents to identify infected network nodes |
| **Neutralize Botnet Handlers** | Fewer handlers than agents exist — neutralizing a few handlers can render multiple agents useless |
| **Spoofed Source Address** | Decent probability the spoofed source address won't represent a valid source of the definite sub-network |

---

### 3️⃣ Prevent Potential Attacks

Includes: Egress filtering, ingress filtering, TCP intercept, rate limiting

---

### 4️⃣ Deflect Attacks — Honeypots

> Systems set up with **limited security** to act as enticement for an attacker. Serve as a means to gain info about attackers, attack techniques, and tools by recording system activities. **Defense-in-depth approach** with IPsec used at different network points to divert suspicious DoS traffic to several honeypots.
> 

**Two types:**

- **Low-interaction honeypots**
- **High-interaction honeypots** (example: **honeynet** — simulates complete network layout for "capturing" attacks, contains real computers running real applications)

---

### 5️⃣ Mitigate Attacks

Load balancing, throttling, drop requests, and other capacity-management techniques to reduce attack impact while preserving service.

---

### 6️⃣ Post-attack Forensics

> Analyze routers, firewalls, and IDS logs to recognize the type of DDoS attack (or combination) used. Attempt to trace back the attacker's IP address with intermediary ISPs and law enforcement agencies.
> 

---

### 🛡️ Techniques to Defend Against Botnets (4 Techniques — CRITICAL)

| Technique | Description |
| --- | --- |
| **RFC 3704 Filtering** | Basic ACL filter limiting DDoS impact by denying traffic with spoofed addresses. Uses a "bogon list" (unused/reserved IPs) — packets from these IPs are dropped |
| **Cisco IPS Source IP Reputation Filtering** | Reputation services determine if an IP/service is a threat source. Cisco IPS regularly updates its database (botnets, botnet harvesters, malware) via Cisco SensorBase Network |
| **Black Hole Filtering** | A "black hole" = network node where incoming traffic is silently discarded/dropped without notifying the source. Discards packets at the routing level |
| **DDoS Prevention from ISP/DDoS Service** | Enable **IP Source Guard** (Cisco) or similar to filter traffic based on the **DHCP snooping binding database** or IP source bindings, preventing spoofed-packet bots from succeeding |

---

### 📋 Additional DoS/DDoS Countermeasure Strategies

- Use advanced network-level surveillance technologies to monitor network perimeter
- Ensure semi-accessible connections enabled with assertive timeout functions
- Implement distributed server model and colocation services as backup
- Ensure servers are free of bottlenecks/failure points
- Use third-party protection services
- Use multi-cloud deployment models for major applications
- Perform extensive DoS/DDoS attack simulations
- Share info with industry peers; leverage threat intelligence feeds
- Use AI/ML and anomaly detection systems for traffic deviation flagging
- Limit network broadcasting
- Disable services such as echo and chargen

---

### 🛠️ DoS/DDoS Protection Tools/Services

```
Huawei AntiDDoS1000 | A10 Thunder TPS | Akamai (Security Center console)
Stormwall PRO | Imperva DDoS Protection | Nexusguard | BlockDoS
```

**A10 Thunder TPS features:** Maintain service availability, defeat growing attacks, scalable protection, reduce security OpEx

---

## 6. Quick Exam Cheat Sheet

### 📊 3 Basic Categories of DoS/DDoS Attack Vectors

```
Volumetric Attacks     → bandwidth exhaustion, measured in bps
                          (UDP flood, ICMP flood, Ping of Death, Smurf, Pulse wave, NTP amplification)
Protocol Attacks       → connection state table exhaustion, measured in pps
                          (SYN flood, Fragmentation, Spoofed session flood, ACK flood, TCP SACK panic)
Application Layer      → app resource exhaustion, measured in rps
Attacks                  (HTTP GET/POST, Slowloris, UDP app flood, DDoS extortion)
```

---

### 🔍 5 Scanning Methods for Finding Vulnerable Machines

```
Random Scanning | Hit-list Scanning | Topological Scanning |
Local Subnet Scanning | Permutation Scanning
```

---

### 📊 6 DDoS Attack Countermeasure Categories

```
1. Protect Secondary Victims
2. Detect and Neutralize Handlers
3. Prevent Potential Attacks
4. Deflect Attacks (Honeypots)
5. Mitigate Attacks
6. Post-attack Forensics
```

---

### 🛡️ 4 Techniques to Defend Against Botnets

```
RFC 3704 Filtering | Cisco IPS Source IP Reputation Filtering |
Black Hole Filtering | DDoS Prevention from ISP/DDoS Service
```

---

### 🔥 Common Exam Scenarios

**Q: What's the main goal of a DoS attack?**
→ To **deny legitimate users access** to a system (NOT to gain unauthorized access or steal data)

**Q: What are the 3 basic categories of DoS/DDoS attack vectors?**
→ **Volumetric, Protocol, and Application Layer attacks**

**Q: How is a Volumetric attack's magnitude measured?**
→ **bits-per-second (bps)**

**Q: How is a Protocol attack's magnitude measured?**
→ **packets-per-second (pps)**

**Q: How is an Application Layer attack's magnitude measured?**
→ **requests-per-second (rps)**

**Q: What attack sends an ICMP ECHO request with the victim's spoofed IP to a broadcast network?**
→ **Smurf Attack**

**Q: What attack exploits Linux Socket Buffer with malformed SACK packets and low MSS?**
→ **TCP SACK Panic Attack**

**Q: What DoS attack type is also known as "phlashing" or "bricking a system"?**
→ **Permanent Denial-of-Service (PDoS) Attack**

**Q: What DDoS attack pattern is periodic rather than continuous, with huge pulses every ~10 minutes?**
→ **Pulse Wave DDoS Attack**

**Q: What scanning method uses a common pseudorandom list generated with a 32-bit block cipher?**
→ **Permutation Scanning**

**Q: What scanning method divides a target list in half upon each new infection for exponential growth?**
→ **Hit-list Scanning**

**Q: What tool/technique uses limited security systems to attract and study DDoS attackers?**
→ **Honeypots** (Deflect Attacks countermeasure)

**Q: What's the difference between low- and high-interaction honeypots?**
→ High-interaction (e.g., honeynet) simulates a complete network with real computers/applications; low-interaction offers more limited emulation

**Q: What filtering technique uses a "bogon list" of unused/reserved IP addresses?**
→ **RFC 3704 Filtering**

**Q: What service should be disabled to prevent Distributed Reflection DoS (DRDoS) attacks?**
→ **CHARGEN (Character Generator Protocol)**

**Q: What Layer-7 DDoS tool uses legitimate HTTP traffic with partial requests to exhaust connections?**
→ **Slowloris**

**Q: What is DDoS Extortion also known as?**
→ **Ransom DDoS (RDDoS)**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 10*