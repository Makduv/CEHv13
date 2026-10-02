# Module 16: Hacking Wireless Network

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Wireless Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-wireless-concepts)
2. [Wireless Encryption Algorithms](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-wireless-encryption-algorithms)
3. [Wireless Threats](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-wireless-threats)
4. [Wireless Hacking Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-wireless-hacking-methodology)
5. [Wireless Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-wireless-attack-countermeasures)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. Wireless Concepts

### 🔑 Key Terminology

| Term | Definition |
| --- | --- |
| **BSSID** | MAC address of an AP/base station that set up a Basic Service Set (BSS) |
| **ISM Band** | Set of frequencies used by industrial, scientific, medical communities |
| **Hotspot** | Places where wireless networks are available for public use |
| **Association** | Process of connecting a wireless device to an AP |
| **SSID** | 32-alphanumeric-character unique identifier for a WLAN |
| **OFDM** | Orthogonal Frequency-Division Multiplexing — splits signal into multiple orthogonal carrier frequencies |
| **MIMO-OFDM** | Multiple Input, Multiple Output-OFDM — influences spectral efficiency of 4G/5G |
| **DSSS** | Direct-Sequence Spread Spectrum — multiplies signal with pseudo-random noise-spreading code |
| **FHSS** | Frequency-Hopping Spread Spectrum (FH-CDMA) — rapidly switches carrier among frequency channels |

---

### 📡 Wireless Standards Table (CRITICAL)

| Standard | Frequency (GHz) | Modulation | Data Rate (Mbps) | Range (m) |
| --- | --- | --- | --- | --- |
| **802.11a** | 5 | OFDM | up to 54 | — |
| **802.11b** | 2.4 | DSSS | up to 11 | — |
| **802.11g** | 2.4 | OFDM | 6,9,12,18,24,36,48,54 | 38–140 |
| **802.11n** | 2.4, 5 | MIMO-OFDM | 54–600 | 70–250 |
| **802.11ax (Wi-Fi 6)** | — | OFDMA | up to 9.6 Gbps | — |
| **802.11be (Wi-Fi 7)** | — | MLO | up to 30 Gbps | — |
| **802.15.1 (Bluetooth)** | 2.4 | GFSK, π/4-DPSK, 8DPSK | 25–50 | 10–240 |
| **802.15.4 (ZigBee)** | 0.868, 0.915, 2.4 | O-QPSK, GFSK, BPSK | 0.02–0.25 | 1–100 |
| **802.16 (WiMAX)** | 2–11 | SOFDMA | 34–1000 | 1609–9656 (1-6 mi) |

**Other standards:**

- **802.11d** — enhancement to 802.11a/b for global portability (regulatory domains)
- **802.11e** — QoS for voice/VoIP/video (Layer 2/MAC layer)
- **802.11i** — improves WLAN security; new encryption protocols (TKIP, AES); defines WPA2-Enterprise/Personal
- **802.11ac** — 5 GHz, high-throughput, Gigabit networking
- **802.11ad** — 60 GHz spectrum, new physical layer
- **802.11ah (Wi-Fi HaLow)** — 900 MHz, extended-range, IoT communication
- **802.12** — demand priority protocol, 100 Mbps, compatible with 802.3/802.5

---

### 📡 Antenna Types

| Type | Description |
| --- | --- |
| **Directional Antenna** | Radiates in a specific direction (unidirectional) |
| **Omnidirectional Antenna** | Radiates EM energy in all directions — 360° horizontal pattern; used by radio stations |
| **Parabolic Grid Antenna** | Semi-dish grid of aluminum wires; achieves very-long-distance transmissions (~10 miles) via focused radio beams; enables Layer-1 DoS and MITM attacks |

---

## 2. Wireless Encryption Algorithms

### 🔒 WEP (Wired Equivalent Privacy)

> Uses **RC4** stream cipher, **24-bit IV**, 40/104-bit key length, **CRC-32** integrity check, no key management.
> 

**Flaws of WEP:**

