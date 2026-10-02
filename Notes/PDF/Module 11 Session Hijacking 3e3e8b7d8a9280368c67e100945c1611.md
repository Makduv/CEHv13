# Module 11: Session Hijacking

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Session Hijacking Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-session-hijacking-concepts)
2. [Application-Level Session Hijacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-application-level-session-hijacking)
3. [Network-Level Session Hijacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-network-level-session-hijacking)
4. [Session Hijacking Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-session-hijacking-countermeasures)
5. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-quick-exam-cheat-sheet)

---

## 1. Session Hijacking Concepts

### 🎯 What is Session Hijacking?

> **Session Hijacking** = an attack in which an attacker **seizes control of a valid TCP communication session** between two computers. Because most authentication occurs only at the **start** of a TCP session, this allows the attacker to gain access to a machine. Attackers sniff traffic from established TCP sessions and perform identity theft, information theft, fraud, etc.
> 
- Attacker steals a valid session ID and uses it to authenticate himself with the server
- Enables further attacks: **MITM** and **DoS**
- In MITM, attacker places themselves between client and server; both believe they communicate directly with each other

---

### ⚡ Why is Session Hijacking Successful?

| Factor | Description |
| --- | --- |
| **Absence of account lockout for invalid session IDs** | Attacker can make multiple attempts (brute-force) without warning message, until valid session ID is found |
| **Weak session-ID generation algorithm or small session IDs** | Linear algorithms predicting variables (time, IP) allow attackers to narrow the search space |
| **Insecure handling of session IDs** | Attacker retrieves stored session-ID via DNS poisoning, XSS exploitation, or browser bugs |

---

### 🔄 Session Hijacking Process — 3 Phases (CRITICAL)

```
1. Tracking the Connection
   - Attacker uses network sniffer or tool (e.g., Nmap) to track victim/host
   - Identifies TCP sequence that's easy to predict
   - Captures sequence and acknowledgment numbers

2. Desynchronizing the Connection
   - Desynchronized state = connection established but SEQ/ACK numbers mismatch
   - Attacker sends null data to advance server's SEQ/ACK without target registering it
   - Alternative: send RST flag to break connection, or intercept SYN/ACK to re-sync in "established" state
   - (Using FIN flag reveals attack via "ACK storm")

3. Injecting the Attacker's Packet
   - Once connection interrupted, attacker injects data into network
   - OR actively participates as man-in-the-middle, reading/injecting data at will
```

> To conduct a session hijack: **Tracking of a session → Desynchronization of the session → Injection of commands during the session**
> 

---

### 🎯 Active vs Passive Session Hijacking

| Type | Description |
| --- | --- |
| **Active Hijack** | Attacker **takes over** an existing session |
| **Passive Hijack** | Attacker **monitors** an ongoing session (no takeover) |

---

### 🔑 Requirements for Hijacking Non-Encrypted TCP Communications

- Presence of non-encrypted session-oriented traffic
- Ability to recognize TCP sequence numbers to predict the **Next Sequence Number (NSN)**
- Ability to spoof a host's MAC or IP address to receive communications not destined for attacker's host
- If on local segment: sniff and predict ISN+1, route traffic back via ARP cache poisoning

---

### 📊 Session Hijacking in OSI Model — 2 Levels (CRITICAL)

| Level | Description |
| --- | --- |
| **Network-Level Hijacking** | **Interception of packets** during transmission between client/server in a TCP or UDP session. Provides crucial info for attacking application-level sessions. Doesn't need per-web-application modification |
| **Application-Level Hijacking** | **Gaining control over the HTTP user session** by obtaining session IDs. Attacker gains control of existing session, can create new unauthorized sessions using stolen data |

---

### ⚠️ Spoofing vs. Session Hijacking

- **Spoofing:** Attacker pretends to be another user by using stolen/fake credentials (e.g., "I am James, here are my credentials")
- **Session Hijacking:** Attacker actively predicts the sequence number, kills the victim's connection, and spoofs the victim's IP to hijack an ALREADY ESTABLISHED session

---

## 2. Application-Level Session Hijacking

