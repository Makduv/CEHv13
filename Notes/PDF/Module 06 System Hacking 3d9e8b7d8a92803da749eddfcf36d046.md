# Module 06: System Hacking

> **Exam:** 312-50 | **Phases:** Gaining Access → Escalating Privileges → Maintaining Access → Clearing Tracks
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Gaining Access — Password Cracking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-gaining-access--password-cracking)
2. [Gaining Access — Vulnerability & Buffer Overflow Exploitation](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-gaining-access--vulnerability--buffer-overflow-exploitation)
3. [Escalating Privileges](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-escalating-privileges)
4. [Maintaining Access](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-maintaining-access)
5. [Clearing Tracks / Covering Tracks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-clearing-tracks--covering-tracks)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. Gaining Access — Password Cracking

### 🔑 Microsoft Authentication Mechanisms

| Mechanism | Description |
| --- | --- |
| **SAM Database** | Security Accounts Manager — stores hashed passwords (LM/NTLM) as a registry file at `%SystemRoot%\system32\config\SAM`, mounted under `HKEY_LOCAL_MACHINE\SAM`. Locked by an exclusive filesystem lock while Windows runs. Uses **SYSKEY** (NT 4.0+) to partially encrypt hashes. |
| **NTLM Authentication** | Challenge-response protocol. 6-step process: (1) user types password → (2) Windows hashes it → (3) computer sends login request to DC → (4) DC sends logon challenge → (5) computer sends response to challenge → (6) DC compares and confirms |
| **Kerberos Authentication** | Default (stronger than NTLM). Uses **KDC (Key Distribution Center)** containing **AS (Authentication Server)** and **TGS (Ticket Granting Server)**. Flow: Client → AS (request) → AS reply → Client → TGS (request service ticket) → TGS reply → Client → Application Server |

> 💡 **LM hash limitation:** Cannot calculate LM hash for passwords >14 characters — falls back to a "dummy" value. Vista+ disables LM hashes by default.
> 

**Tools to extract password hashes:** pwdump7 (tarasco.org), Mimikatz, DSInternals, Hashcat, PyCrack

---

### 📊 Types of Password Attacks (CRITICAL TABLE)

| Type | Description | Techniques |
| --- | --- | --- |
| **Non-Electronic Attacks** | No technical knowledge required | Shoulder surfing, Social engineering, Dumpster diving |
| **Active Online Attacks** | Attacker directly communicates with victim machine | Dictionary, Brute-forcing, Rule-based, Hash injection/Mask attack, LLMNR/NBT-NS poisoning, Trojan/Spyware/Keyloggers, Password guessing/spraying, Internal monologue attack, Cracking Kerberos passwords |
| **Passive Online Attacks** | No communication with authorizing party | Wire sniffing, Man-in-the-Middle, Replay attack |
| **Offline Attacks** | Attacker copies target's password file, cracks locally | Rainbow table attack (pre-computed hashes), Distributed network attack |

---

### 🎯 Active Online Attacks — Details

**Dictionary Attack:** Loads a text file of common passwords against accounts. Useful in cryptanalysis and bypassing authentication. Cannot work against passphrases.

```bash
# Get rockyou wordlist
wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

# Generate customized dictionary with John the Ripper
john --wordlist=</path_to/rockyou.txt> --rules --stdout > <output_wordlist.txt>

# Crack NTLM hashes with customized wordlist
john --rules --wordlist=</path_to/output_wordlist.txt> --format=NT /path/to/ntlm_hashes.txt
```

**Password Spraying:** Attacker tries ONE common password against MANY accounts (stays under lockout threshold), then repeats. Common target ports: **MSSQL (1433/TCP), SSH (22/TCP), FTP (21/TCP), SMB (445/TCP), Telnet (23/TCP), Kerberos (88/TCP)**.

```bash
# thc-hydra password spraying/cracking
hydra -l admin -p password ftp://localhost/
hydra -L default_logins.txt -p test ftp://localhost/
hydra -l admin -P common_passwords.txt ftp://localhost/
hydra -L logins.txt -P passwords.txt ftp://localhost/
```

**LLMNR/NBT-NS Poisoning:** Attacker uses **Responder** tool to poison name resolution broadcasts, capturing NTLMv2-SSP hashes.

```bash
sudo responder -I eth0
```

**Kerberos Attacks (CRITICAL):**

| Attack | Description | Tool |
| --- | --- | --- |
| **AS-REP Roasting** | Targets accounts with **"Do not require Kerberos preauthentication"** enabled. Attacker requests AS-REP, extracts encrypted portion, cracks offline to reveal password | Rubeus, hashcat |
| **Kerberoasting** | Regular user requests TGS tickets for service accounts (SPNs). Portions of TGS are RC4-encrypted with service account's password hash. Attacker extracts and cracks offline | Rubeus (`kerberoast /outfile:hash.txt`) |

