# Chapter 11 - Wireless Security

Wireless networks use **radio waves** to transmit data, which means the signal **extends beyond the intended area**. A transmitter sends a signal through the air, and **any receiver within range** can pick it up — there's no physical boundary like a cable.

This is the fundamental security problem: wireless traffic can be **intercepted by anyone in range** of the transmitter, without any physical access to the network.

## Standards

**WiFi** (Wireless Fidelity) = a set of specifications categorised under **802.11**, managed by the **IEEE** (Institute of Electrical and Electronics Engineers).

802.11 is not the only wireless specification — **Bluetooth** is another (short-range, personal area networks), along with others like **Zigbee** (IoT), **NFC** (near-field communication), and **cellular** (4G/5G).

# Wi-Fi

## Standards

Wi-Fi == **802.11** == network connectivity over a wireless connection. The standards fall under the **IEEE's 802.11** family and specify the **Physical and Data Link layers** — modulation schemes, frequency spectrum, and data rates.

The frequencies used fall in the **ISM band** (Industry, Scientific and Medical — unlicensed).

### Versions

| Version | Frequency | Notes |
| --- | --- | --- |
| **802.11** (original) | 2.4 GHz | 1–2 Mbps. Crowded band, lots of interference |
| **802.11a** | 5 GHz | Not widely adopted in consumer space |
| **802.11b/g** | 2.4 GHz | Widely deployed |
| **802.11n** (Wi-Fi 4) | 2.4 + 5 GHz | Introduced **MIMO** (Multiple Input Multiple Output) |
| **802.11ac** (Wi-Fi 5) | 5 GHz | Faster, more channels |
| **802.11ax** (Wi-Fi 6/6E) | 2.4 + 5 + 6 GHz | Better in dense environments |
| Future | 60 GHz | 6 channels available in that range |

**Backwards compatibility issue:** older devices built for 2.4 GHz can't connect to a 5 GHz-only network.

### Channels

- **2.4 GHz:** 11 channels in the US (up to 14 in other countries). A **channel** = a bounded range of frequencies for transmission. Only channels **1, 6 and 11** don't overlap — using others causes interference
- **5 GHz:** more channels available, varies by country — less crowding
- **60 GHz:** 6 channels

**Range** varies by version — and by the environment. Concrete walls block signal significantly, brick and masonry offer strong resistance, but **glass (windows)** lets signal pass through.

## Wi-Fi Network Types

| Type | How it works |
| --- | --- |
| **Ad hoc** | No central device — a **dynamic mesh** where each device communicates directly with others. All devices must be within range of each other. Easy to set up, fast, but **hard to manage** and **no encryption** by default. Less common in business |
| **Infrastructure** | A central device (**access point**) acts like a switch — all messages go through it. Computers don't talk to each other directly. The standard enterprise setup |

**Switch vs access point:** a switch controls electrical signals on a wire; an AP controls radio signals in the air. Same concept, different medium.

**Monitor mode:** instead of only seeing traffic addressed to you, your interface captures **all** wireless frames in the air — beacons, probes, authentication, data. Essential for sniffing and attacks.

**Beacons and probes:**

- The AP sends **beacon frames** periodically to announce its presence
- Clients send **probe requests** to discover networks in the area

## Wi-Fi Authentication

1. The client sends **probe requests** looking for networks, identified by **SSID** (Service Set Identifier — the network name)
2. Multiple APs can share the same SSID — they're distinguished by the **BSSID** (Basic Service Set Identifier — the AP's **MAC address**)
3. The client connects to the AP and must **authenticate**
4. After authentication, the client must be **associated** — it sends its capabilities (version, speed), and the AP and station **negotiate** how they'll communicate
5. **User-level authentication** may also be required (username/password via 802.1X)

**802.1X** — an IEEE standard for **network-level authentication**. Not just wireless — also used on wired networks to prevent rogue devices. Uses a **RADIUS** server as the backend.

## Wi-Fi Encryption

### WEP (Wired Equivalent Privacy)

Created to address the privacy concern of wireless. Uses the **RC4** algorithm with a **Pre-Shared Key (PSK)**.

