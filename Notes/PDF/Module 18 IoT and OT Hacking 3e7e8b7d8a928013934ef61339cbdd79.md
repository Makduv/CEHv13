# Module 18: IoT and OT Hacking

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [IoT Concepts and Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-iot-concepts-and-attacks)
2. [IoT Hacking Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-iot-hacking-methodology)
3. [IoT Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-iot-attack-countermeasures)
4. [OT Concepts and Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-ot-concepts-and-attacks)
5. [OT Hacking Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-ot-hacking-methodology)
6. [OT Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-ot-attack-countermeasures)
7. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-quick-exam-cheat-sheet)

---

## 1. IoT Concepts and Attacks

### 🎯 What is the IoT?

> **Internet of Things (IoT)**, also known as **Internet of Everything (IoE)**, refers to computing devices that are web-enabled with the capability of sensing, collecting, and sending data using embedded sensors and communication hardware. A "thing" refers to a device implanted in a natural, human-made, or machine-made object with the functionality of communicating over a network.
> 

**How IoT Works — 4 primary systems:**

```
IoT Devices → IoT Gateway → Internet → Data Storage/Cloud Server
                                      → Remote Control using Mobile App
```

---

### 📊 IoT Technologies and Protocols (CRITICAL TABLE)

| Category | Examples |
| --- | --- |
| **Short-Range Wireless** | BLE, Li-Fi, NFC, QR Codes/Barcodes, RFID, Thread, Wi-Fi, Wi-Fi Direct, Z-wave, ZigBee, ANT |
| **Medium-Range Wireless** | Ha-Low, LTE-Advanced, 6LoWPAN, QUIC |
| **Long-Range Wireless** | LPWAN (LoRaWAN, Sigfox, Neul), VSAT, Cellular, MQTT, NB-IoT |
| **Wired Communication** | Ethernet, MoCA, Power-line Communication (PLC) |
| **IoT Operating Systems** | Windows 10 IoT, Amazon FreeRTOS, Fuchsia, RIOT, Ubuntu Core, ARM Mbed OS, Zephyr, Embedded Linux, NuttX RTOS, Integrity RTOS, Apache Mynewt, Tizen |
| **IoT Application Protocols** | CoAP, Edge, LWM2M, Physical Web, XMPP, Mihini/M3DA |

---

### 📊 4 IoT Communication Models

```
1. Device-to-Device      → devices interact directly (ZigBee, Z-Wave, Bluetooth)
                            e.g., smart home: thermostats, light bulbs, door locks
2. Device-to-Cloud       → device communicates with cloud directly (Wi-Fi/Ethernet/Cellular)
                            e.g., CCTV camera accessed remotely via cloud
3. Device-to-Gateway     → device communicates via intermediate gateway (smartphone/hub)
                            protocols: ZigBee, Z-Wave
4. Backend Data-Sharing  → extends device-to-cloud to allow data access/analysis by 3rd parties
```

---

### 🏗️ Security Problems in IoT Architecture (CRITICAL)

```
Application + Network + Mobile + Cloud = IoT

Application: Validation of input string, AuthN, AuthZ, no automatic security updates, default passwords
Network:     Firewall, improper communications encryption, services, lack of automatic updates
Mobile:      Insecure API, lack of communication channels encryption, authentication, lack of storage security
Cloud:       Improper authentication, no encryption for storage/communications, insecure web interface
```

---

### 📊 18 OWASP IoT Attack Surface Areas (CRITICAL)

```
1.  Ecosystem (general)              10. Third-party Backend APIs
2.  Device Memory                    11. Update Mechanism
3.  Device Physical Interfaces       12. Mobile Application
4.  Device Web Interface             13. Vendor Backend APIs
5.  Device Firmware                  14. Ecosystem Communication
6.  Device Network Services          15. Network Traffic
7.  Administrative Interface         16. Authentication/Authorization
8.  Local Data Storage               17. Privacy
9.  Cloud Web Interface              18. Hardware (Sensors)
```