**Kerberoasting steps:**

1. Authenticate with legitimate account → obtain valid TGT
2. Use TGT to request TGS tickets for service accounts (SPNs)
3. Use Rubeus to extract TGS tickets from memory
4. Perform offline brute-force with hashcat/John the Ripper

---

### 🎯 Passive Online & Offline Attacks

- **Markov-Chain Attack:** Splits password DB entries into 2/3-character syllables (n-grams), builds new alphabet, combines into words (max 8 chars), then dictionary attack
- **GPU-based Attack:** Exploits browser's OpenGL API access to track/steal passwords via side-channel leaks
- **Rainbow Table:** Precomputed table of password→hash pairs. Attacker computes hash of candidate password, compares to table for a match

```bash
# rtgen syntax to generate rainbow tables
rtgen hash_algorithm charset plaintext_len_min plaintext_len_max table_index chain_len chain_num part_index
```

**Distributed Network Attack (DNA):** Uses DNA Server (manages job queue: Current Jobs, Finished Jobs) + DNA Client (installed on multiple workstations) to distribute password cracking workload across a network.

**Password Cracking Tools:** RainbowCrack, hashID, Patator, brutus, BruteX, Secure Shell Bruteforcer

---

### 🔒 Password Cracking Countermeasures

- Do not use cleartext protocols or weak encryption
- Set password change policy to 30 days
- Enable **SYSKEY** with strong password to protect SAM
- Monitor server logs for brute-force (HTTP 401 status codes)
- Disable LM/NTLM authentication if not needed
- Perform periodic password audits
- Ensure unpatched systems can't reset passwords via buffer overflow/DoS
- Enable account lockout (attempts, counter time, duration)
- Automated password reset
- BIOS password protection for servers/laptops
- Train employees against social engineering
- Configure password policies via Group Policy
- Use MFA/2FA (e.g., CAPTCHA)
- Secure physical access to prevent offline attacks
- **Disable LLMNR/NBT-NS** (registry/Group Policy) to prevent poisoning attacks

---

## 2. Gaining Access — Vulnerability & Buffer Overflow Exploitation

### 🎯 Vulnerability Exploitation — 7 Steps (CRITICAL)

1. **Identify the vulnerability** — footprinting, scanning, enumeration, vuln analysis; use Exploit-DB, Packet Storm
2. **Determine the risk** associated with the vulnerability
3. **Determine the capability** of the vulnerability (if risk is low)
4. **Develop the exploit** — use Exploit-DB or develop with Metasploit
5. **Select delivery method** — Local or Remote
6. **Generate and deliver the payload** — inject shellcode
7. **Gain remote access** — run exploit, control target via remote shell

> **Proof-of-Concept (PoC):** Demonstrates existence/impact of a vulnerability — code/instructions/script that validates severity and impact for stakeholders.
> 

---

### 🛠️ Metasploit Framework

**Architecture:**

```
Libraries: Rex → Framework-Core → Framework-Base
Interfaces: msfconsole, msfvenom, msfrpc, msfrpcd, Armitage
Modules: Auxiliary, Encoders, Evasion, Exploits, NOPS, Payloads, Post-exploitation
```

**Module types:**

| Module | Purpose |
| --- | --- |
| **Exploit Module** | Encapsulates a single exploit; uses Mixins for dynamic behavior modification, brute-force, passive exploits |
| **NOPS Module** | Generates no-operation instructions to fill buffers (`generate -t c 50` = 50-byte NOP sled) |
| **Encoder Module** | Obfuscates payload to evade AV/IDS signature detection; uses polymorphism (changes encoding each generation) |
| **Evasion Module** | Modifies payload/exploit behavior to avoid detection (e.g., `evasion/windows/windows_defender_exe`) |
| **Post-Exploitation Module** | Used AFTER compromising target — gather info, escalate privileges, maintain access, move laterally |

**Key Post-Exploitation Modules:**

```
post/windows/gather/enum_logged_on_users
post/windows/gather/credentials/credential_collector
post/linux/gather/enum_configs
post/linux/gather/hashdump
post/multi/manage/autoroute       (network pivoting)
post/windows/manage/portproxy     (port forwarding)
```

---

### 💣 Buffer Overflow

> A **buffer** is adjacent memory allocated for runtime data. Buffer overflow = writing MORE data than allocated, overwriting neighboring memory.
> 

**Why programs are vulnerable:**

- Lack of boundary checking
- Older programming languages
- Unsafe/vulnerable functions
- Poor programming practices
- Failure to set filtering/validation
- Executing code in the stack segment
- Improper memory allocation, insufficient input sanitization

**Stack Registers (CRITICAL — Memorize):**

