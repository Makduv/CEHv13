# Chapter 6 - Enumeration

**Enumeration** = determining what services are running, then **extracting information** about those services.

Where scanning tells you a port is open and what's on it, enumeration goes further — it actively **pulls out concrete details**: users, shares, groups, software versions, configuration.

**Why users matter:** external-facing services often have **authentication requirements**, which means there are **user accounts**. Enumeration finds them — e.g. listing the users configured on a web server. Those usernames become targets for password attacks later.

## Services useful for enumeration

| Service | Role — why it's useful |
| --- | --- |
| **SMB** (Server Message Block) | Used on **Windows** for file/resource sharing and remote management. A rich source: shares, users, groups, domain and OS details |
| **SMTP** (Simple Mail Transfer Protocol) | The protocol that **sends email** between servers. Certain commands (`VRFY`, `EXPN`, `RCPT TO`) can be abused to **confirm whether a username/email exists** on the server |
| **SNMP** (Simple Network Management Protocol) | Used to **monitor and manage network devices** (routers, switches, printers). If community strings are weak/default (`public`), it leaks a huge amount — interfaces, routes, running processes, user accounts |

**MITRE ATT&CK note:** enumeration is categorised under the **Reconnaissance** tactic.

# Service Enumeration

**Service enumeration** = identifying the services running on a target system. The workhorse is nmap with **`-sV`**:

```
sudo nmap -sV 192.168.2.3
```

It shows the **open ports**, where they are, and specifics about each service including the **version** running — done by **grabbing the banners** and extracting the service name and version from them.

**Limitation:** sometimes the banner only reveals the **service**, with no application name or version. When that happens, use an **NSE script** to dig deeper.

**Example — SSH detail:** to enumerate the algorithms/ciphers supported across a service, use a script such as `ssl-enum-ciphers.nse`:

```
sudo nmap --script=ssl-enum-ciphers.nse 192.168.2.3
```