**Device Physical Interfaces vulnerabilities:** Firmware extraction, User CLI, Admin CLI, Privilege escalation, Reset to insecure state, Removal of storage media, Tamper resistance, Debug port (UART/Serial, JTAG/SWD), Device ID/serial number exposure.

---

### 📊 17 OWASP IoT Vulnerabilities (CRITICAL TABLE)

```
1.  Username Enumeration            10. Removal of Storage Media
2.  Weak Passwords                  11. No Manual Update Mechanism
3.  Account Lockout                 12. Missing Update Mechanism
4.  Unencrypted Services            13. Firmware Version Display/Last Update Date
5.  Two-factor Authentication (lack)14. Firmware and Storage Extraction
6.  Poorly Implemented Encryption   15. Manipulating the Code Execution Flow
7.  Update Sent Without Encryption  16. Obtaining Console Access
8.  Update Location Writable        17. Insecure Third-party Components
9.  Denial of Service
```

---

### 📊 21 IoT Threats (CRITICAL)

```
1.  DDoS Attack                     12. Forged Malicious Device
2.  Attack on HVAC Systems          13. Side Channel Attack
3.  Rolling Code Attack             14. Ransomware
4.  BlueBorne Attack                15. Client Impersonation
5.  Jamming Attack                  16. SQL Injection Attack
6.  Remote Access using Backdoor    17. SDR-Based Attack
7.  Remote Access using Telnet      18. Fault Injection Attack
8.  Sybil Attack                    19. Network Pivoting
9.  Exploit Kits                    20. DNS Rebinding Attack
10. Man-in-the-Middle Attack        21. Firmware Update (FOTA) Attack
11. Replay Attack
```

**Key definitions:**

- **DDoS Attack:** Attacker gains remote access → injects malware to turn devices into botnets → C&C instructs botnets → target server floods/goes offline
- **Rolling Code Attack:** Attacker jams and sniffs the signal to obtain the code transferred to a vehicle's receiver, then uses it to unlock/steal the vehicle
- **BlueBorne Attack:** Attackers connect to nearby devices and exploit Bluetooth protocol vulnerabilities
- **Jamming Attack:** Attacker jams the signal between sender/receiver with malicious traffic, making endpoints unable to communicate
- **Cryptanalysis Attack:** Same procedure as replay attack + reverse-engineering the protocol to obtain the original signal
- **Reconnaissance Attack:** Obtains info from device specifications; uses multimeters to investigate chipset for product ID discovery

---

## 2. IoT Hacking Methodology

### 📊 IoT Hacking Methodology Phases

```
1. Information Gathering
2. Vulnerability Scanning
3. Launch Attacks
4. Gain Remote Access
5. Maintain Access (implied)
```

---

### 1️⃣ Information Gathering

**Tool: Shodan** — search engine providing info about Internet-connected devices (routers, traffic lights, CCTV cameras, servers, smart home devices).

```
webcamxp country:US        # webcams in US
webcamxp city:paris        # webcams in Paris
webcamxp geo:-50.81,201.80 # webcams by lat/long
```

**Other tools:** MultiPing, FCC ID Search, Censys, FOFA

**MultiPing scanning steps:**

```
File → Add Address Range → set Initial Address, Number of addresses=255 → OK
```

---

### 2️⃣ Vulnerability Scanning

> Identifies vulnerabilities in IoT devices/networks and determines how they can be exploited.
> 

**Tool: beSTORM** — smart fuzzer detecting buffer overflow vulnerabilities via automated protocol-based fuzzing; automated black-box auditing tool.

---

### 3️⃣ Launch Attacks

**Rolling Code Attack using RFCrack:**

```bash
python RFCrack.py -i                              # Live Replay
python RFCrack.py -r -M MOD_2FSK -F 314350000     # Rolling Code
python RFCrack.py -j -F 314000000                 # Jamming
```

**Replay Attack using HackRF One:**