| Register | Full Name | Function |
| --- | --- | --- |
| **EBP** | Extended Base Pointer (StackBase) | Stores address of first data element on stack |
| **ESP** | Extended Stack Pointer | Stores address of next data element to be stored |
| **EIP** | Extended Instruction Pointer | Stores address of NEXT instruction to execute (**most important — read only, target of overflow attacks**) |
| **ESI** | Extended Source Index | Maintains source index for string operations |
| **EDI** | Extended Destination Index | Maintains destination index for string operations |

Stack operates **LIFO** (Last-In-First-Out): PUSH (store data), POP (remove data).

**Stack layout (top to bottom):**

```
ESP → Stack Frame
      Buffer Space
EBP (Extended Base Pointer)
EIP → Return Address
```

**Types:** Stack-based Buffer Overflow (most common — `strcpy()` without bounds check) vs Heap-based Buffer Overflow (`malloc()` corruption)

---

### 🪟 Windows Buffer Overflow Exploitation — 9 Steps (CRITICAL)

```
1. Perform spiking
2. Perform fuzzing
3. Identify the offset
4. Overwrite the EIP register
5. Identify bad characters
6. Identify the right module
7. Generate shellcode
8. Gain root access (shell)
```

**Perform Spiking:** Send crafted TCP/UDP packets to crash the server and identify buffer overflow vulnerabilities.

```bash
nc -nv <Target IP> <Target Port>    # Establish connection with Netcat
```

**Identify the right module:** Use `mona.py` script in Immunity Debugger (`!mona modules`) to find modules lacking memory protection.

**Generate shellcode:** `msfvenom` command generates shellcode injected into EIP register to gain shell access.

---

### 🛡️ Bypassing Security Mechanisms

| Technique | Description |
| --- | --- |
| **ASLR** | Address Space Layout Randomization — randomizes memory addresses to prevent predictable payload placement |
| **DEP** | Data Execution Prevention — prevents code execution in non-executable memory regions |
| **Heap Spraying** | Floods free heap space with multiple copies of malicious code via existing vulnerabilities (steps: vulnerability ID → fill heap → overwrite pointers → execute code) |
| **JIT Spraying** | Exploits JIT compilation in browsers — crafts malicious JavaScript that forces JIT compiler to allocate shellcode into predictable memory locations |
| **ROP (Return-Oriented Programming)** | Hijacks program control flow via the call stack, chains existing code "gadgets" (ending in x86 RET instruction) to execute arbitrary malicious code while bypassing code signing/executable space protection |
| **Exploit Chaining** | Combines multiple exploits/vulnerabilities sequentially to infiltrate from root level — recon → enumerate footprints/vulnerabilities → gain access → escalate to kernel/root/system level |

---

### 🗺️ Domain Mapping — BloodHound

> **BloodHound** — JavaScript web app built on Linkurious, uses **graph theory** to reveal hidden/unintended relationships in an AD environment. Identifies complex attack paths (e.g., shortest path to Domain Admins, Kerberoastable users, AS-REP roastable users).
> 

---

## 3. Escalating Privileges

### 🎯 Privilege Escalation Overview

> Attacker gains access with a **non-admin account**, then attempts to gain **administrative privileges** by exploiting design flaws, programming errors, bugs, and configuration oversights.
> 

### 📊 Two Types (CRITICAL)

| Type | Description | Example |
| --- | --- | --- |
| **Horizontal Privilege Escalation** | Unauthorized user accesses resources/functions of an authorized user with **similar** access permissions | Online banking user A accesses user B's account |
| **Vertical Privilege Escalation** | Unauthorized user gains access to resources/functions of a user with **higher** privileges | User accesses site with administrative functions |

---

### 🛠️ Privilege Escalation Techniques (Comprehensive)

**DLL Hijacking (Windows):** Windows apps often don't use fully-qualified paths for DLLs — search current directory first. Attacker places malicious DLL in application directory → executed instead of real DLL. Tools: **Spartacus, PowerSploit**.

**Dylib Hijacking (macOS):** Similar concept — macOS loader searches multiple directories for dynamic libraries (dylibs). Attacker injects malicious dylib into primary directory. Enables stealthy persistence, runtime process injection, Gatekeeper bypass. Tool: **Dylib Hijack Scanner**.

**Named Pipe Impersonation:** Windows named pipes provide legitimate inter-process communication. Attacker creates a pipe server with LOW privileges, tricks a HIGH-privilege client into connecting — server inherits client's security context.

```
msf > exploit(windows/local/named_pipe...)
> getsystem     # Gain administrative-level privileges
```

**Misconfigured Services:**

- **Unquoted Service Paths:** Executable path not enclosed in quotes → system may execute a malicious binary placed earlier in the unquoted path
- **Service Object Permissions:** Misconfigured permissions allow modifying service attributes, adding users to local admin group
- **Unattended Installs:** `Unattend.xml` stores install configuration (including credentials!) at:
    
    ```
    C:\Windows\Panther\C:\Windows\Panther\UnattendGC\C:\Windows\System32\C:\Windows\System32\sysprep\
    ```
    

