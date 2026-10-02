# Module 17: Hacking Mobile Platforms

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Mobile Platform Attack Vectors](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-mobile-platform-attack-vectors)
2. [Android OS Threats and Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-android-os-threats-and-attacks)
3. [iOS Threats and Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-ios-threats-and-attacks)
4. [Mobile Device Management (MDM)](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-mobile-device-management-mdm)
5. [Mobile Security Guidelines and Tools](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-mobile-security-guidelines-and-tools)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. Mobile Platform Attack Vectors

### 🎯 Vulnerable Areas in Mobile Business Environment

> Smartphones offer broad Internet/network connectivity via **3G/4G/5G, Bluetooth, Wi-Fi, and wired computer connections**. Security threats may arise at different places along these channels during data transmission (Mobile Device → Wi-Fi/Telco → Internet → Corporate VPN Gateway → Corporate Intranet, App Store, Website).
> 

---

### 📊 OWASP Top 10 Mobile Risks — 2024 (CRITICAL — Memorize)

| # | Risk | Description |
| --- | --- | --- |
| **M1** | **Improper Credential Usage** | Insecure handling of credentials/tokens — hardcoded credentials, unprotected storage, unencrypted transmission, weak auth methods |
| **M2** | **Inadequate Supply Chain Security** | Outdated/flawed third-party components/libraries; insecure coding, inadequate code review |
| **M3** | **Insecure Authentication/Authorization** | Weak password policies, insecure token handling, improper authorization checks |
| **M4** | **Insufficient Input/Output Validation** | Inadequate sanitization → SQL injection, command injection, XSS |
| **M5** | **Insecure Communication** | Insecure/deprecated protocols, improper config, invalid SSL certs |
| **M6** | **Inadequate Privacy Controls** | Inadequate protection of PII (names, addresses, financial data) |
| **M7** | **Insufficient Binary Protections** | Threats of code tampering and reverse engineering; lack of protection → binary attacks, counterfeit apps |
| **M8** | **Security Misconfiguration** | Weak encryption/hashing, unprotected storage, misconfigured access controls, enabled debugging, unnecessary permissions |
| **M9** | **Insecure Data Storage** | Plain text storage, unsecured databases, insufficient data protection, weak encryption |
| **M10** | **Insufficient Cryptography** | Weak/outdated encryption, poor key management, insecure hash functions, insecure RNG |

---

### 🔗 Attack Vector Categories

**Phone/SMS-based Attacks:**

| Attack | Description |
| --- | --- |
| **Baseband Attacks** | Exploits vulnerabilities in phone's GSM/3GPP baseband processor (sends/receives radio signals to cell towers) |
| **SMiShing** | SMS phishing — deceptive links/phone numbers via SMS to steal SSN, credit card, banking credentials |

**Why SMiShing is effective:** High SMS open rates, trust in SMS, limited space forces concise/compelling messages, lack of security awareness, mobile devices less protected, ease of caller-ID/number spoofing.

**Application-based Attacks:**

```
Sensitive Data Storage | No Encryption/Weak Encryption | Improper SSL Validation |
Configuration Manipulation | Dynamic Runtime Injection | Unintended Permissions |
Escalated Privileges | UI Overlay/PIN stealing | Third-party code | Intent Hijacking |
Zip Directory Traversal | Clipboard Data | URL Schemes | GPS Spoofing |
Weak/No Local Authentication | Integrity/Tampering/Repackaging | Side-channel Data Leakage
```

**Browser-based Attacks:**

| Attack | Description |
| --- | --- |
| **Phishing** | Fake pages mimicking trustworthy sites; mobile users more vulnerable (small screen, short URLs, limited warnings) |
| **Framing** | Malicious page embedded via HTML iFrame |
| **Clickjacking** | UI redress attack — tricks users into clicking something different than perceived |
| **Man-in-the-Mobile** | Malicious code bypasses OTP verification, relays gathered info to attacker |
| **Buffer Overflow** | Program writes beyond buffer limit, overwrites adjacent memory |
| **Data Caching** | Exploits cached data storing sensitive info |