```bash
hackrf_transfer -r connector.raw -f [device frequency]   # Step 1: Record signal
```

> Attackers use FCC database or RTL-SDR to determine target device frequency first.
> 

**KillerBee** — attacks ZigBee and IEEE 802.15.4 networks.

---

### 4️⃣ Gain Access / Gain Remote Access

**Identifying IoT Communication Buses and Interfaces:** Attackers identify serial/parallel interfaces — **UART, SPI, JTAG, I2C** — to gain shell access, extract firmware. Tools: BUS Auditor (16 channels CH0-CH15), Damn Insecure and Vulnerable Application (DIVA).

**Gaining Remote Access using Telnet:**

- Attacker performs port scanning to find open Telnet ports
- Many embedded systems (ICS, routers, VoIP phones, TVs) implement remote access via Telnet
- If auth required: try default credentials (root/root, system/system) or brute-force

**Additional IoT hacking tools:** CatSniffer, Cascoda Packet Sniffer, KillerBee, JTAGULATOR, wiz_exploit, PENIOT, RouterSploit

---

## 3. IoT Attack Countermeasures

### 🛡️ How to Defend Against IoT Hacking (14-Point Checklist — CRITICAL)

1. Disable the "guest" and "demo" user accounts if enabled
2. Use the "Lock Out" feature to lock accounts after excessive invalid login attempts
3. Implement strong authentication mechanisms
4. Locate control system networks/devices behind firewalls; isolate from business network
5. Implement IPS and IDS in the network
6. Implement end-to-end encryption; use Public Key Infrastructure (PKI)
7. Use VPN architecture for secure communication
8. Deploy security as a unified, integrated system
9. Allow only trusted IP addresses to access the device from the Internet
10. Disable telnet (port 23)
11. Disable the UPnP port on routers
12. Protect devices against physical tampering
13. Patch vulnerabilities and update device firmware regularly
14. Monitor traffic on port 48101, as infected devices attempt to spread malicious files using port 48101

---

### 📊 12 Secure Development Practices for IoT Applications

```
1. Ensure Secure Boot                6. Secure Firmware/Software Updates
2. Secure API Endpoints              7. Ensure Device Identity Management
3. Implement Threat Modeling         8. Implement Hardware Security
4. Secure Coding Practices           9. Allow Code Signing
5. Conduct Security Testing          10. Implement Runtime Protection
                                      11. Ensure Secure Cloud Integration
                                      12. Utilize Secure Communication Protocols
```

**IoT Device Management Solutions:** Azure IoT Central, Oracle Fusion Cloud IoT, Golioth, AWS IoT Device Management, IBM Watson IoT Platform, openBalena

---

## 4. OT Concepts and Attacks

### 🎯 What is OT?

> **Operational Technology (OT)** is a combination of hardware and software used to monitor, run, and control industrial process assets. It drives collections of devices designed to work together as an integrated/homogeneous system (e.g., electrical grid telecommunications).
> 

---

### 🏗️ Components of an ICS (Industrial Control System) — CRITICAL

```
ICS (Industrial Control System)
├── SCADA — Supervisory Control and Data Acquisition
├── DCS   — Distributed Control System
├── BPCS  — Basic Process Control Systems
├── SIS   — Safety Instrumentation Systems
├── HMI   — Human Machine Interface
├── PLC   — Programmable Logic Controller
├── RTU   — Remote Terminal Unit
└── IED   — Intelligent Electronic Device
```

**Control loop types:** Closed Loop (output affects input to reach objective) | Manual Loop (totally under human control)

---

### 🖥️ SCADA Architecture

```
Control Center: HMI, Engineering Workstations, Data Historian, Control Server (SCADA-MTU), Communications Routers
      ↕ (Switched Telephone/Leased Line/Power Line, Radio/Microwave/Cellular, Satellite → WAN)
Field Sites: Field Site 1 (Modem+PLC) | Field Site 2 (WAN Card+IED) | Field Site 3 (Modem+RTU)
```