**Misconfigured NFS:** Attacker enumerates NFS misconfig to gain root-level access via regular/low-privilege user account. Uses port 2049 (RPC).

**UAC Bypass (User Account Control):** Even with UAC enabled, attackers abuse trusted Windows apps to escalate without triggering notification.

```bash
msf > use exploit/windows/local/bypassuac              # via process injection
msf > use exploit/windows/local/bypassuac_injection     # via reflective DLL / memory injection
```

Additional techniques: **FodHelper Registry Key**, **Eventvwr Registry Key**, **COM Handler Hijacking**

**Access Token Manipulation, Network Logon Scripts, RC Scripts (`rc.common`/`rc.local` on Unix), Startup Items (macOS `/Library/StartupItems`, `StartupParameters.plist`)** — all abused to maintain persistence/escalate.

**Active Directory Certificate Services (ADCS) Abuse:** Misconfigured ADCS templates lead to credential theft, domain escalation, persistence.

```bash
certipy find -u '<target user>@<domain name>' -p <password> -dc-ip <DC_IP> -vulnerable -enabled
```

**Kernel Exploits:**

```bash
cat /etc/issue          # OS
uname -a                 # Kernel version
cat /proc/version        # Architecture
```

Use exploit-db.com and scripts like `linprivchecker.py`.

**Abusing '.' in PATH:** If `.` is in a privileged user's PATH, attacker places a malicious script (e.g., `ls`) in a directory the victim will execute from — executes attacker's script instead of the legitimate command.

**macOS Elevation Abuse:** Exploiting `AuthorizationExecuteWithPrivileges` API (lacks validation of whether root access requests come from a trusted source).

**Network Pivoting (via Metasploit):**

```bash
run post/windows/gather/arp_scanner RHOSTS <target subnet range>   # Discover live hosts
background
route add <IP address> <subnet mask> <session number>              # Set up routing rules
```

**pwncat privilege escalation:**

```bash
pwncat$ escalate list              # List direct escalations for any user
pwncat$ escalate list -u root      # List for specific user
pwncat$ escalate run               # Perform escalation
```

---

### 🔒 Privilege Escalation Countermeasures

- Enforce temporary/time-limited credentials for privileged accounts
- Implement code signing and verification
- Enable session recording/monitoring for privileged users
- Test patches in secured environment before production
- Mandate strong password complexity
- Implement **JIT (Just-In-Time) access** for privileged users
- Frequently audit/update ACLs
- Configure **RBAC** (Role-Based Access Control)
- Regularly scan/patch misconfigurations
- Harden system configs (disable unnecessary services)
- Implement application whitelisting
- Enforce least privilege + file integrity monitoring
- Adopt **zero-trust security model**

**Sudo Rights Countermeasures:**

- Strong password policy for sudo users
- Set `timestamp_timeout` to 0 (no password caching)
- Separate sudo-level accounts from regular admin accounts
- Test sudo users for arbitrary code execution risk
- Monitor sudo logins; centralize logging

**Defending against Spectre/Meltdown:**

- Regularly patch OS/firmware
- Continuous monitoring of critical apps/services
- Update ad-blockers/anti-malware
- Use DLP solutions
- Check manufacturer for BIOS updates
- Implement retpoline (compiler mitigation), speculative load hardening

---

## 4. Maintaining Access

### 🎯 Overview

> After gaining access + escalating privileges, attackers execute malicious applications (**"owning" the system**) to maintain access: **Backdoors, Crackers, Keyloggers, Spyware**. They hide these using **rootkits, steganography, NTFS data streams**.
> 

---

### ⌨️ Keyloggers

> Programs/hardware devices that record every keystroke. Can capture: login names, bank/credit card numbers, passwords, chat conversations, copy-paste clipboard content, website URLs.
> 

**Complete Keylogger Taxonomy (Diagram):**

```
Keystroke Loggers
├── Hardware Keystroke Loggers
│   ├── PC/BIOS Embedded
│   ├── Keylogger Keyboard
│   └── External Keylogger
│       ├── PS/2 and USB Keylogger
│       ├── Acoustic/CAM Keylogger
│       ├── Bluetooth Keylogger
│       └── Wi-Fi Keylogger
└── Software Keystroke Loggers
    ├── Application Keylogger
    ├── Kernel Keylogger
    ├── Hypervisor-based Keylogger
    ├── Form Grabbing Based Keylogger
    ├── Javascript Based Keylogger
    └── Memory Injection Based Keylogger
```