### 📊 Ways a Session Token Can Be Compromised (CRITICAL LIST — 12 Methods)

```
Session sniffing            | Session replay attack
Predictable session token   | Session fixation attack
Man-in-the-middle (MITM)    | CRIME attack
Man-in-the-browser attack   | Forbidden attack
Cross-site scripting (XSS)  | Session donation attack
Cross-site request forgery  | PetitPotam hijacking
```

---

### 🎯 Predictable Session Token (Brute-Forcing)

> Attacker attempts various session ID combinations against a server (e.g., `VW30422101518909`, `VW30422101520803`...) until arriving at the correct one, then gains complete access.
> 

> 💡 **Note:** A session ID brute-forcing attack is called a **session prediction attack** if the predicted range of values for a session ID is very small.
> 

---

### 🎯 Man-in-the-Middle (MITM) / Manipulator-in-the-Middle Attack

> Used to **intrude into an existing connection** between systems and intercept exchanged messages. Attacker splits the TCP connection into TWO: **client-to-attacker connection** and **attacker-to-server connection**. After interception, attacker can read, modify, and insert fraudulent data.
> 

---

### 🎯 Man-in-the-Browser Attack

> Extension registers a button event handler on a specific page → uses DOM interface to extract/modify all form field data → browser sends modified values to server → server cannot distinguish original from modified → receipt generated for modified transaction → browser displays receipt with ORIGINAL details → user believes original transaction succeeded.
> 

---

### 🎯 Client-Side Attacks (compromise session IDs)

| Attack | Description |
| --- | --- |
| **Cross-site scripting (XSS)** | Injects malicious client-side scripts into web pages viewed by other users |
| **Malicious JavaScript codes** | Embedded script captures session tokens silently, sends to attacker |
| **Trojans** | Changes proxy settings in user's browser to route all sessions through attacker's machine |

---

### 🎯 Session Replay Attack

```
1. User establishes connection with server
2. Server asks for authentication info
3. User sends authentication tokens
4. Attacker eavesdrops, captures the authentication tokens
5. Attacker replays the captured token to gain unauthorized access
```

---

### 🎯 Session Fixation Attack

```
1. Attacker sends email with malicious link containing a fixed Session ID (SID)
2. Victim clicks the link and logs into the vulnerable server (SID preserved)
3. Attacker logs in using the SAME SID (sessionid=0D6441FEA4496C2)
4. Both attacker and victim now share the same authenticated session
```

---

### 🎯 CRIME Attack (Compression Ratio Info-Leak Made Easy)

> A **client-side** attack that exploits vulnerabilities in the **data compression** feature of protocols such as SSL/TLS, SPDY, and HTTPS. Attackers hijack the session by **decrypting secret session cookies**.
> 

**Process:**

1. Attacker tricks victim into clicking malicious link with CRIME JavaScript
2. Victim establishes HTTPS session; attacker sniffs traffic and captures cookies
3. Attacker sends multiple HTTPS requests with cookie value prepended with random characters, monitoring the **total length** of the compressed response
4. By observing length changes, attacker predicts cookie characters ONE AT A TIME
5. Attacker successfully establishes HTTPS session using the reconstructed cookie

> In HTTPS, cookies compressed with a lossless algorithm (**DEFLATE**) then encrypted — difficult to obtain via simple sniffing, but compression ratio leaks info about the cookie content.
> 

---

### 🎯 Forbidden Attack

> A type of **MITM attack** used to hijack HTTPS sessions by exploiting the **reuse of a cryptographic nonce** during the TLS handshake. Exploits vulnerability where TLS implementation incorrectly reuses the same nonce when encrypting with **AES-GCM (Galois/Counter Mode)**.
> 

**Steps:**

1. Attacker monitors connection, sniffs the nonce from TLS handshake messages
2. Attacker generates authentication keys using the nonce and hijacks the connection
3. All traffic between victim and server flows through attacker's machine
4. Attacker injects JavaScript code/web fields into the transmission
5. Victim reveals sensitive info (bank numbers, passwords, SSNs) to attacker