**PLC (Programmable Logic Controller):** Real-time digital computer for industrial automation — robust construction, ease of programming, sequential control, timers/counters, built to survive severe industrial environments.

---

### 🛡️ Layers of Protection Provided by SIS Systems

```
Process Design (bottom layer)
    ↓
Process Control (BPCS)
    ↓
Operator Intervention          } PREVENTION
    ↓
Safety Instrumented System
    ↓
Active Protection (relief valve/rupture disk)
    ↓
Passive Protection (bund/dike)  } MITIGATION
    ↓
Emergency Response (plant/community)
```

> SIS functional requirements determined via HAZOP, LOPA, risk graphs. Components: **Field sensors** (collect process data) → **Logic solvers** (decide action) → **Final control elements** (implement action, e.g., solenoid valves).
> 

---

### 📊 The Purdue Model (CRITICAL — MEMORIZE)

> Derived from the Purdue Enterprise Reference Architecture (PERA); describes internal connections/dependencies of ICS network components. Also known as the Industrial Automation and Control System reference model.
> 

```
IT Systems (Enterprise Zone)
├── Level 5: Enterprise Network
└── Level 4: Business Logistics Systems
─────────────────────────────────────────
Industrial Demilitarized Zone (IDMZ)
─────────────────────────────────────────
OT Systems (Manufacturing Zone)
├── Level 3: Operation Systems/Site Operations
├── Level 2: Control Systems/Area Supervisory Controls
├── Level 1: Basic Controls/Intelligent Devices
└── Level 0: Physical Process
```

> **IDMZ** = barrier between manufacturing zone (OT) and enterprise zone (IT); enables secure network connection while containing intrusions to allow uninterrupted production. Includes Microsoft domain controllers, database replication servers, proxy servers.
**Level 0 (Physical Process)** = actual manufacturing process; also called Equipment Under Control (EUC) — sensors, actuators, industrial equipment.
> 

---

### 📊 OT/ICS Protocols by Purdue Level

| Level | Protocols |
| --- | --- |
| **Level 4/5** | DCOM, FTP/SFTP, GE-SRTP, IPv4/IPv6, OPC UA, TCP/IP, SMTP, HTTP/HTTPS, Wi-Fi |
| **Level 3** | CC-Link, HSCP, ICCP (IEC 60870-6), IEC 61850, IEC 60870-5-104, IEC 60870-5-101/104, SOAP, DeviceNet, AS-Interface |
| **Level 2** | 6LoWPAN, **DNP3**, DNS/DNSSEC, FTE, HART-IP, ISA/IEC 62443, **Modbus**, NTP, Profinet, SuiteLink, Tase-2, ControlNet, Profibus PA/DP |
| **Level 0/1** | BACnet, EtherCAT, CANopen, Crimson, DeviceNet, Zigbee, ISA SP100, MELSEC-Q, Niagara Fox |

> **Modbus** — serial communication protocol used with PLCs; enables communication between many devices on the same network.
**DNP3** (Distributed Network Protocol 3) — communication protocol used to interconnect components within process automation systems.
> 

---

### 🎯 MITRE ATT&CK for ICS (CRITICAL — 12 Tactics)

| Tactic | Description | Techniques |
| --- | --- | --- |
| **Initial Access** | Methods to establish initial access within targeted ICS | Drive-by compromise, exploit public-facing app, exploit remote services |
| **Execution** | Execute malicious code/manipulate data/system functions | Changing operating mode, CLI use, execution through APIs |
| **Persistence** | Retain access even if device restarted/communication interrupted | Modification of program, module firmware insertion, execution through APIs, project file infection |
| **Privilege Escalation** | Gain higher-level access/authorization | Software exploitation, hooking |
| **Evasion** | Evade traditional defense mechanisms | Removing indicators, rootkits, changing operator mode |
| **Discovery** | Gain info to assess/identify target assets | Removal of indicators, enumeration of network connections, network sniffing |
| **Lateral Movement** | Additional movement across target ICS via existing access | Default credentials, program download, remote services |
| **Collection** | Gather info/knowledge regarding data and domains | Automated collection, information repositories, I/O images |
| **Command and Control** | Deactivate, control, or exploit physical control processes | Frequently used ports, connection proxy, standard app-layer protocol |
| **Inhibit Response Function** | Thwart reactions against security events | Activation of firmware update mode, blocking command/reporting messages |
| **Impair Process Control** | Disable, exploit, or control physical control processes | I/O brute-forcing, altering parameters, injection of module firmware |
| **Impact** | Damage, disrupt, or gain control of data/systems | Damage to property, loss of availability, denial of control |