- **Hardware keyloggers:** Physically placed between keyboard and USB socket. Not OS-dependent → **cannot be detected by anti-keylogger software**. Easy to discover physically.
- **PC/BIOS Embedded:** Requires physical/admin-level access — modifies BIOS-level firmware
- **Keylogger Keyboard:** Hardware circuit attached to keyboard cable connector

**Hardware Keylogger Products:** KeyGrabber USB, KeyCarbon, Keyboard logger, KeyGhost, KEYKatcher

**Software Keyloggers (Windows):** Spyrix Personal Monitor (hidden from AV/anti-rootkit/anti-spyware)

**Anti-Keyloggers:** Zemana AntiLogger, GuardedID, KeyScrambler, Oxynger KeyShield, Ghostpress, SpyShelter

---

### 🕵️ Spyware

> Program that monitors user activities and sends data to a remote hijacker WITHOUT consent.
> 

**Spyware capabilities:** Steal personal info, monitor online activity, display pop-ups, redirect browser, decrease system security, connect to malicious sites, capture screenshots, activate microphone/webcam covertly, distribute spam/malware.

**Types:**

- **Email Spyware** — monitors/forwards all incoming/outgoing emails in stealth mode
- **Internet Spyware** — records all visited URLs, applications opened, blocks specific pages
- **Child-Monitoring Spyware** — tracks child's online/offline activity, real-time alerts on keyword triggers

**Desktop/Child-Monitoring Tools:** CurrentWare, FlexiSPY, NetVizor, SoftActivity Monitor, SoftActivity TS Monitor

**Anti-Spyware:** SUPERAntiSpyware, Kaspersky Total Security, SecureAnywhere Internet Security, Avast One, MacScan, Malwarebytes

---

### 🐛 Rootkits

> Software/programs that provide privileged (root-level) access to a system while hiding their presence.
> 

**How attackers place a rootkit:**

- Scanning for vulnerable computers/servers
- Wrapping rootkit in a special package (like a game)
- Installing via social engineering
- Launching zero-day attacks (privilege escalation, kernel exploitation)
- Phishing emails with malicious attachments

**Objectives of a rootkit:**

- Root the host system, gain remote backdoor access
- Mask attacker tracks and malicious process presence
- Gather sensitive data/network traffic
- Store other malware, act as server resource for bot updates
- Secure persistent access across reboots/updates
- Serve as platform for downloading additional malware
- Spy on user activity (keystrokes, screenshots, traffic)

### 📊 6 Types of Rootkits (CRITICAL — Memorize)

| Type | Ring | Description |
| --- | --- | --- |
| **Hypervisor-Level Rootkit** | Ring -1 | Exploits Intel VT/AMD-V; hosts target OS as a VM, intercepts all hardware calls |
| **Hardware/Firmware Rootkit** | — | Uses devices/platform firmware (hard drive, BIOS, network card) for persistent malware image |
| **Kernel-Level Rootkit** | Ring 0 | Highest OS privileges; modifies kernel code via device drivers (Windows) or loadable kernel modules (Linux); affects system stability |
| **Boot-Loader-Level Rootkit (Bootkit)** | — | Modifies/replaces legitimate boot loader; activates BEFORE OS starts — serious threat, facilitates hacking encryption keys |
| **Application-Level/User-Mode Rootkit** | Ring 3 | Runs as a user app; replaces application binaries or patches present applications |
| **Library-Level Rootkit** | — | Patches/hooks/supplants system calls with backdoor versions |
| **Memory Rootkit** (also mentioned) | — | Resides solely in RAM (volatile), leaves no disk traces — most elusive |

**How a rootkit works:** **System hooking** — replaces original function pointer with rootkit-provided pointer in stealth mode. **Inline function hooking** changes bytes of a function inside core system DLLs (kernel32.dll, ntdll.dll).

### 🔒 How to Defend Against Rootkits (14 Points)

1. Reinstall OS/applications from trusted source after backing up data
2. Maintain well-documented automated installation procedures
3. Perform kernel memory dump analysis
4. Harden the workstation/server
5. Educate staff to avoid untrusted downloads
6. Install network- and host-based firewalls
7. Ensure availability of trusted restoration media
8. Update and patch OSes, applications, firmware
9. Regularly verify integrity of system files (cryptographic digital fingerprint)
10. Regularly update antivirus and anti-spyware
11. Avoid logging in with administrative privileges
12. Adhere to principle of least privilege
13. Ensure AV software has rootkit protection
14. Avoid installing unnecessary applications; disable unused features/services

**Anti-Rootkits:** GMER, Stinger, Avast One, TDSSKiller, Malwarebytes Anti-Rootkit, AVG Rootkit Scanner

---

### 📁 NTFS Data Streams (ADS)

> **NTFS Alternate Data Stream (ADS)** — Windows hidden stream containing metadata (attributes, word count, author, access/modification time). Files with ADS are **impossible to detect** with native file-browsing (CLI, Explorer). Original file size doesn't change.
> 