---

### 🎯 Session Donation Attack

> Attacker **donates their own session identifier (SID)** to the target user (opposite of stealing). Attacker first obtains a valid SID by logging into a service, then feeds the SAME SID to the target user. This links the target user back to the attacker's account page.
> 

**Steps:**

1. Attacker logs into a service, establishes legitimate connection, deletes stored info
2. Web server issues session ID (e.g., `0D6441FEA4496C2`) to attacker
3. Attacker donates their SID to victim via link (`?SID=0D6441FEA4496C2`), lures click
4. Victim clicks, enters info believing it's legitimate, saves it under the SAME SID
5. Attacker logs back in and acquires victim's information

---

### 🎯 PetitPotam Hijacking

> A **domain controller (DC)** is forced by an attacker to initiate authentication to the attacker's server. Attacker uses Microsoft's **Encrypting File System Remote Protocol (MS-EFSRPC)** API for authentication session hijacking. Attacker relays the NTLM authentication to **Active Directory Certificate Services (AD CS)** and generates a certificate to acquire admin-level privileges.
> 

**Steps:**

1. Attacker uses already-captured NTLM credentials to authenticate with target server
2. Attacker uses `EfsRpcOpenFileRaw` command (MS-EFSRPC API) to coerce target server into NTLM-authenticating to attacker's SMB server
3. Attacker initiates NTLM replay attack to gain remote access to target AD CS
4. Attacker creates AD certificate to gain administrator privileges to the target AD server

---

## 3. Network-Level Session Hijacking

### 📊 6 Network-Level Hijacking Techniques (CRITICAL)

```
1. Blind Hijacking
2. UDP Hijacking
3. TCP/IP Hijacking
4. RST Hijacking
5. Man-in-the-Middle: Packet Sniffer
6. IP Spoofing: Source Routed Packets
```

> Network-level hijacking relies on hijacking **transport and Internet protocols** used by web apps at the application layer. Doesn't require host access (vs. host-level) or per-application tailoring (vs. application-level).
> 

---

### 🎯 Three-Way Handshake (Reference)

```
1. Bob sends SYN packet to server
2. Server responds with SYN+ACK and an Initial Sequence Number (ISN)
3. Bob sets ACK flag, increments sequence number by 1
4. Session established
```

---

### 🎯 Blind Hijacking

> IP address and port number are easy to determine (available in IP packets, unchanged throughout session). However, **sequence numbers change**. Attacker must successfully **guess the sequence numbers** for a blind hijack. If the attacker fools the server into accepting spoofed packets, the hijack succeeds.
> 

---

### 🎯 UDP Hijacking

> UDP doesn't use packet sequencing/synchronizing (connectionless) — easier to attack than TCP. Hijacker **forges a server reply to a client UDP request before the server can respond**.
> 

**How UDP Hijacking Works:**

- **Spoofing source IP:** No handshake required — attacker sends UDP packets pretending to be another host
- **Intercepting traffic:** Forged UDP packets sent to client/server appearing legitimate
- **Manipulating communication:** False info inserted into data stream to manipulate app behavior

---

### 🎯 TCP/IP Hijacking

> After spoofing IP successfully, hijacker alters sequence and acknowledgment numbers, injecting forged packets into the TCP session before the client responds — desynchronizing the connection.
> 

---

### 🎯 RST Hijacking

> Involves injecting an **authentic-looking reset (RST) packet** using a spoofed source address and **predicting the acknowledgment number**. If accurate, the victim's connection resets, believing the source sent the legitimate reset.
> 

**Process:**

```
1. Victim sends data packet to server (SEQ#1429775000, ACK#1250510000)
2. Server sends response back
3. Attacker spoofs server's IP address
4. Attacker predicts the ACK# and sends RST to reset the connection
```

**Tools:** Colasoft Packet Builder (packet crafting), tcpdump (TCP/IP analysis)

---

### 🎯 Man-in-the-Middle: Packet Sniffer

> Attacker uses a packet sniffer to intercept communication between client and server, positioning themselves as an intermediary.
> 

---