---

### 📊 14 OT Threats

```
1.  Maintenance and Administrative Threat  8.  Exploiting Enterprise Specific Systems/Tools
2.  Data Leakage                           9.  Spear Phishing
3.  Protocol Abuse                         10. Malware Attacks
4.  Potential Destruction of ICS Resources 11. Exploiting Unpatched Vulnerabilities
5.  Reconnaissance Attacks                 12. Side-Channel Attacks
6.  Denial-of-Service Attacks              13. Buffer Overflow Attacks
7.  HMI-based Attacks                      14. Exploiting RF Remote Controllers
```

**Protocol Abuse example:** Attackers exploit outdated protocols (Modbus, CAN bus) to abuse emergency stop (e-stop) safety mechanisms via single-packet attacks.

**Side-Channel Attack:** Attacker gains physical access to unprotected device, uses oscilloscope + analysis software to recover cryptographic keys by observing power profile differences between correct/incorrect password characters.

### 🦠 OT Malware Examples

```
Fuxnet | Kapeka | Abyss Locker | AvosLocker | COSMICENERGY | INDUSTROYER.V2 | Pipedream
```

> **Fuxnet:** Destructive ICS malware disrupting OT environments; modifies crucial data, prevents sensor gateway access, damages physical sensors, rewrites NAND chip on gateways to disable remote access.
**COSMICENERGY:** Causes power disruptions by interacting with IEC-104 devices via tools like LIGHTWORK, issuing ON/OFF commands to disrupt electricity flow.
> 

---

## 5. OT Hacking Methodology

### 📊 5-Phase OT Hacking Methodology (CRITICAL)

```
1. Information Gathering
2. Vulnerability Scanning
3. Launch Attacks
4. Gain Remote Access
5. Maintain Access
```

---

### 1️⃣ Information Gathering

**Identifying ICS/SCADA Systems using Shodan:**

```
port:502                    # Modbus-enabled ICS/SCADA systems
"Schneider Electric"        # Search by PLC name
"SCADA Country:US"          # Search by geolocation
```

**Key SCADA/ICS Protocol Ports (CRITICAL):**

```
Modbus        → port 502
Fieldbus      → port 1089-91
DNP           → port 19999
Ethernet/IP   → port 2222
DNP3          → port 20000
PROFINET      → port 34962-64
EtherCAT      → port 34980
```

---

### 2️⃣ Vulnerability Scanning

**Nmap scanning commands by protocol (CRITICAL):**

```bash
# Siemens SIMATIC S7 PLCs (port 102)
nmap -Pn -sT -p 102 --script=s7-info <Target IP>

# Modbus Devices (port 502)
nmap -Pn -sT -p 502 --script modbus-discover <Target IP>
nmap -sT -Pn -p 502 --script modbus-discover --script-args='modbus-discover.aggressive=true' <Target IP>

# BACnet Devices (port 47808)
nmap -Pn -sU -p 47808 --script bacnet-info <Target IP>

# Ethernet/IP Devices (port 44818)
nmap -Pn -sU -p 44818 --script enip-info <Target IP>

# Niagara Fox Devices (ports 1911, 4911)
nmap -Pn -sT -p 1911,4911 --script fox-info <Target IP>

# ProConOS Devices (port 20547)
nmap -Pn -sT -p 20547 --script proconos-info <Target IP>
```