**Web-server/Network-based Attacks:** Server Misconfiguration, XSS, CSRF, Weak Input Validation, Brute-Force Attacks, Cross-origin resource sharing, side-channel attacks, hypervisor attacks

**Database Attacks:** SQL Injection (nonvalidated input passed as SQL commands to backend database)

---

### 🦠 Notable Mobile Attack Examples

**Agent Smith Attack:**

```
1. Attacker drops malicious app in third-party app store
2. User downloads and installs the malicious mobile application
3. Malicious app infects/replaces legitimate apps (WhatsApp, SHAREit, MX Player) with C&C command versions
4. User's mobile bombarded with irrelevant ads
5. Attacker exploits infected apps to steal critical information
```

**Simjacker Vulnerability:**

```
Attacker sends malicious code via SMS → victim's SIM processes it →
retrieves device location and Cell-ID → sends SMS with cell-ID and mobile
location to accomplice device
```

**OTP Hijacking/Two-Factor Authentication Hijacking:**

- Attacker steals victim's PII by bribing/tricking mobile store sellers or exploiting number reuse
- Performs social engineering on telecom operator (claims device lost) to gain SIM control transfer
- Can also use SIM jacking malware to intercept/read OTPs
- **Tool: AdvPhishing** — social media phishing tool bypassing 2FA/OTP authentication

---

## 2. Android OS Threats and Attacks

### 🤖 Android OS Architecture (CRITICAL — Diagram)

```
System Apps (Dialer, Email, Calendar, Camera...)
        ↓
Java API Framework (Content Providers, Managers: Activity/Location/Package/
                     Notification/Resource/Telephony/Window, View System)
        ↓
Native C/C++ Libraries (WebKit, Libc, Media Framework, OpenGL ES...)  |  Android Runtime (ART, Core Libraries)
        ↓
Hardware Abstraction Layer — HAL (Audio, Bluetooth, Camera, Sensors...)
        ↓
Linux Kernel (Drivers: Audio, Binder/IPC, Display, Keypad, Bluetooth, WiFi, USB, Camera, Shared Memory)
        ↓
Power Management
```

**Features:** Application framework enabling reuse/replacement of components, prebuilt UI components, integrated browser (open-source Blink/WebKit), media support (MPEG4, H.264, MP3, AAC, AMR, JPG, PNG, GIF)

**Data storage options:**

- **Shared Preferences** — private primitive data in key-value pairs
- **Internal Storage** — private data on device memory
- **External Storage** — public data on shared external storage
- **SQLite Databases** — structured data in a private database
- **Network Connection** — data on own network server

---

### 🔓 Android Rooting

> Rooting allows Android users to attain privileged control (**"root access"**) within Android's subsystem. Process exploits security vulnerabilities in device firmware, copies the **su binary** to a location in current process's PATH (e.g., `/system/xbin/su`), and grants executable permissions via `chmod`.
> 

**Rooting enables:**

- Modifying/deleting system files, modules, ROMs, kernels
- Removing carrier/manufacturer bloatware
- Low-level hardware access
- Improved performance
- Wi-Fi and Bluetooth tethering
- Installing apps on SD card

**Tool: KingoRoot** (PC and no-PC versions)

- **With PC:** Download KingoRoot, connect device via USB, enable USB debugging, click ROOT
- **Without PC:** Enable "unknown sources," download KingoRoot.apk, launch, tap "One Click Root"

---

### 🔐 Bypassing FRP (Factory Reset Protection)

> FRP is a security feature preventing unauthorized access to lost/stolen Android devices. Attackers bypass via tools like **4ukey** and **Octoplus FRP**.
> 

**4ukey steps:** Connect locked device → click "Remove Google Lock (FRP)" → select OS version → click Start → follow on-screen instructions → "Bypassed Google FRP Lock Successfully"

---

### 🛠️ Android Hacking Tools