### 🎯 IP Spoofing: Source Routed Packets

> Source routed packets are useful for gaining unauthorized access using a **trusted host's IP address**. The sender specifies the path for packets from source to destination (source routing).
> 

**Process:**

1. Packet source routing technique used for gaining unauthorized access via trusted host's IP
2. Attacker spoofs host's IP so the server accepts packets from attacker
3. Attacker injects forged packets before the host responds
4. Original packet from host is lost (server receives packet with sequence number already used by attacker)
5. Attacker's packets are source-routed through the host with attacker-specified destination IP

---

## 4. Session Hijacking Countermeasures

### 🛡️ Protecting Against Session Hijacking (14-Point Checklist — CRITICAL)

1. Use **Secure Shell (SSH)** or OpenSSH to create a secure communication channel
2. Pass authentication cookies over **HTTPS** connections
3. Implement **log-out functionality** for the user to end sessions
4. Generate a session ID **after** successful login; accept only server-generated session IDs
5. Ensure data in transit is **encrypted**; implement **defense-in-depth**
6. Use strings or **long random numbers** as session keys
7. Use different usernames/passwords for different accounts
8. Educate employees; minimize remote access
9. Implement **timeout()** mechanism to destroy expired sessions
10. Avoid including the session ID in the **URL or query string**
11. Switch from a **hub network to a switch network** to reduce ARP spoofing/hijacking risk
12. Ensure client-side and server-side protection software are active/up-to-date
13. Use strong authentication (e.g., **Kerberos**) or **peer-to-peer VPNs**
14. Configure appropriate **internal and external spoof rules** on gateways
15. Use **IDS products or ARPwatch** for monitoring ARP cache poisoning
16. Enable browsers to **verify website authenticity** using network notary servers
17. Use **SFTP, AS2 managed file transfer, or FTPS** for encrypted data + digital certificates

---

### 🔍 Session Hijacking Detection Methods

> Session hijacking attacks are **exceptionally difficult to detect** — users often overlook them unless severe damage occurs.
> 

**Symptoms:**

- A burst of network activity for some time, decreasing system performance
- Busy servers resulting from requests sent by BOTH the client and the hijacker

**Detection Tools:** Wireshark, Quantum Intrusion Prevention System (IPS), SolarWinds Security Event Manager, IBM Security Network Intrusion Prevention System, LogRhythm

---

### 🛡️ Approaches to Prevent MITM Attacks

**DNS over HTTPS (DoH):**

> Enhanced DNS protocol version used to prevent snooping of user's web activities/DNS queries during the DNS lookup process. Web queries and traffic sent through encrypted HTTPS via **port 443** (vs conventional DNS on port 53). DoH sends only a **segment** of the necessary domain name (not the complete domain) to fetch results. Adopted by Chrome, Mozilla, Microsoft Edge (Mozilla default since 2020 for US clients).
> 

---

### 🔐 IPsec (Internet Protocol Security)

> A protocol suite developed by the **IETF** for securing IP communications by **authenticating and encrypting** each IP packet of a communication session. Widely deployed for VPNs and remote user access via dial-up to private networks.
> 

**IPsec Authentication and Confidentiality — Two Security Services:**

| Service | Function |
| --- | --- |
| **Authentication Header (AH)** | Provides **data authentication** of the sender only |
| **Encapsulation Security Payload (ESP)** | Provides **BOTH data authentication AND encryption (confidentiality)** of the sender |

**Benefits of IPsec:**

```
Network-level peer authentication | Data origin authentication |
Data integrity | Data confidentiality (encryption) | Replay protection
```

**Modes of IPsec:**

| Mode | Description |
| --- | --- |
| **Transport Mode** | Encapsulates only the transport-layer payload: `IP header + IPsec header + Transport data (TCP/UDP) + IPsec trailer (ESP only)`. Encrypted portion = transport data; authenticated portion = everything except outer IP header |
| **Tunnel Mode** | Encapsulates the ENTIRE original IP packet: `Outer IP header + IPsec header + Inner IP header + IP payload + IPsec trailer (ESP only)`. Used between gateways (Network 1 ↔ GW1 ↔ Internet ↔ GW2 ↔ Network 2) |