**Tool: Nessus** — configure New Policy/Basic Network Scan, modify DISCOVERY node port scan range (e.g., 0-1000).

---

### 3️⃣ Launch Attacks

**Fuzzing ICS Protocols with Fuzzowski:**

```bash
python -m fuzzowski 127.0.0.1 47808 -p udp -f bacnet -rt 0.5 -m BACnetMon    # BACnet
python -m fuzzowski 127.0.0.1 502 -p tcp -f modbus -rt 1 -m modbusMon        # Modbus
python -m fuzzowski printer1 631 -f ipp -r get_printer_attribs --restart smartplug  # IPP
```

---

### 4️⃣ Gain Remote Access

**Gaining Remote Access using DNP3:**

> ICS often configured with direct Internet access, ignoring firewall implementations, using default/weak credentials. Attacker performs port scanning to find open DNP3 port, then exploits it. Uses tools like Shodan to gain remote access.
> 

**Gaining Remote Access using Telnet/other:** Similar to IoT — exploit poorly configured remote access, default credentials.

---

### 5️⃣ Maintain Access

> Attackers remain undetected by clearing logs, updating firmware, and injecting rootkits. Can modify PLC firmware to launch firmware attacks, monitor/control target device operations.
> 

---

## 6. OT Attack Countermeasures

### 🛡️ How to Defend Against OT Hacking (14-Point Checklist — CRITICAL)

1. Use purpose-built sensors to discover vulnerabilities in the network
2. Update systems to the latest technologies and regularly patch systems
3. Implement secure configuration and secure coding practices for OT applications
4. Maintain an asset register for tracking and scrutinizing outdated systems
5. Use strong passwords and change the default factory-set passwords
6. Secure remote access through multiple layers of defense by implementing VPNs
7. Secure the network perimeter, and filter and prevent unauthorized inbound traffic
8. Regularly scan systems and networks using anti-malware tools
9. Harden the systems by disabling unused services and functionalities
10. Regularly patch vulnerabilities released by the manufacturers
11. Employ IDS and flow-measurement systems to detect attacks at an early stage
12. Use only tested and familiar third-party web servers for serving ICS web applications
13. Ensure ICS vendors add cryptographic signatures to the application updates
14. Perform periodic audits of the industrial systems to validate security controls

**Additional:** Regularly conduct risk assessments, incorporate threat intelligence to prioritize OT patches, disable unused ports/services, continuous monitoring of log data, train employees on latest security policies.

---

### 📊 How to Secure an IT/OT Environment — Security Controls by Purdue Level (CRITICAL TABLE)

| Zone | Purdue Level | Attack Vector | Risks | Security Controls |
| --- | --- | --- | --- | --- |
| **Enterprise** | 5 & 4 (Enterprise Network/Business Logistics) | Spear phishing, Ransomware | Abusing infrastructure, network access | Firewalls, IPS, Anti-bot, URL filtering, SSL inspection, Antivirus, DLP |
| **Industrial DMZ** | 3.5 (IDMZ) | DoS attacks | Malware injections, network infections | Anti-DoS solutions, IPS, Antibot, Application control, ALF |
| **Manufacturing** | 3 (Operational Systems) | Ransomware, Bot infection, Unsecured USB ports | Altering industrial process, industrial spying, unpatched monitoring systems | Anti-bot, IPS, Sandboxing, Application control, Traffic encryption, Port protection |
| **Manufacturing** | 2 & 1 (Control Systems/Basic Controls) | DoS exploitation, Unencrypted protocols, Default credentials, App/OS vulnerabilities | Altering industrial process, industrial spying | IPS, Firewall, Communication encryption using IPsec, Security gateways, authorized RTU/PLC commands only |
| **Manufacturing** | 0 (Physical Process) | Physical security breach | Modifications/disruption in physical process | Point-to-point communication, MAC authentication, additional security gateways at level 1 & 0 |