*(This one enumerates SSL/TLS ciphers; the equivalent for SSH is `ssh2-enum-algos.nse`. The point stands: scripts extract detail the plain banner doesn't give.)*

## Countermeasures

Each service has its own configuration settings, but at a high level these apply to all of them:

- **Firewalls** — restrict who can reach the service at all; if an attacker can't connect, they can't enumerate it
- **Authentication** — require it, so the service doesn't hand out information to anonymous connections
- **Reduce the information provided** — especially in **banners**: suppress or genericise version strings so `sV` and scripts learn as little as possible

---

# Cheatsheet — Commands

| Command | Purpose |
| --- | --- |
| `sudo nmap -sV 192.168.2.3` | Service + version detection via banner grabbing |
| `sudo nmap --script=ssl-enum-ciphers.nse <target>` | Enumerate SSL/TLS ciphers |
| `sudo nmap --script=ssh2-enum-algos.nse <target>` | Enumerate SSH algorithms |
| `sudo nmap -sV --script=banner <target>` | Grab raw service banners |

**Countermeasures:** firewall access · require authentication · suppress version info in banners.

# Remote Procedure Calls (RPC)

Normally a program calls functions that live **inside itself**. **RPC** lets a program call a function that lives **on another machine** — and it looks, to the programmer, almost like calling a local function. The RPC machinery hides the network in between.

So: **program on system A calls a procedure that actually runs on system B**, using the RPC protocol. The two processes talking can be on different machines (remote) or the same one (local).

**Why an attacker cares:** RPC services expose functions to the network, often with weak or no authentication, and enumerating them reveals **what programs are running and on which ports** — a map of what to attack next.

**Flavours you'll see:**

- **Sun RPC** — the classic Unix implementation
- **Java RMI** (Remote Method Invocation) — Java's version, a request-response protocol
- **CORBA** — the older cross-language standard that came before RMI

## SunRPC

RPC programs don't sit on fixed, well-known ports.That’s why it exists a directory service called **portmap** (a.k.a. **rpcbind**), listening on **port 111**.

1. An RPC program starts, picks a port, and **registers** itself with the portmapper ("I'm NFS, I'm on port 2049")
2. A client asks the portmapper on **port 111**: "which port is NFS on?"
3. The portmapper answers, and the client connects there

A classic user of this is **NFS** (Network File System) — Unix file sharing.

**`rpcinfo`** queries the portmapper to list every registered program and its port:

```
rpcinfo -p 192.168.2.3
```

Metasploit equivalent:

```
use auxiliary/scanner/misc/sunrpc_portmapper
```

**Why use the portmapper tools instead of nmap:**

- The portmapper gives you **program names** (nfs, mountd…), not just "port 2049 open" — you learn *what* it is
- nmap **won't find RPC services on their random high ports** unless you explicitly scan **all** ports (`p-`); the portmapper hands them to you directly

## Java RMI (Remote Method Invocation)

Java is very common in web applications, and it has its **own** built-in RPC — called **RMI**.

**The key difference:** RMI is the **object-oriented** version of RPC. Instead of just calling a function, whole **objects are passed** between the client and the server.

It has its own directory service — the Java equivalent of the portmapper — called **`rmiregistry`**, listening by default on **port 1099**. Programs register their remote objects there so clients can look them up.

Two supporting pieces make the "call an object across the network" illusion work:

- **Stub** — sits on the **client**, stands in for the remote object. You call the stub; it packages the call and sends it over the network
- **Skeleton** — sat on the **server**, received the call and invoked the real object (modern Java has merged this away, but the exam still names it)

**Enumerating RMI with Metasploit:**

```
use auxiliary/gather/java_rmi_registry
```

**BaRMIe** — a dedicated tool for **attacking and enumerating Java RMI** services (lists exposed objects and tries known attacks against them):

```
java -jar BaRMIe_v1.01.jar 192.168.2.3
```

## Java runtime note

- **`javac`** → **compiles** source code into an executable (bytecode)
- **`java`** → **runs** the executable

Inference for the attacker: if a host is running an **RMI registry and RMI services**, it has at least a **Java Runtime Environment (JRE)** installed. If it can also *compile* Java, it has the full **Java Development Kit (JDK)** — though you can't tell the version from this alone. Knowing Java is present, and roughly which components, tells you what kind of exploits might land.

---

# Cheatsheet

**Concepts**

| Term | Meaning |
| --- | --- |
| **RPC** | Call a procedure that runs on another machine as if it were local |
| **portmap / rpcbind** | Sun RPC directory service — maps RPC programs to their ports (**port 111**) |
| **rmiregistry** | Java's equivalent directory for RMI objects (**port 1099**) |
| **Stub / Skeleton** | Client-side proxy / server-side receiver that hide the network in RMI |
| **JRE vs JDK** | JRE runs Java; JDK also compiles it (`javac`) |

**Ports**

| Port | Service |
| --- | --- |
| **111** | portmap / rpcbind (Sun RPC) |
| **2049** | NFS |
| **1099** | Java RMI registry |

**Tools & commands**

| Command | Purpose |
| --- | --- |
| `rpcinfo -p 192.168.2.3` | List RPC programs + ports via the portmapper |
| `use auxiliary/scanner/misc/sunrpc_portmapper` | Metasploit Sun RPC enumeration |
| `use auxiliary/gather/java_rmi_registry` | Metasploit RMI enumeration |
| `java -jar BaRMIe_v1.01.jar <target>` | Enumerate/attack Java RMI services |
| `nmap -p- <target>` | Needed to catch RPC services on random high ports |

**Why portmapper > nmap for RPC:** gives program **names**, and finds services on random high ports nmap skips by default.

# Server Message Block (SMB)

## What SMB is

SMB's most popular use is **sharing files across a network** — but it does more than that.

SMB is an **application-layer** protocol that can run over different lower-layer protocols:

- **Transport (TCP)** → **TCP port 445** (modern, direct SMB)
- **Session (NetBIOS)** → **UDP 137/138** or **TCP 137/139** (older, SMB over NetBIOS)

It's used for communication between Windows systems — **file sharing, network management, system administration**.

**Why it's a goldmine for enumeration:** SMB supports authentication but **doesn't always require it** — it allows **null authentication** (anonymous), and systems **announce themselves**. Because SMB knows about **users, groups and shares** (directories shared on the network), a null session can reveal all of that without credentials.

**On Unix-like systems: Samba** provides SMB. Two processes:

- **smbd** — handles SMB itself
- **nmbd** — handles the **naming** side of interoperating with Windows

## Built-in NetBIOS utilities

⚠️ You need to be on the **same broadcast domain** — NetBIOS was designed for **LAN**, not WAN.

**`nbtstat`** (Windows only) — gather NetBIOS statistics. Output is a list of names, each with a **code** (the context the name exists in) and a **status**.

```
nbtstat -a billthecat      # by hostname
nbtstat -A 192.168.2.3     # by IP address
nbtstat -r                 # list resolved names
```

Resolved names come from **broadcast messages** or a **WINS** (Windows Internet Name Server).

**`nmblookup`** (Linux, Samba package) — look up names on the network or query WINS:

```
nmblookup -S -B 192.168.2.3 billthecat
```

- `B` — use the supplied **broadcast** address
- `R` — **recursive** lookup (use WINS)
- `S` — also return **node status**, not just the name status

## The `net` utility (Windows)

Used to connect to a shared drive and to **query using SMB messages**.

```
net statistics workstation
```

Shows statistics from the Workstation service — network communication info: bytes transferred, sessions started, sessions failed. Extracting full system configuration requires being on the **local network and joined to the domain**.

## Nmap SMB scripts

Nmap ships roughly **35 SMB-related scripts**.

```
sudo nmap --script=smb-os-discovery.nse 192.168.2.3
```

Scripts also enumerate **users, groups, services, processes and shares** — some require **authentication**.

**Null authentication note:** Windows **stopped allowing null sessions after Windows 7 / Server 2008 R2** — but the setting **can be re-disabled** (turned back on) by an admin, so you still test for it.

**Common share names:**

- **IPC$** — allows access to shared **pipes** (the null-session entry point)
- **C$** — an **administrative** share auto-created for the C: drive

## GUI and framework tools

**NetBIOS Enumerator** (GUI) — give it an IP range; it scans, and when it finds a system running SMB it queries it for as much as it can get: **hostname, IP, workgroup and domains**. It **won't** get the logged-in username — it's an **unauthenticated** scan.

**Metasploit** — many SMB modules:

```
use auxiliary/scanner/smb/smb_version         # SMB version
use auxiliary/scanner/smb/smb_enumusers_domain # users (better if authenticated)
use auxiliary/scanner/smb/smb_login            # test username/password combos
use auxiliary/scanner/smb/smb_enumshares       # list shares
```

## Other utilities

**nbtscan** — details about systems on the local network: **NetBIOS name, user, MAC address, IP**. Output can be made script-friendly with `-s` + a separator character:

```
nbtscan 192.168.2.0/24
```

**enum4linux** (Kali) — wraps the Samba tools to pull users, shares, groups and more:

```
enum4linux -S 192.168.2.3
```

## Countermeasures

- **Disable SMBv1** (legacy, insecure)
- **Enable host-based firewall**
- **Network firewall** — block 445/137–139 at the perimeter
- **Disable sharing** where not needed
- **Disable NetBIOS over TCP/IP**
- (Also: block **null sessions**, enforce authentication)

---

# Cheatsheet

**Ports**

| Port | Use |
| --- | --- |
| **TCP 445** | SMB direct over TCP |
| **TCP 137/139, UDP 137/138** | SMB over NetBIOS |

**Tools & commands**

| Command | Purpose | OS |
| --- | --- | --- |
| `nbtstat -A <IP>` | NetBIOS names by IP | Windows |
| `nbtstat -a <host>` | NetBIOS names by hostname | Windows |
| `nmblookup -S -B <IP> <name>` | Name lookup / WINS query | Linux (Samba) |
| `net statistics workstation` | Workstation service stats | Windows |
| `sudo nmap --script=smb-os-discovery.nse <IP>` | OS discovery via SMB | any |
| `nbtscan 192.168.2.0/24` | NetBIOS sweep (name, user, MAC, IP) | any |
| `enum4linux -S <IP>` | Full SMB enumeration | Kali |

**Metasploit modules**

| Module | Purpose |
| --- | --- |
| `scanner/smb/smb_version` | SMB/OS version |
| `scanner/smb/smb_enumusers_domain` | Enumerate users |
| `scanner/smb/smb_login` | Test credentials |
| `scanner/smb/smb_enumshares` | List shares |

**Key points**

- Null session = anonymous SMB access → users, groups, shares without creds; killed after Win7/2008 R2 but can be re-enabled
- **IPC$** = pipes (null-session target), **C$** = admin share
- Samba: **smbd** (SMB) + **nmbd** (naming)
- NetBIOS = LAN only, same broadcast domain
- Countermeasures: disable SMBv1, firewall 445/137-139, disable NetBIOS over TCP/IP, block null sessions

# Simple Network Management Protocol (SNMP)

Used to **monitor** network devices — but also to **set parameters** on an endpoint. Most used on **network equipment** like routers and switches. An **agent** is installed on the endpoint; because it exposes so much, SNMP is usually **blocked at firewalls** from outside.

## Versions (the security story)

| Version | Year | Security |
| --- | --- | --- |
| **SNMPv1** | 1988 | Binary protocol, **no encryption**, very weak auth via **community strings**. No concept of users |
| **SNMPv2** | — | Enhanced auth — but the common **v2c** variant **kept community strings**. Incompatible with v1 |
| **SNMPv3** | — | **Encryption** + **user-based authentication** — the only secure version |

**Community strings** are the weak point: they act as a shared password and the defaults are notoriously well known:

- **`public`** → read-only access
- **`private`** → read-write access

Read-write access via a guessed community string lets an attacker not just read config but **change it**.

## MIBs and OIDs

An agent serves up information stored in **MIBs (Management Information Bases)**. MIBs are structured with **ASN.1 (Abstract Syntax Notation One)**, and each node/data point gets a unique **identifier (OID)** — a dotted numeric path in a tree.

## Enumerating with snmpwalk

**`snmpwalk`** queries the agent and walks the MIB tree, pulling everything it's allowed to read — system name, kernel identifier, and much more.

```
snmpwalk -v 2c -c public 192.168.2.3
```

- `v` — version (`1`, `2c`, `3`)
- `c` — community string

**Interface table (`ifTable`)** — a particularly useful OID: lists **all network interfaces** on the system and how each is configured (a map of the device's connectivity).

## Countermeasures

- **Disable SNMP** if you don't need it
- If you do need it, **upgrade to SNMPv3**
- Then harden the implementation:
    - **Require authentication**
    - **Require encryption**
    - **Use firewalls** to restrict who can reach the SNMP port
- (Also: change default community strings, and don't expose SNMP to untrusted networks)

---

# Cheatsheet

**Versions**

| Version | Auth | Encryption |
| --- | --- | --- |
| v1 | Community string | No |
| v2c | Community string | No |
| v3 | User-based | **Yes** |

**Community strings:** `public` = read-only · `private` = read-write (defaults — change them).

**Ports:** UDP **161** (queries), UDP **162** (traps).

**Terms**

| Term | Meaning |
| --- | --- |
| **MIB** | Management Information Base — the data the agent serves |
| **OID** | Unique identifier for each MIB node (dotted numeric path) |
| **ASN.1** | Notation used to structure MIBs |
| **ifTable** | MIB table listing all interfaces + config |

**Command**

```
snmpwalk -v 2c -c public 192.168.2.3
```

**Countermeasures:** disable SNMP · upgrade to v3 · require auth + encryption · firewall the port · change default community strings.

# Simple Mail Transfer Protocol (SMTP)

SMTP works with **verbs**. The client sends a **verb** plus any necessary parameters to the SMTP server, and based on the verb the server knows how to handle what it received. Because some verbs make the server **confirm whether an address exists**, SMTP is useful for **enumerating users and email addresses**.

## Talking to the server manually

Connect straight to the SMTP port (**25**) with netcat:

```
nc 192.168.35.2 25
```

Start the conversation with **`HELO`** or **`EHLO`**, and the server returns a list of **capabilities** it offers.

## The enumeration verbs

| Verb | Use |
| --- | --- |
| **VRFY** | **Verify** a user exists. Not all servers have this enabled |
| **EXPN** | **Expand** a mailing list into its member addresses |
| **RCPT TO** (with **MAIL FROM**) | The server accepts or rejects the recipient → reveals whether the address exists |

Manual example:

```
nc 192.168.35.2 25
EHLO blah.com
VRFY admin
```

## Automating with Metasploit

The **smtp_enum** module takes a **wordlist** and does the same probing automatically:

```
use auxiliary/scanner/smtp/smtp_enum
```

It uses **VRFY** (or **MAIL TO / RCPT TO**) under the hood to verify each username.

## Countermeasures

Goal: stop attackers probing the mail server for valid users.

- **Disable VRFY** (and EXPN)
- **Ignore unknown addresses** — return the same response whether or not an address exists, so nothing leaks
- **Restrict information in headers** (software, versions, internal hostnames)
- **Disable open relays** — don't let the server relay mail for anyone
- **Implement email security** (SPF, DKIM, DMARC)

---

# Cheatsheet

**Port:** TCP **25** (also 587 submission, 465 SMTPS)

**Verbs**

| Verb | Purpose |
| --- | --- |
| `HELO` / `EHLO` | Start session, list server capabilities |
| `VRFY` | Verify a user exists |
| `EXPN` | Expand a mailing list |
| `MAIL FROM` / `RCPT TO` | Set sender/recipient — RCPT reveals valid addresses |

**Commands**

```
nc 192.168.35.2 25            # manual SMTP conversation
EHLO blah.com
VRFY <user>
use auxiliary/scanner/smtp/smtp_enum   # Metasploit, wordlist-driven
```

**Countermeasures:** disable VRFY/EXPN · uniform response to unknown addresses · strip header info · no open relays · SPF/DKIM/DMARC.

# Web-based Enumeration

The goal is to **identify the directories available on a website** — usually by taking a **wordlist** of known or expected directory names and checking each one against the server.

## Directory discovery

**dirb** — tests directory names from a wordlist against a web server:

```
dirb http://google.com
```

No guarantee of finding **all** directories — results depend entirely on the **wordlist**.

**Metasploit — brute_dirs** — fuzzes directory names the same way:

```
use auxiliary/scanner/http/brute_dirs
```

## WordPress enumeration

Metasploit has many HTTP modules. For a **WordPress** site, **wordpress_login_enum** enumerates users:

```
use auxiliary/scanner/http/wordpress_login_enum
```

- Wordlist files need to be in the **directory msfconsole was launched from** (its working/root directory)
- Run **`loot`** inside msfconsole to see the information you've gathered

But for WordPress you don't need Metasploit — Kali ships **wpscan**, purpose-built to enumerate **users, plugins and themes** (and known vulnerabilities in them):

```
wpscan --url https://google.com
```

## Countermeasures

Web servers can leak a lot.

- **Restrict the information provided** (banners, error messages, version strings)
- **Use appropriate access control** on directories and files
- **Disable directory listing** so the server doesn't reveal its contents when there's no index page

---

# Cheatsheet — Tools & Commands

| Tool | Purpose | Command |
| --- | --- | --- |
| **dirb** | Directory discovery from a wordlist | `dirb http://target.com` |
| **brute_dirs** (Metasploit) | Fuzz directory names | `use auxiliary/scanner/http/brute_dirs` |
| **wordpress_login_enum** (MSF) | Enumerate WordPress users | `use auxiliary/scanner/http/wordpress_login_enum` |
| **loot** (MSF) | Show gathered info inside msfconsole | `loot` |
| **wpscan** | Enumerate WordPress users, plugins, themes, vulns | `wpscan --url https://target.com` |

**Also worth knowing:** gobuster / feroxbuster / ffuf (faster modern directory & vhost fuzzers).

**Countermeasures:** restrict info leaked · access control · disable directory listing.

**Note:** msfconsole wordlists must sit in its launch directory · `loot` shows what you've collected.

# Enumeration with AI

AI can accelerate and augment the enumeration phase in several ways:

**Automated reconnaissance with AI** — AI-driven **OSINT** gathering: the AI **scrapes and correlates data** from public sources far faster than by hand, connecting scattered pieces (names, emails, domains, tech mentions) into a coherent picture of the target.

**Smart subdomain and service enumeration** — instead of blindly brute-forcing from a static wordlist, AI can **predict likely subdomain and service names** based on patterns it has learned (naming conventions, the organisation's existing assets), making enumeration more targeted and efficient.

**User and credential enumeration** — AI helps **generate likely usernames** (from naming patterns like first.last) and build **context-aware credential guesses**, improving the hit rate over generic lists.

**AI-powered fuzzing and payload generation** — AI can **generate and mutate fuzzing inputs and payloads** dynamically, adapting to how the target responds rather than firing a fixed set, which surfaces edge cases a static fuzzer would miss.

**Caveat:** AI output still needs **manual verification** — the same false-positive problem as any automated tooling, plus the risk of fabricated/hallucinated results. Treat it as a force multiplier for a human tester, not a replacement.