**IPsec Architecture:**

```
IPsec Architecture
    ├── AH Protocol  → Authentication Algorithm ─┐
    └── ESP Protocol → Encryption Algorithm ─────┼──→ IPsec Domain of Interpretation (DOI)
                                                   │         ↑↓
                                             Policy ←──→ Key Management
```

---

### 🛠️ Session Hijacking Tools

```
bettercap | Burp Suite | OWASP ZAP | WebSploit Framework | sslstrip | JHijack
```

---

## 5. Quick Exam Cheat Sheet

### 🔄 3-Phase Session Hijacking Process

```
1. Tracking the Connection → 2. Desynchronizing the Connection → 3. Injecting the Attacker's Packet
```

---

### 📊 2 Levels of Session Hijacking (OSI Model)

```
Network-Level     → interception of packets (TCP/UDP transmission)
Application-Level → control over HTTP user session via session IDs
```

---

### 📊 12 Ways to Compromise a Session Token

```
Session sniffing | Predictable session token | MITM | Man-in-the-browser |
XSS | CSRF | Session replay | Session fixation | CRIME | Forbidden attack |
Session donation | PetitPotam hijacking
```

---

### 📊 6 Network-Level Hijacking Techniques

```
Blind Hijacking | UDP Hijacking | TCP/IP Hijacking |
RST Hijacking | MITM Packet Sniffer | IP Spoofing (Source Routed Packets)
```

---

### 🔐 IPsec Quick Reference

```
AH  (Authentication Header)        → authentication ONLY
ESP (Encapsulation Security Payload) → authentication AND encryption

Transport Mode → encrypts payload only (host-to-host)
Tunnel Mode    → encrypts entire IP packet (gateway-to-gateway)
```

---

### 🔥 Common Exam Scenarios

**Q: What are the 3 phases of the session hijacking process?**
→ **Tracking the Connection → Desynchronizing the Connection → Injecting the Attacker's Packet**

**Q: What's the difference between active and passive session hijacking?**
→ Active = takes over an existing session; Passive = monitors an ongoing session

**Q: What's the difference between network-level and application-level hijacking?**
→ Network-level intercepts packets in TCP/UDP transmission; application-level gains control over the HTTP session via session IDs

**Q: What attack exploits data compression in SSL/TLS to decrypt session cookies?**
→ **CRIME Attack** (Compression Ratio Info-Leak Made Easy)

**Q: What attack exploits nonce reuse in AES-GCM during a TLS handshake?**
→ **Forbidden Attack**

**Q: What attack has the attacker donate their OWN session ID to a victim (rather than stealing one)?**
→ **Session Donation Attack**

**Q: What attack forces a domain controller to authenticate to an attacker's server using MS-EFSRPC?**
→ **PetitPotam Hijacking**

**Q: What's the difference between AH and ESP in IPsec?**
→ AH provides authentication only; ESP provides both authentication AND encryption

**Q: What IPsec mode encrypts the entire original IP packet (used gateway-to-gateway)?**
→ **Tunnel Mode**

**Q: What IPsec mode encrypts only the transport-layer payload (host-to-host)?**
→ **Transport Mode**

**Q: What network-level hijacking technique requires guessing sequence numbers without any prior traffic analysis?**
→ **Blind Hijacking**

**Q: Why is UDP hijacking easier than TCP hijacking?**
→ UDP is connectionless and doesn't use packet sequencing/synchronizing

**Q: What protocol enhancement prevents DNS snooping by sending queries over HTTPS (port 443)?**
→ **DNS over HTTPS (DoH)**

**Q: What's the difference between spoofing and session hijacking?**
→ Spoofing = pretending to be someone using fake/stolen credentials from the start; Session hijacking = taking over an ALREADY ESTABLISHED session by predicting sequence numbers and killing the victim's connection

**Q: When is a session ID brute-forcing attack called a "session prediction attack"?**
→ When the predicted range of values for the session ID is **very small**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 11*