| Tool | Purpose |
| --- | --- |
| **Kali NetHunter** | Mobile penetration testing platform (MAC Changer, HID Attacks, Bad USB MITM, Mana Wireless Toolkit, MITM Framework) |
| **msfvenom (Metasploit Payload Generator)** | Generates payloads (ASP, reverse TCP, staged, etc.) |
| **LOIC (Low Orbit Ion Cannon)** | Mobile app for DoS/DDoS attacks (UDP/HTTP/TCP flood) |
| **PhoneSploit Pro** | Connects to devices via ADB, controls device, installs/uninstalls APKs, accesses shell, hacks device via Metasploit |
| **Metasploit/Meterpreter** | Remote shell access — `ipconfig`, full device control via ADB exploitation |

**Notable Android malware:** Mamont Trojan (requests call/SMS permissions, fake cash prize), SecuriDropper, Dwphon, DogeRAT, Tambir, SoumniBot

---

### 🔍 Static Analysis of Android APK

> Security analysts examine code **without executing** the app to identify harmful features/vulnerabilities, or compare against malware signatures.
> 

**Tool: MobSF (Mobile Security Framework)** — automates malware analysis and security assessment via static/dynamic analysis. Analyzes APK, XAPK, APPX, IPA files. Extracts app permissions, browsable activities, signer certificates.

```
Steps: Go to mobsf.live → Upload & Analyze the suspicious APK
```

**Other tool: Sixo Online APK Analyzer**

---

### ✅ Securing Android Devices (CRITICAL CHECKLIST)

| ✔️/❌ | Guideline |
| --- | --- |
| ✔️ | Enable screen locks (PIN, password, pattern, biometrics) |
| ❌ | Never root your Android device |
| ✔️ | Download apps only from official Android market |
| ✔️ | Regularly update the operating system |
| ✔️ | Use free protector apps (assign passwords to SMS, mail accounts) |
| ✔️ | Keep device updated with Android antivirus software (e.g., Kaspersky Antivirus) |
| ❌ | Do not directly download APK files |
| ✔️ | Enable encryption in your Android device |
| ✔️ | Customize your locked home screen with user info |

---

## 3. iOS Threats and Attacks

### 🍎 iOS Framework Architecture (CRITICAL — Diagram)

```
Cocoa (Application)                                  | AppKit
        ↓
Media Layer (AV Foundation, Core Animation, Core Audio, Core Image,
              Core Text, OpenAL, OpenGL, Quartz)
        ↓
Core Services (Address Book, Core Data, Core Foundation, Foundation,
                Quick Look, Social, Security, WebKit)
        ↓
Core OS (Accelerate, Directory Services, Disk Arbitration, OpenCL, System Configuration)
        ↓
Kernel and Device Drivers (BSD, File System, Mach, Networking)
```

- **Core OS layer:** Low-level features most other tech is based on; deals with security, external hardware/networks
- **Kernel and Device Drivers:** Lowest layer — kernel, drivers, BSD, file systems, networking infrastructure

---

### 🔓 Jailbreaking

### 📊 3 Types of Jailbreaking Exploits (CRITICAL)

| Type | Description |
| --- | --- |
| **Userland Exploit** | Uses loophole in system application; allows user-level access but NOT iBoot-level access. Cannot be secured against (no recovery mode loop trigger). Only firmware updates patch it |
| **iBoot Exploit** | Semi-tethered if device has new bootrom. Allows user-level AND iBoot-level access. Exploits loophole in iBoot (3rd bootloader) to delink code-signing appliance. Firmware updates can patch |
| **Bootrom Exploit** | Uses loophole in SecureROM (1st bootloader) to disable signature checks, load patched NOR firmware. Firmware updates CANNOT patch. Allows user-level AND iBoot-level access. Only a hardware update of bootrom by Apple can patch it |

### 📊 4 Jailbreaking Techniques (CRITICAL)