**Fatal flaw:** the **initialisation vector (IV)** was not truly random and was only 24 bits — with enough captured data, the key can be determined. WEP encryption was **cracked**.

Uses a **CRC** (Cyclic Redundancy Check) for message integrity — which is also weak (CRC is for error detection, not security).

### WPA (Wi-Fi Protected Access)

Released as a **stopgap** after WEP was broken. Could be implemented with just a **firmware upgrade** — no hardware changes needed.

**Key improvement:** introduced **TKIP** (Temporal Key Integrity Protocol) — mixes the keys differently. The same RC4 algorithm is used, but with a **per-packet key** instead of a session key. Each packet has its own encryption/decryption key.

**Integrity improvement:** replaced CRC with a **MIC** (Message Integrity Check), also called a **MAC** (Message Authentication Code). Verifies both the **integrity and authenticity** of the message — the receiver calculates its own MAC and compares it to the one received. Match = message hasn't been tampered with.

**Two authentication modes:**

- **WPA-Personal** — PSK method. The password also provides the key used with the IV to generate the encryption key for RC4
- **WPA-Enterprise** — uses **EAP** (Extensible Authentication Protocol) with backend systems like Active Directory

**WPS** (Wi-Fi Protected Setup) — a convenience feature: push a button on the AP or enter a **PIN** to connect without typing the full password. **Insecure** — the PIN can be **recovered** (it's checked in two halves, reducing the brute-force space dramatically).

### WPA2 (Wi-Fi Protected Access 2)

Ratified as **IEEE 802.11i-2004**. The intended **long-term replacement** for WEP.

**Key changes:**

- Replaced RC4 with **AES-CCMP** (Counter Mode with CBC-MAC Protocol) — a **block cipher**, much stronger
- Introduced a **4-way handshake** to improve key management
- Handles MIMO streams, different channels and bandwidths

**Keys in WPA2:**

| Key | Role |
| --- | --- |
| **PMK** (Pairwise Master Key) | Retained over time, must be protected |
| **PTK** (Pairwise Transient Key) | Derived from the PMK for the session |
| **GTK** (Group Temporal Key) | Used for broadcast/multicast — shared across devices |

**The 4-way handshake:**

1. AP generates a **nonce** + key replay counter → sends to station
2. Station constructs the **PTK**, generates its own nonce → sends back with the replay counter and a **MIC**
3. AP derives its own PTK → sends the **GTK** + MIC
4. Station sends an **acknowledgement**

**Authentication modes:**

- **Personal** — PSK
- **Enterprise** — **802.1X** for user-level authentication (username/password via RADIUS)

**EAP variants:**

- **LEAP** (Lightweight EAP) — Cisco proprietary, now considered weak
- **PEAP** (Protected EAP) — wraps EAP in a TLS tunnel
- **EAP-TLS** — mutual certificate-based authentication (strongest)

All ensure authentication and key exchange happen over a **protected channel** (typically TLS).

### WPA3 (Wi-Fi Protected Access 3)

**Key improvements:**

- Longer encryption keys: **AES-256** with **SHA-384** for message authentication
- Introduced **SAE** (Simultaneous Authentication of Equals) — eliminates the pre-shared key vulnerability
- Resistant to **offline dictionary attacks** (even if the handshake is captured)
- **Forward secrecy** — past sessions can't be decrypted even if the password is later compromised

**SAE process:**

1. Station sends probe request → AP responds
2. Both sides exchange **authentication commit** messages → each creates its own **PMK**
3. Both sides exchange **authentication confirm** messages → each validates the other's key
4. Regular association request/response
5. **4-way handshake** using the PMK (same as WPA2, but the PMK was derived securely via SAE)

Continues to mandate **CCMP** — a set of cryptographic protocols using a **block cipher** (messages encrypted in fixed-length blocks).

### Comparison table

|  | **WEP** | **WPA** | **WPA2** | **WPA3** |
| --- | --- | --- | --- | --- |
| **Encryption** | RC4 (stream) | RC4 + TKIP (per-packet key) | **AES-CCMP** (block) | **AES-256-CCMP** |
| **Key management** | Static PSK + 24-bit IV | TKIP mixes keys per packet | 4-way handshake (PMK → PTK + GTK) | **SAE** + 4-way handshake |
| **Authentication** | PSK only | PSK or Enterprise (EAP) | PSK or Enterprise (802.1X/EAP) | SAE (Personal) or Enterprise |
| **Integrity** | CRC (weak) | MIC/MAC | MIC/MAC | MIC/MAC + SHA-384 |
| **Main weakness** | IV reuse → key recovery. Trivially cracked | TKIP still uses RC4; vulnerable to certain attacks | **KRACK** (Key Reinstallation Attack) | Still new, limited adoption |
| **Status** | Broken — never use | Deprecated | Still widely used | Current standard |

## BYOD (Bring Your Own Device)

Companies create **policies** telling people how to use personal devices on the enterprise network.

- May require **approval** or **minimum requirements** (OS version, encryption, MDM agent)
- Without **NAC** (Network Access Control), you can't restrict who connects to the wireless — but you can **block MAC addresses** (easily spoofed)
- Best practice: **isolate** the wireless network so it behaves as if users are **outside** the enterprise. To access internal resources, everyone (even authenticated users) connects through a **VPN**
- Common to see a **Guest network** — an isolated network for untrusted users, requiring a VPN for anything internal

## Wi-Fi Attacks

Start with **reconnaissance** — tools like Wireshark. To see traffic, you sometimes need to **force endpoints to reauthenticate** via a deauthentication attack, evil twin, or key reinstallation attack.

### Wireless Footprinting

Identifying wireless networks in an area and understanding their boundaries — called **war-driving**.

- **Tools:** Kismet (Linux), WiFi Explorer (macOS)
- **Signal strength** gives a sense of distance from the AP
- **Antenna matters:** an **omnidirectional** antenna sends/receives in all directions but disperses signal strength; a **directional** antenna concentrates on one direction — better range but must be aimed

### Sniffing

Wireshark captures traffic with **radio headers** (important for signal strength, channel, etc.).

To see beacons, probes and authentication messages, set your interface to **monitor mode**:

```
airmon-ng start wlan0
```

Capture all traffic on the monitor interface:

```
tcpdump -i wlan0mon
tcpdump -w radio.pcap -i wlan0mon
```

Open the saved capture in Wireshark for analysis.

**Signal strength** is measured in **dBm** (decibels relative to a milliwatt):

| dBm | Quality |
| --- | --- |
| **-10** | Best possible |
| **-19 to -67** | Usable range |
| **-100** | Lowest detectable |
| **Beyond -100** | Not usable |

**Tip:** specify the **channel** before capturing — otherwise you may miss packets as the interface hops between channels.

**airodump-ng** — a more focused wireless capture tool:

```
sudo airodump-ng wlan1mon
sudo airodump-ng --bssid 64:66:B3:6E:B0:8A -c 11 wlan1mon -w output
```

### Deauthentication Attack

Sends management frames that **force stations to disconnect and reauthenticate** against the AP.

**Why you'd do it:**

- Capture the **ESSID** (Extended Service Set Identifier) when it's not broadcast
- Capture the **handshake** during reassociation (needed to crack the key)
- For WEP: force traffic to retrieve the encryption key

```
sudo aireplay-ng --deauth 10 -a 01:02:ab:03:04:ff -c 10:03:cd:04:06:fe wlan0mon
```

- `a` = BSSID of the access point
- `c` = MAC of the target station (omit to deauth **everyone**)
- `10` = number of deauth frames to send

### Evil Twin

A **rogue access point** configured to look like a legitimate one — advertising a **known SSID**. Goal: capture **authentication information or the PSK** to gain access to the enterprise network, or intercept unencrypted web traffic.

**Tools:**

- **wifiphisher** — impersonates a wireless network while jamming the legitimate AP, then redirects traffic to a site you manage
- **airgeddon** (Linux) — multiple wireless attacks: evil twin + sniffing + sslstrip. Requires **2 wireless interfaces** (one for the fake AP, one for DoS against the real AP). Pools together hostapd, DHCP server, lightweight HTTP server, DNSSpoof. Can also use **BeEF** (Browser Exploitation Framework) to exploit browser vulnerabilities
- **bettercap** — spoofing attacks

### Key Reinstallation Attack (KRACK)

Targets the **WPA2 4-way handshake**. The session key is created during the handshake and used for the duration of the connection.

**The flaw:** the **third message** of the handshake can be **resent multiple times**. If the attacker forces a **nonce reuse**, the encryption key becomes known — the attacker can decrypt traffic.

If the nonce can be replayed, any key creation is vulnerable — including the **GTK**, which exposes **broadcast and multicast** traffic on the network.

### mdk3/4

A tool for **beacon flooding, authentication DoS, deauthentication, and other wireless tests**. Requires monitor mode.

```
sudo mdk4 wlan0mon a                          # attack all SSIDs
sudo mdk4 wlan0mon p -e WifiName              # probe for a specific network
```

---

# Cheatsheet

**Encryption comparison**

|  | WEP | WPA | WPA2 | WPA3 |
| --- | --- | --- | --- | --- |
| Cipher | RC4 | RC4 + TKIP | **AES-CCMP** | **AES-256** |
| Key | Static PSK | Per-packet | 4-way handshake | **SAE** |
| Attack | IV reuse | TKIP weaknesses | **KRACK** | Limited |

**Key terms**

| Term | Meaning |
| --- | --- |
| SSID | Network name |
| BSSID | AP's MAC address |
| ESSID | Extended SSID (multi-AP) |
| MIMO | Multiple Input Multiple Output |
| SAE | WPA3 key agreement (no PSK vulnerability) |
| KRACK | Key Reinstallation Attack (WPA2) |
| TKIP | WPA's per-packet key protocol |
| CCMP | AES-based encryption protocol |
| PMK / PTK / GTK | Master / Transient / Group keys |
| WPS | Push-button/PIN setup — PIN is crackable |
| dBm | Signal strength unit (-10 best, -100 lowest) |

**Tools & commands**

| Tool | Purpose | Command |
| --- | --- | --- |
| **airmon-ng** | Enable monitor mode | `airmon-ng start wlan0` |
| **airodump-ng** | Wireless capture / reconnaissance | `sudo airodump-ng wlan1mon` |
| **aireplay-ng** | Deauthentication attack | `sudo aireplay-ng --deauth 10 -a <BSSID> -c <MAC> wlan0mon` |
| **aircrack-ng** | Crack captured keys | `aircrack-ng capture.cap -w wordlist.txt` |
| **wifiphisher** | Evil twin + phishing | `wifiphisher` |
| **airgeddon** | Multi-attack tool (evil twin, sniffing, sslstrip) | `sudo bash airgeddon.sh` |
| **bettercap** | Spoofing attacks | `bettercap` |
| **mdk4** | Beacon flood, auth DoS, deauth | `sudo mdk4 wlan0mon a` |
| **Kismet** | Wireless footprinting / war-driving | `kismet` |
| **tcpdump** | Capture wireless traffic | `tcpdump -i wlan0mon -w radio.pcap` |

**Exam reflexes**

- WEP cracked because of **24-bit IV reuse** · WPA used **TKIP** as a stopgap · WPA2 = **AES-CCMP** · WPA3 = **SAE**
- **WPS PIN** is crackable (checked in two halves)
- Deauth needs **BSSID** of the AP (+ optionally target MAC)
- Evil twin requires **2 wireless interfaces** (one fake AP, one for DoS)
- KRACK exploits **nonce reuse** in the WPA2 4-way handshake
- Monitor mode = `airmon-ng start wlan0` → interface becomes `wlan0mon`
- Non-overlapping 2.4 GHz channels: **1, 6, 11**

# Bluetooth

Peripherals have gone wireless — two devices that would normally communicate over a wire can use **Bluetooth**, a wireless protocol for **short-range communication** between two devices.

Bluetooth operates in the **ISM band at 2.4 GHz** (same frequency range as Wi-Fi — they can interfere with each other).

## Range

| Class | Range |
| --- | --- |
| **Class A** | Up to **100 metres** |
| **Class B** | Up to **10 metres** (the most common) |
| **Class C** | Up to **1 metre** |

Most Bluetooth attacks require **close physical proximity** to the target.

## Profiles and capabilities

Bluetooth capabilities depend on **profiles** — each device implements a set of profiles based on its requirements (audio, file transfer, input device, serial port, etc.). When two devices **pair**, they share their capabilities by telling each other **which profiles they support**.

## Pairing

**Pairing** = creating a **bond** between two devices. The pairing request is verified — originally with a **4-digit PIN**, but devices with no input capabilities (headphones, speakers) have a PIN **hard-coded** (often `0000` or `1234`).

**Bluetooth v2.1** introduced **Secure Simple Pairing (SSP)** with 4 mechanisms:

| Mechanism | How it works |
| --- | --- |
| **Just Works** | Pairing without user interaction — no verification. Least secure |
| **Numeric Comparison** | Both devices display a **6-digit number** — if they match, the user confirms on both sides |
| **Passkey Entry** | One device has input but no output — the user enters a passkey displayed on the other device |
| **Out of Band (OOB)** | Uses another channel (like **NFC** on a smartphone held close to the device) to complete the pairing |

## Scanning

**btscanner** — a tool for discovering Bluetooth devices with two scan modes:

| Mode | How it works |
| --- | --- |
| **Inquiry scan** | Passive — listens for inquiries from other devices across **32 channels** |
| **Brute force scan** | Active — sends messages to devices to determine what they are. Requires **MAC addresses**. Won't reveal which profiles the device supports |

## Bluetooth Attacks

All require **close proximity** to the target (within Bluetooth range).

### Bluejacking

**Sending data to a device** without going through the pairing process — or without the recipient knowing about it. Uses the **OBEX** (Object Exchange) protocol to push a message or picture from one device to another.

**Impact:** mostly annoyance — unsolicited messages appear on the victim's device. No data is stolen.

### Bluesnarfing

**Getting data from a device** — gain access and **extract data** (contacts, messages, calendar, files).

Works over the **OBEX** protocol. If there's an **FTP server running over OBEX**, it's possible to access the remote system **without authentication** because of the **OBEX Push** service.

**Long-distance bluesnarfing** = the same attack from a **greater distance** using a high-gain directional antenna.

### Bluebugging

Uses Bluetooth to **gain access to a phone** in order to **place a phone call**. The attacker sets up a remote listening device. The attacker needs to be in **close proximity** to initiate, but once the call is placed, it continues **regardless of distance**.

**Remediation:** the victim can **hang up** the call.

### Bluedump

An attack that **tricks a target device into trusting the attacker's device**. The attacker needs to know the **BDADDR** (Bluetooth Device Address — the hardware address) of a device the target already trusts.

**Process:**

1. The attacker **spoofs** the trusted device's BDADDR in a connection attempt
2. The target responds with an **authentication request**
3. The attacker responds with an **HCI_Link_Key_Request_Negative_Reply** message
4. The target **deletes the existing key** for the spoofed device
5. The target enters **pairing mode** — the attacker can now pair as the trusted device

### Bluesmack

A **denial-of-service** attack. The attacker uses the **L2CAP** (Logical Link Control and Adaptation Protocol) — which handles connections and measures round-trip time between devices.

The attacker **manipulates the size of the packets** being sent (like an oversized ping). With the right size, the target device becomes **unusable**.

---

# Cheatsheet

**Bluetooth attacks**

| Attack | What it does | Protocol |
| --- | --- | --- |
| **Bluejacking** | **Send** unsolicited data (messages/pictures) | OBEX |
| **Bluesnarfing** | **Extract** data from a device (contacts, files) | OBEX / FTP over OBEX |
| **Bluebugging** | **Control** the phone — place calls, eavesdrop | AT commands |
| **Bluedump** | **Trick** the target into trusting the attacker | Spoofed BDADDR + HCI |
| **Bluesmack** | **DoS** — oversized packets crash the device | L2CAP |

**Key terms**

| Term | Meaning |
| --- | --- |
| **ISM band** | 2.4 GHz — shared with Wi-Fi |
| **BDADDR** | Bluetooth Device Address (hardware MAC) |
| **OBEX** | Object Exchange protocol (file/data transfer) |
| **L2CAP** | Logical Link Control and Adaptation Protocol |
| **SSP** | Secure Simple Pairing (v2.1+) |
| **Profile** | Set of capabilities a device supports |

**Tools**

| Tool | Purpose |
| --- | --- |
| **btscanner** | Discover Bluetooth devices (inquiry scan + brute force scan) |

**Exam reflexes**

- "Send data without pairing" → **bluejacking** (OBEX)
- "Steal data from a device" → **bluesnarfing** (OBEX/FTP)
- "Place a call on victim's phone" → **bluebugging**
- "Force re-pairing by spoofing a trusted address" → **bluedump**
- "DoS via oversized packets" → **bluesmack** (L2CAP)
- OBEX Push = why bluesnarfing works **without authentication**
- All attacks require **close proximity** (except long-distance bluesnarfing with a directional antenna)

# Mobile Devices

Two main platforms for mobile devices:

**Android** — software developed and managed by **Google**, but the hardware is made by **numerous manufacturers**. Google provides the source code to vendors, and each vendor may implement it differently — altering the code, adding software and features. This makes Android **fragmented** — many versions, many customisations, inconsistent patch levels. Runs on smartphones, tablets and watches.

**iOS** — developed by **Apple** for the iPhone and iPad. A stripped-down version of **macOS**. Apple pushes users hard to stay on the **latest version**, which means less fragmentation and faster security patch adoption.

## App marketplaces

The main attack vector for mobile devices is through **applications**.

| Platform | Official store | Third-party stores |
| --- | --- | --- |
| **Android** | Google Play Store | Can be configured — users can **sideload** apps from other sources |
| **iOS** | Apple App Store | Could not install other marketplaces without **jailbreaking** (breaking the OS's restrictions). Recent regulation in some regions is changing this |

**Vetting** = passing software through **tests and analysis** before it's allowed in the store. Apple's review process is stricter; Google Play has improved but still lets more through.

## Mobile Device Attacks

| Attack vector | How it works |
| --- | --- |
| **Third-party / malicious apps** | The best way to compromise a mobile device — install an app that looks legitimate but contains malware or exfiltrates data |
| **Rogue network access** | Setting up a fake Wi-Fi network in **public places** (cafés, airports) — mobile devices often auto-connect to known SSIDs |
| **Phishing / SMiShing** | Phishing via email or **SMS** — mobile users are more likely to tap links on small screens without checking the URL |
| **Malware** | Trojans, spyware, ransomware delivered via apps, links or drive-by downloads |
| **Bad programming** | Poor encryption implementation, bad session handling, insecure data storage by app developers — not an attack by an external actor, but a vulnerability created by the developer |

**Additional threats worth knowing:**

- **Jailbreaking (iOS) / Rooting (Android)** — removes OS restrictions, gives full control but also **disables built-in security protections**
- **MDM bypass** — circumventing Mobile Device Management controls
- **SIM swapping** — social engineering the carrier to transfer the victim's number to the attacker's SIM → intercepts SMS-based MFA

---

# Cheatsheet

**Platforms**

|  | Android | iOS |
| --- | --- | --- |
| Developer | Google (open source) | Apple (closed) |
| Fragmentation | High (many vendors, many versions) | Low (Apple controls updates) |
| Sideloading | Allowed | Requires jailbreak |
| Main store | Google Play | App Store |

**Attack vectors:** malicious apps · rogue Wi-Fi · phishing/SMiShing · malware · bad developer practices (weak encryption, insecure session handling)

**Key terms**

- **Jailbreaking** = removing iOS restrictions
- **Rooting** = gaining root access on Android
- **Sideloading** = installing apps from outside the official store
- **Vetting** = testing and analysing apps before store approval
- **SMiShing** = phishing via SMS

**Exam reflexes**

- "Best attack vector for mobile?" → **malicious applications**
- "Android fragmentation" → many vendors + versions = inconsistent patching
- "Installing apps outside the App Store on iOS?" → requires **jailbreaking**
- Mobile phishing via SMS → **SMiShing**