- No defined method for encryption key distribution — PSKs rarely changed
- RC4 designed for a more randomized environment than WEP provides
- Attacker can compute the key with knowledge of ciphertext/plaintext
- Weak IVs — RC4's Key Scheduling Algorithm (KSA) makes first bytes of plaintext predictable
- Same IV can be reused with the same secret key
- Vulnerable to **Fluhrer-Mantin-Shamir (FMS) attacks**
- No built-in provision to update keys

**Cracking tools:** Fern Wifi Cracker, WEP-key-break, aircrack-ng, Wifi-Cracker

---

### 🔒 WPA (Wi-Fi Protected Access)

> Uses **RC4 + TKIP** (Temporal Key Integrity Protocol), 48-bit IV, 128-bit key, **4-way handshake** key management, **Michael algorithm + CRC-32** integrity.
> 

**How WPA works:**

1. TK, transmit address, TSC used as input to RC4 to generate a keystream
2. IV/TK sequence + transmit address + TK combined with hash function → 128-bit/104-bit key
3. Key combined with RC4 → keystream
4. MSDU + MIC combined using **Michael algorithm**
5. Combination fragmented → MPDU
6. 32-bit ICV calculated for MPDU
7. MPDU + ICV bitwise XORed with keystream → encrypted data
8. IV added to encrypted data → MAC frame

**Issues with WPA:**

- Weak passwords vulnerable to cracking attacks
- Lack of forward secrecy — capturing PSK decrypts ALL packets
- Vulnerable to packet spoofing/decryption attacks (WPA-TKIP) — enables TCP hijacking

---

### 🔒 WPA2

> Uses **AES-CCMP**, 48-bit IV, 128-bit key, 4-way handshake, **CBC-MAC** integrity.
> 

---

### 🔒 WPA3

> Uses **AES-GCMP 256**, arbitrary-length IV (1 - 2^64), **192-bit key**, **ECDH and ECDSA** key management, **BIP-GMAC-256** integrity.
> 

---

### 📊 Comparison of WEP, WPA, WPA2, WPA3 (CRITICAL TABLE — MEMORIZE)

| Encryption | Algorithm | IV Size | Key Length | Key Establishment | Key Management | Integrity Check |
| --- | --- | --- | --- | --- | --- | --- |
| **WEP** | RC4 | 24-bits | 40/104-bits | **Open System** ou **Shared Key Authentication** | None | CRC-32 |
| **WPA** | RC4, TKIP | 48-bits | 128-bits | PSK ou 802.1X/EAP | 4-way handshake | Michael algorithm + CRC-32 |
| **WPA2** | AES-CCMP | 48-bits | 128-bits | PSK ou 802.1X/EAP | 4-way handshake | CBC-MAC |
| **WPA3** | AES-GCMP 256 | Arbitrary (1-2^64) | 192-bits | **SAE** ou 802.1X/EAP | ECDH and ECDSA | BIP-GMAC-256 |

**Summary:**

- **WEP, WPA** → should be replaced with more secure WPA2/WPA3
- **WPA2** → incorporates protection against forgery and replay attacks
- **WPA3** → enhanced password protection, secure IoT connections, stronger encryption

---

## 3. Wireless Threats

### 📊 5 Categories of Wireless Threats (CRITICAL)

```
1. Access Control Attacks   — evade WLAN access-control measures
2. Integrity Attacks        — send forged control/management/data frames
3. Confidentiality Attacks  — intercept confidential info
4. Availability Attacks     — obstruct delivery of wireless services
5. Authentication Attacks   — steal identity of Wi-Fi clients
```

---

### 🚫 Access Control Attacks