| Technique | Behavior on Reboot |
| --- | --- |
| **Untethered Jailbreaking** | Device starts up completely; kernel patched WITHOUT computer help — jailbroken after every reboot |
| **Semi-tethered Jailbreaking** | Device starts up completely; kernel NOT patched but still usable for normal functions. Need jailbreaking tool to use jailbroken addons |
| **Tethered Jailbreaking** | Device has NO patched kernel if started on its own; may get stuck partially started. Must be "re-jailbroken" via computer ("boot tethered") each time powered on |
| **Semi-untethered Jailbreaking** | Similar to semi-tethered; kernel not patched on reboot BUT can be patched WITHOUT computer, using an app installed on the device |

**Jailbreaking tool example: Hexxa Plus** — Repo Extractor for jailbreak repos (Extract Repo, Get Repos, Contact Support)

---

### 🖥️ Accessing iOS Device Shell

**Default credentials:** users **`root`** and **`mobile`**, default password for both: **`alpine`**

```bash
# Via Wi-Fi (OpenSSH installed on device, same network as host)
root@<device_ip_address>
# Type "exit" or Control+D to quit

# Via USB (using usbmuxd/iproxy when Wi-Fi unavailable)
ssh -p 2222 root@localhost
root@localhost's password:
iPhone:~ root#
```

> Note: Cannot establish data connection for more than 1 hour in a locked state (USB Restricted Mode)
> 

**Listing installed apps (Frida):**

```bash
frida-ps -Uai
```

**Network sniffing on iOS:**

```bash
rvictl -s <UDID of the iOS device>   # Starts device with rvi0 interface
```

---

### 🛠️ iOS Hacking Tools

```
Elcomsoft Phone Breaker (iCloud/local data extraction, backup decryption, keychain exploration)
Enzyme | Network Analyzer (net tools) | iOS Binary Security Analyzer | iWepPRO | Frida
```

---

### ✅ Guidelines to Secure iOS Devices (CRITICAL CHECKLIST)

- Configure **Find My iPhone**; use it to wipe a lost/stolen device
- Enable **Jailbreak detection**; protect AppleID/Google account access
- **Disable iCloud services** so enterprise data isn't backed up to the cloud
- Enable **Ask to Join Networks** (`Settings → Wi-Fi → Ask to Join Networks`)
- Regularly update device OS with security patches
- Enable **Erase Data** after 10 failed attempts (`Settings → Face ID & Passcode → Erase Data`)
- Disable **Voice Dial** (`Settings → Face ID & Passcode → Voice Dial → OFF`)
- Delete **Keyboard Cache** (`Settings → General → Transfer or Reset iPhone → Reset → Reset Keyboard Dictionary`)
- Disable **Geotagging** (`Settings → Privacy & Security → Location Services → Camera → Never`)
- Enable **Safari's Privacy/Security Settings** (block pop-ups, disable AutoFill, fraudulent website warning)
- Enable **Do Not Track** (`Settings → Safari → Do Not Track`)
- Disable **Bluetooth** and **Wi-Fi** when not in use
- Disable **Siri** (`Settings → Siri & Search → Listen for → OFF`)
- Disable **AutoFill** in Safari

**iOS Security Tools:** Malwarebytes Mobile Security, Norton Mobile Security for iOS, McAfee Mobile Security, Trend Micro Mobile Security, AVG Mobile Security, Kaspersky Standard

---

## 4. Mobile Device Management (MDM)

### 📊 What is MDM?

> Provides platforms for **over-the-air or wired distribution** of applications, data, and configuration settings for all mobile device types. Helps implement enterprise-wide policies to reduce support costs, business discontinuity, and security risks. Manages both company-owned and employee-owned (**BYOD**) devices.
> 

**Architecture:** Windows/Smartphone/iPad/Windows Phone/Tablet PC/iPhone → Wireless → Internet → Firewall/DMZ → File System, Directories/Databases, Administrative Console, MDM Server

**Basic MDM features:**