---

### 🌐 International OT Security Organizations

```
OTCC (Operational Technology Cybersecurity Coalition) — industry/government collaboration
OT-ISAC (Operational Technology Information Sharing and Analysis Center) — threat info sharing hub
                                                                             for energy/water utility sectors
```

---

## 7. Quick Exam Cheat Sheet

### 📊 The Purdue Model (MEMORIZE)

```
Level 5 - Enterprise Network             ┐
Level 4 - Business Logistics Systems     ┘ IT (Enterprise Zone)
─────────────── IDMZ ───────────────
Level 3 - Operation Systems/Site Ops     ┐
Level 2 - Control Systems/Area Supervisory│
Level 1 - Basic Controls/Intelligent Dev  │ OT (Manufacturing Zone)
Level 0 - Physical Process               ┘
```

---

### 🏗️ ICS Components

```
SCADA | DCS | BPCS | SIS | HMI | PLC | RTU | IED
```

---

### 🔑 Key SCADA/ICS Ports

```
Modbus: 502 | Fieldbus: 1089-91 | DNP: 19999 | Ethernet/IP: 2222 |
DNP3: 20000 | PROFINET: 34962-64 | EtherCAT: 34980
```

---

### 🎯 MITRE ATT&CK for ICS — 12 Tactics

```
Initial Access → Execution → Persistence → Privilege Escalation → Evasion →
Discovery → Lateral Movement → Collection → Command and Control →
Inhibit Response Function → Impair Process Control → Impact
```

---

### 📊 IoT vs OT Hacking Methodology (both 5 phases, near-identical)

```
Information Gathering → Vulnerability Scanning → Launch Attacks →
Gain Remote Access → Maintain Access
```

---

### 🔥 Common Exam Scenarios

**Q: What are the 4 layers whose combination equals IoT security architecture?**
→ **Application + Network + Mobile + Cloud = IoT**

**Q: What model derived from PERA describes IT/OT zone separation in ICS networks?**
→ **The Purdue Model**

**Q: What zone separates the OT manufacturing zone from the IT enterprise zone in the Purdue Model?**
→ **Industrial Demilitarized Zone (IDMZ)** — at level 3.5

**Q: What Purdue level represents the actual physical manufacturing process?**
→ **Level 0** (also called Equipment Under Control/EUC)

**Q: What port does Modbus use?**
→ **Port 502**

**Q: What port does DNP3 use?**
→ **Port 20000** (Note: DNP uses port 19999)

**Q: What framework provides 12 tactics to understand attacker behavior against ICS/OT systems?**
→ **MITRE ATT&CK for ICS**

**Q: What ICS components make up SCADA architecture at the Control Center?**
→ **HMI, Engineering Workstations, Data Historian, Control Server (SCADA-MTU), Communications Routers**

**Q: What are the 3 elements of a Safety Instrumented System (SIS)?**
→ **Field sensors, Logic solvers, Final control elements**

**Q: What attack uses a jamming + sniffing technique to steal a vehicle's unlock code?**
→ **Rolling Code Attack**

**Q: What tool is commonly used to discover Internet-connected SCADA/ICS/IoT devices?**
→ **Shodan**

**Q: What Nmap script detects Modbus-enabled devices?**
→ **`--script modbus-discover`**

**Q: What attack recovers cryptographic keys by analyzing power consumption differences during password entry?**
→ **Side-Channel Attack**

**Q: What tool is used for fuzzing ICS protocols like Modbus and BACnet?**
→ **Fuzzowski**

**Q: What are the 18 OWASP IoT Attack Surface Areas' first 3 areas?**
→ **Ecosystem (general), Device Memory, Device Physical Interfaces**

**Q: What port should be monitored because infected IoT devices use it to spread malicious files?**
→ **Port 48101**

**Q: What OT malware caused power disruptions by interacting with IEC-104 devices?**
→ **COSMICENERGY** (using the LIGHTWORK component)

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 18*