| Attack | Description |
| --- | --- |
| **MAC Spoofing** | Reconfigure MAC address to appear as an authorized AP/host (tool: SMAC) |
| **AP Misconfiguration** | Improperly configured security settings expose the network; hard to detect (legitimate device) |
| **Ad Hoc Associations** | — |
| **Promiscuous Client** | — |
| **Client Mis-association** | Attacker sets up rogue AP outside perimeter, spoofs SSID, lures clients to connect, launches MITM/EAP dictionary/Metasploit attacks |
| **Unauthorized Association** | Two forms: accidental (connecting to neighboring org's AP unknowingly) and malicious (attacker creates soft AP on laptop to gain access) |

**Key elements of misconfigured AP attacks:** SSID broadcast (vulnerable to brute-force), weak password (SSID used as password), configuration error

---

### 🔧 Integrity Attacks (Table — CRITICAL)

| Type of Attack | Description | Method/Tools |
| --- | --- | --- |
| **Data-Frame Injection** | Constructing/sending forged 802.11 frames | Airpwn-ng, Wperf |
| **WEP Injection** | Constructing/sending forged WEP encryption keys | WEP cracking + injection tools |
| **Bit-Flipping Attacks** | Capturing frame, flipping random bits in payload, modifying ICV, sending to user | — |
| **Extensible AP Replay** | Capturing 802.1X EAP protocols (Identity, Success, Failure) for later replay | Wireless capture + injection tools |
| **Data Replay** | Capturing 802.11 data frames for later (modified) replay | Capture + injection tools |
| **Initialization Vector Replay Attacks** | Deriving keystream by sending a plaintext message | — |
| **RADIUS Replay** | Capturing RADIUS Access-Accept/Reject messages for later replay | Ethernet capture + injection tools |
| **Wireless Network Viruses** | — | — |

---

### 🕵️ Confidentiality Attacks

```
Eavesdropping | Traffic Analysis | Cracking WEP Key |
Evil Twin AP | Honeypot AP | Session Hijacking |
Masquerading | Man-in-the-Middle Attack
```

**Honeypot AP:** Attacker sets up unauthorized rogue AP with high-power antennas using the SAME SSID as the target network, transmitting a STRONGER beacon signal than legitimate APs so NICs connect to it automatically.

---

### 📵 Availability Attacks (Table — CRITICAL)

| Type of Attack | Description | Method/Tools |
| --- | --- | --- |
| **Access Point Theft** | Physically removing an AP | Stealth and/or speed |
| **Disassociation Attacks** | Destroying connectivity between AP and client | Destruction of connectivity |
| **EAP-Failure** | Observing valid 802.1X EAP exchange, sending forged EAP-Failure | Airtool Pi |
| **Beacon Flood** | Generating thousands of counterfeit 802.11 beacons | — |
| **Denial-of-Service** | Exploiting CSMA/CA clear channel assessment (CCA) to make channel appear busy | Adapter with CW Tx mode |
| **De-authenticate Flood** | Flooding forged de-authenticates/disassociates | AirJack |
| **Routing Attacks** | Distributing routing info within the network | RIP, AODV, DSR with wormhole/sinkhole attacks |
| **Authenticate Flood** | Sending forged authenticates from random MACs to fill AP's association table | AirJack |
| **ARP Cache Poisoning Attacks** | Creating many attack vectors | — |
| **Power Saving Attacks** | Transmitting spoofed TIM/DTIM to a client in power-saving mode | — |
| **TKIP MIC Exploit** | Generating invalid TKIP data to exceed AP's MIC error threshold, suspending WLAN service | — |

---

### 🔐 Authentication Attacks (Table — CRITICAL)

| Type of Attack | Description | Method/Tools |
| --- | --- | --- |
| **PSK Cracking** | Recovering WPA PSK from captured key handshake frames | Cowpatty, Fern Wifi Cracker |
| **LEAP Cracking** | Recovering credentials from captured 802.1X LEAP packets, cracking NT hash | Asleap, THC-LEAPcracker |
| **VPN Login Cracking** | Gaining credentials (PPTP password, IPsec PSK) via brute-force | ike_scan, IKECrack, Anger, THC-pptp-bruter |
| **Domain Login Cracking** | Recovering credentials by cracking NetBIOS password hashes | John the Ripper, L0phtCrack, THC-Hydra |
| **Key Reinstallation Attack (KRACK)** | Exploiting the four-way handshake of WPA2 | Nonce reuse technique |
| **Identity Theft** | Capturing user identities from cleartext 802.1X Identity Response | Packet capturing tools |
| **Shared Key Guessing** | Attempting 802.11 shared key auth with vendor default/cracked WEP keys | WEP cracking tools, Wifite |
| **Password Speculation** | Repeatedly attempting 802.1X auth using captured identity to guess password | Password dictionary |
| **Application Login Theft** | Capturing credentials from cleartext application protocols | Ace Password Sniffer, dsniff, Wi-Jacking Attack |

---

### 🔑 KRACK Attack (CRITICAL — Detailed)

> **Key Reinstallation Attack** exploits flaws in the **4-way handshake** of the WPA2 authentication protocol by **forcing Nonce reuse**.
> 

**Normal 4-way handshake:**

```
Message 1 (ANonce)                    →
Message 2 (Signed SNonce)             ←
Message 3 (Signed ANonce, Encryption Key Installation) →
Message 4 (Acknowledgement)           ←
```

**KRACK attack:** Attacker intercepts Message 3, forces the client to reinstall an already-in-use encryption key by replaying it → resets nonce/replay counters → allows decryption of packets, packet replay/forgery. Works against **all modern protected Wi-Fi networks** (WPA and WPA2, personal and enterprise), affecting ciphers WPA-TKIP, AES-CCMP, and GCMP. Any device (Android, Linux, Windows, Apple, OpenBSD, MediaTek) vulnerable to some variant.

**Steals:** credit card numbers, passwords, chat messages, emails, photos

---

### 📞 aLTEr Attack

> Performed on LTE (4G) devices encrypting data in **AES-CTR mode** (no integrity protection). Attacker installs a **virtual/fake communication tower** between two authentic endpoints — Layer 2 (datalink layer) attack.
> 

**Phases:**

1. **Information-Gathering Phase** — passively gather info via identity mapping, website fingerprinting
2. **Attack Phase** — install fake tower → intercept user input → redirect via spoofed DNS to malicious website → attacker stores user credentials

---

### 🔌 Inter-Chip Privilege Escalation / Wireless Co-Existence Attack

> Exploits vulnerabilities in **combo chips** handling both Bluetooth and Wi-Fi. Attacker leverages one chip (e.g., Bluetooth) to steal data from another (Wi-Fi) or manipulate its traffic — causes privilege escalation at chip boundaries.
> 

---

## 4. Wireless Hacking Methodology

### 🎯 5-Step Methodology (CRITICAL)

```
1. Wi-Fi Discovery
2. Wireless Traffic Analysis
3. Launch of Wireless Attacks
4. Wi-Fi Encryption Cracking
5. Wi-Fi Network Compromising
```

---

### 1️⃣ Wi-Fi Discovery / Wireless Network Footprinting

**Two footprinting methods:**

| Method | Description |
| --- | --- |
| **Passive Footprinting** | Detects AP existence by sniffing packets from airwaves — no connection attempt, no data injection |
| **Active Footprinting** | Wireless device sends probe request with SSID to see if AP responds; empty SSID probe if unknown |

**Tools:** inSSIDer, NetSurveyor, Sparrow-wifi (2.4/5 GHz spectral awareness, integrates HackRF/Ubertooth/GPS)

---

### 2️⃣ Wireless Traffic Analysis

RF monitoring/spectrum analyzing tools: Chanalyzer, AirCheck G3 Pro, Spectraware S1000, RSA306B, RF Explorer 6G, RFXpert, Monics, Signal Hound, FieldSENSE

---

### 3️⃣ Launch of Wireless Attacks

**MAC Spoofing Attack:** Attacker changes device MAC to match a trusted AP. Uses tools like **Technitium MAC Address Changer**, LizardSystems Change MAC Address tool.

```bash
ifconfig wlan0 down
ifconfig wlan0 hw ether 02:25:ab:4c:2a:bc
ifconfig wlan0 up
```

**MITM Attack Steps:**

1. Attacker sniffs victim's wireless parameters (MAC, ESSID/BSSID, channel count)
2. Attacker sends **DEAUTH** request to victim with spoofed source (victim's AP)
3. Victim's computer de-authenticated, searches all channels for new valid AP
4. (Attacker's rogue AP intercepts connection)

**Rogue AP Setup (MANA Toolkit):**

```bash
bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh
```

- Configures phy (wireless interface) and upstream (Internet-connected interface)
- Broadcasts SSID (e.g., "Free Internet") to lure victims

**KARMA Attack:** Uses **hostapd-wpe** to lure victims into connecting to a malicious Wi-Fi network by responding to ALL probe requests (impersonating any network the client has connected to before).

**Post-deauth exploitation:** Use dnsmasq + Python scripts to inject malicious URL, force victim's browser to load it. Fake "router updating" pages can be used to harvest credentials via hidden autocomplete-collecting form fields.

**Jamming Signal Attack:** Attacker sends 2.4 GHz jamming signals to disrupt Wi-Fi in a targeted area.

---

### 4️⃣ Wi-Fi Encryption Cracking

#### WPA/WPA2 Encryption Cracking (4 Techniques)

| Technique | Description |
| --- | --- |
| **WPA PSK** | User-defined password initializes TKIP; not directly crackable (per-packet key) but bruteforceable via dictionary attacks |
| **Offline Attack** | Attacker needs proximity to AP briefly to capture the WPA/WPA2 authentication handshake; cracks keys offline afterward |
| **De-authentication Attack** | Force connected client to disconnect (tool: aireplay); capture re-connect/auth packets containing PMK; dictionary/brute-force crack |
| **Brute-Force WPA Keys** | Use aircrack and aireplay to brute-force WPA keys; compute-intensive — can take hours/days/weeks |

#### Cracking WPA3 Using Aircrack-ng and hashcat — 5 Steps

```bash
# Step 1: Set wireless interface to monitor mode
airmon-ng start <Wireless_Interface>

# Step 2: Capture the handshake
airodump-ng wlan0mon
# or targeted:
airodump-ng --bssid <BSSID> --channel <CH> --write capture wlan0mon

# Step 3: De-authenticate a client to force handshake capture
aireplay-ng --deauth 10 -a <BSSID> -c <Client_MAC> wlan0mon

# Step 4: Convert .cap file to .hccapx format
hcxpcapngtool -o capture.hccapx <capture>.cap
hcxpcapngtool -o capture.hc22000 <capture>.cap

# Step 5: Crack the handshake with hashcat + wordlist
hashcat -m 22000 capture.hccapx </path/to/wordlist.txt>
```

#### WPS PIN Cracking (Reaver)

```bash
reaver -i <monitor-mode interface> -b <BSSID of target AP> -vv
# Example:
reaver -i wlan0mon -b B4:75:0E:89:00:60 -vv
```

Scans all WPS PINs until a match is found, then exploits.

**RFID Cloning Tools:** iCopy-X, RFIDler, RFID Mifare Cloner, Flipper Zero, Boscloner Pro

---

### 5️⃣ Wi-Fi Network Compromising

After cracking encryption, the attacker gains full access to the wireless network resources.

---

## 5. Wireless Attack Countermeasures

### 🛡️ 6 Wireless Security Layers (CRITICAL — Diagram)

```
1. Wireless Signal Security   — RF spectrum, Wireless IDS
2. Connection Security        — Per-packet authentication, centralized encryption
3. Data Protection            — WPA2 and AES
4. Device Security            — Vulnerabilities and patches
5. Network Protection         — Strong authentication
6. End-user Protection        — Stateful per-user firewalls
```

- **Wireless signal security:** Continuous monitoring via **WIDS** (Wireless Intrusion Detection System) — analyzes RF spectrum, generates alarms for unauthorized devices
- **Connection security:** Per-frame/packet authentication protects against MITM
- **Device security:** Vulnerability and patch management
- **Data protection:** WPA3, WPA2, AES encryption algorithms
- **Network protection:** Strong authentication for authorized access only
- **End-user protection:** Personal firewalls prevent file access even if attacker associates with AP

---

### 🚫 Blocking Rogue APs

- Launch a DoS attack on the rogue AP to deny service to new clients
- Block the switch port or physically remove the rogue AP
- Use **WIPS** (Wireless Intrusion Prevention Systems) to continuously monitor and auto-block
- Use **ACLs** to restrict access to known/authorized MAC addresses
- Implement **802.1X authentication**
- Segment the network to isolate critical resources
- Disable broadcasting of open SSIDs
- Maintain a whitelist of authorized MAC addresses

---

### 📋 Best Practices for AP Configuration

- Turn off **WPS** on the router
- Use VLANs or separate SSIDs to segment traffic types
- Adjust transmission power to limit Wi-Fi signal range to required premises
- Turn off unneeded services, close unused ports
- Use built-in router firewall to filter traffic
- Set up separate guest network with restricted access

---

### 📋 Best Practices for SSID Settings

- Use SSID cloaking (hide broadcast)
- Never use SSID, company name, or easy-to-guess strings in passphrases
- Place firewall/packet filter between AP and corporate Intranet
- Limit wireless signal strength to organization bounds
- Implement IPsec over wireless for additional encryption
- Use unique characters/strings in SSID (not manufacturer default)
- Separate SSID for guest users
- Ensure each SSID protected with **WPA3** (or WPA2+AES minimum)
- Periodically change SSIDs and passwords

---

### 📋 Best Practices for Authentication

- Enable **WPA3** for highest security level
- If WPA3 unsupported, use **WPA2 with AES** (avoid WPA or TKIP)

---

### 🔒 KRACK Attack Countermeasures

- Audit IoT devices; don't connect to insecure Wi-Fi routers
- Always enable **HTTPS Everywhere** extension
- Enable **two-factor authentication**
- Use a **VPN**
- Always use **WPA3**
- Disable **fast roaming** and **repeater mode**
- Employ the **EAPOL-key replay counter** — AP recognizes only latest counter value
- Use backup wired connection (Ethernet) when KRACK vulnerability detected
- Use third-party routers with better security patches if ISP router lacks them
- Use **network segmentation**
- Temporarily disable **802.11r** protocol (susceptible to KRACK)
- Use **802.1X authentication** with RADIUS for enterprise networks

---

### 🔒 aLTEr Attack Countermeasures

- Encrypt DNS queries; use only trusted DNS resolvers
- Resolve DNS queries using HTTPS protocol
- Access only HTTPS-connection websites
- Use **DNS over TLS (DoT)** or **DNS over DTLS**
- Implement **RFC 7858/RFC 8310** to prevent DNS spoofing
- Add message authentication code (MAC) to user plane packets
- Use **DNSCrypt** protocol
- (Tool example: Cisco Security Connectors, developed with Apple, encrypts DNS queries via Cisco Umbrella)

---

### 🛠️ Wireless Security Auditing Tools

```
RFProtect | Fern Wifi Cracker | OSWA-Assistant | BoopSuite | Wifite
```

### 🛠️ Wi-Fi IPS

> Automatically scans, detects, and classifies unauthorized wireless access and rogue traffic.
> 

**WatchGuard Wi-Fi Cloud WIPS** — defends against unauthorized devices/rogue APs, prevents evil twins, shuts down DoS attacks with near-zero false positives.

**Cisco Adaptive Wireless IPS** — Wireless Control System for monitoring alarms (rogue APs, mobility services, etc.)

---

## 6. Quick Exam Cheat Sheet

### 📊 WEP/WPA/WPA2/WPA3 Comparison (MEMORIZE — HIGH-YIELD)

```
WEP  : RC4          | 24-bit IV  | 40/104-bit key  | No key mgmt      | CRC-32
WPA  : RC4+TKIP      | 48-bit IV  | 128-bit key     | 4-way handshake  | Michael+CRC-32
WPA2 : AES-CCMP      | 48-bit IV  | 128-bit key     | 4-way handshake  | CBC-MAC
WPA3 : AES-GCMP 256  | Arbitrary  | 192-bit key     | ECDH+ECDSA       | BIP-GMAC-256
```

---

### 📊 5 Categories of Wireless Threats

```
Access Control | Integrity | Confidentiality | Availability | Authentication
```

---

### 🎯 5-Step Wireless Hacking Methodology

```
1. Wi-Fi Discovery → 2. Wireless Traffic Analysis →
3. Launch of Wireless Attacks → 4. Wi-Fi Encryption Cracking →
5. Wi-Fi Network Compromising
```

---

### 🔑 Key Attacks to Remember

```
KRACK              → exploits WPA2 4-way handshake, forces nonce reuse
aLTEr              → fake LTE tower, Layer 2, AES-CTR mode exploit
Evil Twin/Honeypot → rogue AP with same SSID, stronger signal
FMS Attack         → exploits weak WEP IVs
Inter-Chip Priv Esc → combo Bluetooth/Wi-Fi chip exploitation
```

---

### 🛠️ Tool Reference

| Purpose | Tool |
| --- | --- |
| WPA3 cracking | aircrack-ng + hashcat (-m 22000) |
| WPS PIN cracking | Reaver |
| Rogue AP creation | MANA toolkit |
| KARMA attack | hostapd-wpe |
| MAC spoofing | Technitium MAC Address Changer, SMAC |
| WEP cracking | Fern Wifi Cracker, aircrack-ng |
| WPA PSK cracking | Cowpatty, Fern Wifi Cracker |
| LEAP cracking | Asleap, THC-LEAPcracker |
| RFID cloning | iCopy-X, Flipper Zero |
| Wi-Fi discovery | inSSIDer, Sparrow-wifi |

---

### 🔥 Common Exam Scenarios

**Q: What encryption algorithm does WPA3 use?**
→ **AES-GCMP 256**

**Q: What integrity check mechanism does WPA2 use?**
→ **CBC-MAC**

**Q: What is the IV size for WEP?**
→ **24-bits**

**Q: What key management method do WPA and WPA2 both use?**
→ **4-way handshake**

**Q: What attack exploits the WPA2 4-way handshake by forcing nonce reuse?**
→ **KRACK (Key Reinstallation Attack)**

**Q: What are the 5 categories of wireless threats?**
→ **Access Control, Integrity, Confidentiality, Availability, Authentication attacks**

**Q: What tool is used to crack WPS PINs?**
→ **Reaver**

**Q: What hashcat mode number is used for WPA/WPA2/WPA3 handshake cracking?**
→ **-m 22000**

**Q: What attack sets up a rogue AP with the same SSID and a stronger signal than the legitimate AP?**
→ **Honeypot AP** (a form of Evil Twin)

**Q: What frequency does 802.11b operate on, and what modulation does it use?**
→ **2.4 GHz, DSSS**

**Q: What frequency and modulation does 802.11a use?**
→ **5 GHz, OFDM**

**Q: What attack exploits weak WEP IVs to crack the key?**
→ **FMS (Fluhrer-Mantin-Shamir) attack**

**Q: What toolkit is used to set up a rogue AP with NAT?**
→ **MANA toolkit** (`start-nat-simple.sh`)

**Q: What are the 6 wireless security layers?**
→ **Wireless Signal Security, Connection Security, Data Protection, Device Security, Network Protection, End-user Protection**

**Q: What attack targets LTE/4G devices using a fake communication tower at Layer 2?**
→ **aLTEr attack**

**Q: What's the recommended encryption if WPA3 isn't supported?**
→ **WPA2 with AES** (avoid WPA or TKIP)

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 16*