- Uses a passcode for the device
- Remotely locks the device if lost
- Remotely wipes data in lost/stolen device
- Detects if device is rooted or jailbroken
- Enforces policies and tracks inventory
- Performs real-time monitoring and reporting

**MDM Solutions:** Scalefusion, ManageEngine Mobile Device Manager Plus, Microsoft Intune, SOTI MobiControl, AppTec360, Jamf Pro

---

### 💼 BYOD (Bring Your Own Device)

**Benefits:** Access from anywhere, employee freedom (fewer imposed rules), mobile/cloud-centric strategy vs traditional client-server, **lower costs** (employees purchase own devices, shift data service costs)

### ⚠️ BYOD Risks

| Risk | Description |
| --- | --- |
| **Sharing confidential data on unsecured networks** | Unencrypted public network connections → data leakage |
| **Data leakage and endpoint security issues** | Mobile devices are insecure cloud-connectivity endpoints; loss exposes corporate data |
| **Improperly disposing of devices** | Sensitive info (financial, credit card, contacts, corporate data) not wiped before disposal |
| **Support for many different devices** | Increases IT costs, impedes management/control capability |
| **Mixing personal and private data** | Serious security/privacy implications; best practice is separation for targeted encryption/remote wipe |

---

## 5. Mobile Security Guidelines and Tools

### 📊 OWASP Top 10 Mobile Risks and Solutions (CRITICAL TABLE)

| Risk | Solutions |
| --- | --- |
| **Improper Credential Usage** | Avoid hardcoded credentials; encrypt credentials during transmission; use revocable access tokens; strong auth protocols |
| **Inadequate Supply Chain Security** | Ensure secure app signing/distribution; use only trusted/validated third-party libraries |
| **Insecure Authentication/Authorization** | Avoid weak auth design patterns; reinforce server-side authentication |
| **Insufficient Input/Output Validation** | Implement strict input/output validation; data integrity checks; secure coding practices |
| **Insecure Communication** | Use certificates signed by a trusted CA; ensure certs valid and fail closed |
| **Inadequate Privacy Controls** | Protect access with proper auth/authorization; use static/dynamic security checking tools |
| **Insufficient Binary Protections** | Use code obfuscation and anti-tampering techniques; local security checks, backend enforcement, integrity checks |
| **Security Misconfiguration** | Refrain from hardcoded default credentials; disable debugging features in production |
| **Insecure Data Storage** | Store sensitive data in secure, restricted-access locations; regularly patch libraries/frameworks/dependencies |
| **Insufficient Cryptography** | Use strong encryption algorithms with sufficient key length; strong hash functions (SHA-256, bcrypt) |

---

### 🔑 Critical Data Storage: KeyStore (Android) vs Keychain (iOS)

| Android KeyStore | iOS Keychain |
| --- | --- |
| Authentication via patterns, PINs, passwords, fingerprints | Authentication via Touch ID, Face ID, passcodes, passwords |
| Hardware-backed Android KeyStore | Hardware-backed **256-bit AES encryption** |
| Encryption for non-readable storage format | **Access-Control Lists (ACLs)** specify accessibility by applications |
| Authorization techniques to create/import keys | Store only small chunks of data directly in keychain |
| Keys accessed only after proper authentication | Specify **AccessControlFlags** to authenticate the key |

---

### 📋 General Mobile Device Security Guidelines

- Configure notifications to disable viewing while locked
- Configure Auto Fill carefully (reduce shoulder-surfing risk)
- Disable diagnostics/usage data collection
- Harden browser permission rules per company policy
- Design and implement mobile device policies
- Control devices and applications; prohibit USB keys
- Press power button to lock device when not in use
- Verify printer location before printing sensitive documents
- Use Citrix technologies / follow-me-data / ShareFile for enterprise-managed solutions
- Use cellular data instead of public Wi-Fi
- Deploy anti-malware applications
- Enforce multi-factor authentication
- Always log off from mobile applications after use
- Use secure protocols (TLS); discourage public Wi-Fi without VPN

---

### 🛠️ Mobile Penetration Testing Tools