**Structure:** File.txt → Attributes + Main Stream + Alternate Stream(s)

**Attacker uses:** Injects malicious code into existing files without altering functionality/size/display — hides rootkits/hacker tools, executes them undetected.

### 🔒 Defending Against NTFS Streams

- Move suspected files to a **FAT partition** to delete streams
- Use third-party file integrity checker (Tripwire File Integrity Manager)
- Use Stream Detector or GMER to detect streams
- Enable real-time antivirus scanning
- Use up-to-date antivirus software

**NTFS Stream Detection Tools:** Stream Detector, GMER, ADS Scanner, Streams (Microsoft Sysinternals), AlternateStreamView, StreamArmor

---

### 🖼️ Steganography

> **Steganography** = hiding a secret message within an ordinary message (cover) and extracting it at the destination. Unlike encryption, hides the EXISTENCE of the message.
> 

**Process:** Cover + Message → Embedding Function → **Stego Object** → Extracting Function → Cover Medium + Message

### 📊 Classification of Steganography

```
Steganography
├── Technical Steganography (physical/chemical methods)
│   ├── Invisible Ink
│   └── Microdots
└── Linguistic Steganography
    ├── Semagrams
    │   ├── Visual Semagrams
    │   └── Text Semagrams
    └── Open Codes
        ├── Covered Ciphers
        │   ├── Null Cipher
        │   └── Grille Cipher
        └── Jargon Code
```

### 🖼️ Image Steganography — LSB Insertion (CRITICAL)

> **Least-Significant-Bit (LSB) Insertion** — most common image steganography technique. LSB (rightmost bit) of each pixel holds secret data. Modification is imperceptible to human eye.
> 

**Process:**

1. Stego tool copies image palette using RGB model
2. Each pixel's 8-bit LSB is substituted with 1 bit of hidden message
3. New RGB color produced in copied palette
4. Pixel changed to new 8-bit binary number

**Example:** Hide letter "H" (binary `01001000`) in a 24-bit image by modifying the LSB of consecutive bytes.

**Other media types:** Document, audio, video, folder, spam/email, web, DNS, natural language steganography also exist.

---

### 🔍 Steganalysis — Attack Methods (CRITICAL TABLE)

| Attack | Description |
| --- | --- |
| **Stego-only** | Only the stego object is available for analysis |
| **Known-stego** | Attacker has access to the stego algorithm AND both cover medium and stego-object |
| **Known-message** | Attacker has access to the hidden message AND stego object |
| **Known-cover** | Attacker compares stego-object and cover medium to identify hidden message |
| **Chosen-message** | Generates stego objects from a known message using specific tools to identify the algorithm |
| **Chosen-stego** | Attacker has access to stego-object AND stego algorithm |
| **Chi-square** | Probability analysis to test whether stego object and original data are the same |
| **Distinguishing Statistical** | Analyzes embedded algorithm to detect distinguishing statistical changes + length of embedded data |
| **Blind Classifier** | Fed with original/unmodified data to learn resemblance from multiple perspectives |

**Challenges of steganalysis:** Suspect stream may/may not have encoded data; efficient detection is difficult; message might be pre-encrypted; irrelevant noise may be present.

**Steganography Detection Tools:** **zsteg** (PNG/BMP detection), StegoVeritas, Stegextract, StegoHunt MP, Steganography Studio, Virtual Steganographic Laboratory (VSL)

---

### 👑 Domain Dominance (Active Directory Persistence)

> Techniques attackers use to maintain long-term control over an AD domain.
> 

```
Domain Dominance Techniques:
├── Remote code execution
├── Abusing the Data Protection API (DPAPI)
├── Malicious replication
├── Skeleton key attack
├── Golden ticket attack
└── Silver ticket attack
```

**Remote Code Execution:** Attacker creates dummy process/user on target DC via WMI, adds to Admins group.

```bash
wmic /node:<DomainControllerName> process call create "net user /add PiratedProcess Du^^Y01"
PsExec.exe \\<DomainControllerName> -accepteula net localgroup "Admins" PiratedProcess /add
```

**Abusing DPAPI:** Windows DCs contain a master key to decrypt DPAPI-protected files — attacker attempts to obtain this master key from the DC.

**Skeleton Key Attack:** Memory-resident malware injecting false credentials into DCs to create a backdoor password. Enables attacker to validate as ANY legitimate user with a master password. Difficult to detect (mimics standard auth). Executed via Empire: `execute` triggers `powershell/persistence/misc/skeleton_key`.

**Golden Ticket Attack (CRITICAL):** Post-exploitation technique for **complete AD control**. Forges Ticket Granting Tickets (TGTs) by compromising the **KRBTGT** account.

Steps:

1. Obtain domain name and SID using `whoami`
2. Elevate to domain admin, steal KRBTGT's NTLM hash via **DCSync attack**:
    
    ```
    lsadump::dcsync /domain:<domain name> /user:krbtgt
    ```
    
3. Forge golden ticket with mimikatz:
    
    ```
    kerberos::golden /domain:<domain name> /sid:<SID> /rc4:<KRBTGT hash> /id:<value> /user:<username>
    ```
    
4. Maintain persistence by setting ticket validity

**Silver Ticket Attack:** Forges a TGS (not TGT) by extracting a **service account's** NTLM hash (not KRBTGT) from a compromised machine. More limited scope than Golden Ticket (only affects the targeted service). PAC validation is optional.

```
mimikatz "privilege::debug" "sekurlsa::logonpasswords"
```

**Maintaining Persistence via WMI Event Subscription:** Attackers use WMI event subscriptions to execute malicious content persistently, surviving reboots.

```bash
wmic /NAMESPACE:"\\root\subscription" PATH CommandLineEventConsumer CREATE Name="EthicalHacker", ExecutablePath="C:\Windows\System32\ethicalhacker.exe" ...
```

---

## 5. Clearing Tracks / Covering Tracks

### 🎯 Overview

> After hiding malicious files/maintaining access, the attacker's final step is removing any traces/tracks from the compromised system.
> 

---

### 🗑️ Clearing Logs

**Windows Event Viewer:** Manually clear Application/Security/System logs via GUI (Windows Logs → right-click → Clear Log)

**PowerShell:**

```powershell
Clear-EventLog "Windows PowerShell"                                 # Clear PowerShell event log
Clear-EventLog -LogName ODiag, OSession -ComputerName localhost, Server02   # Clear multiple log types
Clear-EventLog -LogName application, system -confirm                # Clear + display list
```

**CLI (wevtutil):**

```bash
wevtutil cl <log name>
```

**Meterpreter (Metasploit):**

```
meterpreter > clearev    # Wipes Application, System, and Security logs
```

**Batch utility:** `Clear_Event_Viewer_Logs.bat` — wipes security/system/application logs.

**Linux:**

```bash
# Navigate to /var/log directory, edit/delete plaintext log files
/var/log/<filename.log>
```

---

### 🕵️ Disabling Windows Functionality (Anti-Forensics)

**Disable Last Access Timestamp:**

```bash
fsutil behavior set disablelastaccess 1    # 1 = disabled, 0 = enabled
```

**Disable Windows Thumbnail Cache:** Local Group Policy Editor → Windows Components → File Explorer → "Turn off the caching of thumbnails in hidden thumbs.db files" → **Enabled**

**Disable Windows Prefetch Feature:** `services.msc` → find **SysMain (Superfetch)** service → Startup type → **Disabled**. (Prefetch stores data about used applications — can reveal installed/uninstalled malicious apps to forensic investigators.)

**Clear Online Tracks (Windows 11):**

- **Privacy Settings:** Settings → Personalization → Start → turn off "Show most used apps" and "Show recently opened items"
- **Registry:** `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer` → remove `RecentDocs` key

**Hiding User Accounts:** Registry Editor → `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon` → create nested keys (`<Account1>` → `<Account2>` → DWORD named `<UserName>`) to hide the account from the login screen.

---

### 🌐 Covert Channels / Anti-Forensic Communication

| Technique | Description |
| --- | --- |
| **HTTP Tunneling** | Victim acts as web client executing HTTP GET; attacker acts as web server; traffic appears normal (bypasses DMZ/firewall) |
| **Reverse ICMP Tunnels** | Uses ICMP echo/reply packets as carriers for TCP payload — bypasses firewalls that only check incoming (not outgoing) ICMP |
| **DNS Tunneling** | Encodes malicious content within DNS queries/replies to create a backchannel; exfiltrates data via compromised internal system acting as C2 |
| **TCP Parameters** | Hides data in TCP fields (e.g., IP Identification Field) — payload transferred bitwise, one character per packet |

---

### 🔒 Defending Against Covering Tracks (10-Point List — CRITICAL)

1. Activate logging functionality on all critical systems
2. Conduct periodic audits on IT systems to ensure logging complies with security policy
3. Ensure new events do NOT overwrite old entries when storage limit is exceeded
4. Configure appropriate/minimal permissions for reading/writing log files
5. Maintain a separate logging server on the **DMZ** to store logs from critical servers
6. Regularly update and patch OSes, applications, firmware
7. Close all unused open ports and services
8. Encrypt log files with **immutable logging** (cannot be altered without decryption key)
9. Set log files to **"append only"** mode to prevent unauthorized deletion
10. Periodically back up log files to **unalterable media**

Additional: Use restricted ACLs to secure log files.

---

## 6. Quick Exam Cheat Sheet