```
ImmuniWeb MobileSuite (iOS/Android Security Test, OWASP Mobile Top 10 Test, Mobile App Privacy Check)
Codified Security | Astra Security | Appknox | Data Theorem's Mobile Secure | MobSF
```

---

## 6. Quick Exam Cheat Sheet

### 📊 OWASP Top 10 Mobile Risks 2024 (M1-M10)

```
M1  - Improper Credential Usage
M2  - Inadequate Supply Chain Security
M3  - Insecure Authentication/Authorization
M4  - Insufficient Input/Output Validation
M5  - Insecure Communication
M6  - Inadequate Privacy Controls
M7  - Insufficient Binary Protections
M8  - Security Misconfiguration
M9  - Insecure Data Storage
M10 - Insufficient Cryptography
```

---

### 🔓 3 Jailbreaking Exploit Types

```
Userland Exploit → user-level access only, NO iBoot access, patched by firmware update
iBoot Exploit    → user-level + iBoot-level access, patched by firmware update
Bootrom Exploit  → user-level + iBoot-level access, CANNOT be patched by firmware (needs Apple hardware fix)
```

### 🔓 4 Jailbreaking Techniques (by reboot behavior)

```
Untethered      → kernel patched automatically, no computer needed, every reboot
Semi-tethered   → kernel NOT patched, still usable, need tool for addons
Tethered        → kernel NOT patched, stuck partial boot, MUST reconnect to computer
Semi-untethered → kernel NOT patched, but can self-patch via on-device app (no computer)
```

---

### 🔑 Default iOS Shell Credentials

```
Users: root, mobile
Password (both): alpine
```

---

### 🏗️ Architecture Stacks

```
ANDROID: System Apps → Java API Framework → Native Libraries/ART → HAL → Linux Kernel → Power Mgmt
iOS:     Cocoa → Media → Core Services → Core OS → Kernel and Device Drivers
```

---

### 🔥 Common Exam Scenarios

**Q: What OWASP mobile risk covers hardcoded credentials and insecure token storage?**
→ **M1 - Improper Credential Usage**

**Q: Which jailbreak type cannot be patched by a firmware update?**
→ **Bootrom Exploit** (only a hardware update by Apple can fix it)

**Q: In which jailbreaking technique does the device get stuck in a partially started state on its own?**
→ **Tethered Jailbreaking**

**Q: What are the default iOS device shell credentials?**
→ **Username: root or mobile, Password: alpine**

**Q: What binary is copied to the PATH during Android rooting?**
→ **su binary** (e.g., to `/system/xbin/su`)

**Q: What Android security feature prevents unauthorized access to a lost/stolen device?**
→ **FRP (Factory Reset Protection)**

**Q: What tool automates static/dynamic analysis of Android APK files?**
→ **MobSF (Mobile Security Framework)**

**Q: What's the difference between Android KeyStore and iOS Keychain encryption?**
→ Android KeyStore uses hardware-backed storage with pattern/PIN/biometric auth; iOS Keychain uses hardware-backed 256-bit AES with Touch ID/Face ID and ACLs

**Q: What does MDM stand for and what are its core features?**
→ **Mobile Device Management** — passcode enforcement, remote lock/wipe, root/jailbreak detection, policy enforcement, real-time monitoring

**Q: Name 3 BYOD security risks.**
→ **Sharing data on unsecured networks, data leakage/endpoint security, improper device disposal** (also: support complexity, mixing personal/corporate data)

**Q: What attack uses malicious SMS to retrieve a victim's device location via SIM processing?**
→ **Simjacker vulnerability**

**Q: What social engineering technique tricks a telecom operator into transferring SIM control to an attacker?**
→ **OTP Hijacking / SIM swapping**

**Q: What tool bypasses two-factor/OTP authentication via social media phishing?**
→ **AdvPhishing**

**Q: What Android malware replaces legitimate apps like WhatsApp with ad-injecting versions?**
→ **Agent Smith Attack**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 17*