### 🔑 Authentication Mechanisms

```
SAM Database    → stores hashed passwords, locked file, SYSKEY encryption
NTLM            → 6-step challenge-response
Kerberos        → AS + TGS in KDC (stronger than NTLM, current default)
```

---

### 📊 4 Types of Password Attacks

```
Non-Electronic    → shoulder surfing, social engineering, dumpster diving
Active Online     → dictionary, brute-force, spraying, Kerberoasting, LLMNR poisoning
Passive Online    → wire sniffing, MITM, replay
Offline           → rainbow tables, distributed network attack
```

---

### 🎯 Kerberos Attacks

```
AS-REP Roasting  → targets accounts WITHOUT Kerberos pre-auth required
Kerberoasting    → cracks service account TGS tickets (RC4-encrypted)
Golden Ticket    → forges TGT via KRBTGT hash (full domain control)
Silver Ticket    → forges TGS via service account hash (limited to 1 service)
Skeleton Key     → injects backdoor master password into DC memory
```

---

### 💣 Buffer Overflow Registers

```
EBP = Extended Base Pointer   (StackBase)
ESP = Extended Stack Pointer  (next data element)
EIP = Extended Instruction Pointer (NEXT instruction — attack target!)
ESI = Extended Source Index
EDI = Extended Destination Index
```

### 🪟 Windows Buffer Overflow Steps

```
1. Spiking → 2. Fuzzing → 3. Identify offset → 4. Overwrite EIP →
5. Identify bad chars → 6. Identify right module → 7. Generate shellcode → 8. Gain root access
```

---

### 📊 Privilege Escalation Types

```
Horizontal = same-level access (User A → User B's data)
Vertical   = higher-level access (User → Admin functions)
```

---

### 🐛 6 Types of Rootkits

```
Hypervisor-Level    → Ring -1, hosts OS as VM
Hardware/Firmware   → BIOS, hard drive, network card
Kernel-Level        → Ring 0, highest OS privileges
Boot-Loader-Level   → Bootkit, activates before OS
Application-Level   → Ring 3, user-mode
Library-Level       → patches/hooks system calls
```

---

### ⌨️ Keylogger Types

```
Hardware: PC/BIOS Embedded | Keylogger Keyboard | External (PS/2, USB, Acoustic, Bluetooth, WiFi)
Software: Application | Kernel | Hypervisor-based | Form Grabbing | JavaScript | Memory Injection
```

---

### 🔍 Steganalysis Attacks

```
Stego-only        → only stego object available
Known-stego       → algorithm + cover + stego-object known
Known-message     → message + stego object known
Known-cover       → compares stego-object vs cover medium
Chosen-message    → generates stego objects from known message
Chosen-stego      → stego-object + algorithm known
Chi-square        → probability analysis
Distinguishing Statistical → analyzes embedded algorithm
Blind Classifier  → learns from unmodified data
```

---

### 🔥 Common Exam Scenarios

**Q: What database stores Windows password hashes?**
→ **SAM (Security Accounts Manager)** database

**Q: What's the difference between AS-REP Roasting and Kerberoasting?**
→ AS-REP targets accounts WITHOUT Kerberos pre-auth; Kerberoasting cracks TGS tickets for service accounts

**Q: What account must be compromised for a Golden Ticket attack?**
→ **KRBTGT** account

**Q: Which register is the primary target in a buffer overflow attack?**
→ **EIP** (Extended Instruction Pointer)

**Q: What's the difference between ASLR and DEP?**
→ ASLR randomizes memory addresses; DEP prevents code execution in non-executable memory regions

**Q: What technique chains existing code "gadgets" ending in RET instruction?**
→ **ROP (Return-Oriented Programming)**

**Q: What tool uses graph theory to map AD attack paths?**
→ **BloodHound**

**Q: Which rootkit type runs at Ring -1?**
→ **Hypervisor-Level Rootkit**

**Q: Which rootkit type is hardest to detect and leaves no disk traces?**
→ **Memory Rootkit** (resides only in RAM)

**Q: What technique hides data in the LSB of image pixels?**
→ **LSB (Least-Significant-Bit) Insertion** — image steganography

**Q: What steganalysis attack only has the stego object available?**
→ **Stego-only attack**

**Q: What Windows command disables last access timestamp?**
→ `fsutil behavior set disablelastaccess 1`

**Q: What Meterpreter command clears Windows logs?**
→ `clearev`

**Q: What NTFS feature can hide malicious code without changing file size?**
→ **Alternate Data Streams (ADS)**

**Q: What common ports are targeted in password spraying?**
→ **MSSQL (1433), SSH (22), FTP (21), SMB (445), Telnet (23), Kerberos (88)**

**Q: What Windows service must be disabled to stop Prefetch?**
→ **SysMain (Superfetch)**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 06*