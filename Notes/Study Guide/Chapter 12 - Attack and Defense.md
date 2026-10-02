# Chapter 12 - Attack and Defense

# Web Application Attacks

## Architecture — Model/View/Controller (MVC)

Web applications have **programmatic components** (scripts, server-side code) unlike static sites which are just content and links.

MVC is the design pattern behind most web apps — it separates the application into three responsibilities:

| Component | Role | Maps to |
| --- | --- | --- |
| **Model** | The structure of the data — usually stored in a **relational database**. An **application server** manages the model by creating the structure and acting on it. Interaction with the database uses **SQL** (Structured Query Language). The database provides **persistent storage** | Database server |
| **Controller** | Receives requests from the user and decides what to do with them — routes them to the right logic. Handles the HTTP layer | Web server |
| **View** | The user interaction component — what the user sees and interacts with | Web browser |

The **application server** handles the **business logic** and can use various languages (Java, Python, PHP, .NET). Java application servers: **Tomcat, WildFly, WebLogic**.

**Why MVC matters for attacks:** each layer is a target. Attacks go against the **client** (view), the **web server** (controller), and the **data storage** (model/database).

## OWASP Top 10 (2021)

The **Open Web Application Security Project (OWASP)** is a collection of projects — the most famous is the **Top 10**, listing the most common vulnerabilities across web applications.

| # | Category | What it means |
| --- | --- | --- |
| **A01** | Broken Access Control | Giving too many permissions, bypassing access control checks, privilege escalation, forced browsing |
| **A02** | Cryptographic Failures | No encryption, weak keys, poor/outdated algorithms, improperly validated certificates |
| **A03** | Injection | SQL injection, XSS, command injection — untrusted data sent to an interpreter |
| **A04** | Insecure Design | Security was **not considered** during design — no amount of patching fixes a bad architecture |
| **A05** | Security Misconfiguration | Lack of hardening, default credentials left in place, unnecessary features enabled |
| **A06** | Vulnerable/Outdated Components | Unpatched software, poor vetting of libraries and frameworks |
| **A07** | Identification & Authentication Failures | Credential stuffing, brute force, passwords in cleartext, poor session handling |
| **A08** | Software & Data Integrity Failures | Supply chain attacks, unverified code, insecure CI/CD pipelines |
| **A09** | Security Logging & Monitoring Failures | Insufficient logging, no alerting — breaches go undetected |
| **A10** | Server-Side Request Forgery (SSRF) | The app doesn't validate a user-provided URL → sends requests to **unexpected destinations** (internal services, metadata endpoints) |

## XML External Entity (XXE)

Falls under: **Security Misconfiguration (A05)**.

**What it is:** an attacker manipulates data sent to the server to get results not expected or allowed by the application.

**Background:** complex data is often structured in **XML** (Extensible Markup Language) — a common way to pass data back and forth with a server. XML supports **entities**, including **external entities** that reference outside resources (files, URLs).

**The attack:** the XML parser on the server is configured to **allow processing of external entities**. The attacker sends XML containing a malicious entity definition that reads a local file (like `/etc/passwd`) or reaches an internal service. The server processes it because it accepts data from the client **without validation**.

**Remediation:**

- **Input validation** on the server side (client-side validation is bypassed with an interception proxy)
- **Disable external entity processing** in the XML parser — external resources should be handled by the application code after proper evaluation
- Use simpler data formats like **JSON** where possible

## Cross-Site Scripting (XSS)

Falls under: **Injection (A03)**.

**What it is:** using the web server to attack the **client side**. The attacker injects a code fragment (usually JavaScript inside `<script>` tags) into an input field, and that code **executes in the browser** of a user visiting the page.

### Three types (all target the user — the difference is storage)

| Type | How it works |
| --- | --- |
| **Stored (persistent)** | The script is **saved on the server** (in a database, a comment field, a forum post) and displayed to **every user** who visits that page |
| **Reflected** | The script is **not stored** — it's included in the **URL parameters** and reflected back by the server in the response. The victim must click a crafted link |
| **DOM-based** | The script manipulates the **Document Object Model** (the in-browser representation of the page) directly — the vulnerability is in the **client-side JavaScript**, not the server. The payload never reaches the server |

**Reflected XSS example:**

```
http://www.badsite.com/foo.php?param=<script>alert("Hi");</script>
```

**URL encoding:** special characters need to be encoded for URLs. Each ASCII character has a numeric value, rendered in **hexadecimal** with a `%` prefix:

- Space → ASCII 32 → hex 20 → **`%20`**
- `<` → ASCII 60 → hex 3C → **`%3C`**

This opens the door to **encoding attacks** — obfuscating the payload so filters don't recognise it.

**DOM:** the Document Object Model is how the browser represents every element on the page as an **object** — you can **get and set** properties of those objects with JavaScript.

## SQL Injection

Falls under: **Injection (A03)**.

**SQL** (Structured Query Language) makes programmatic requests to a relational database server. SQL injection is an attack against the **database** that takes advantage of **poor programming practices** — when user input is passed **directly into an SQL query** without sanitisation.

Data can be **altered, damaged, extracted**, or authentication can be **bypassed**.

**Example — a legitimate search query:**

```sql
SELECT * FROM inventory_table WHERE description='$searchstr';
```

The attacker needs to make their input **work within the context of the existing query**.

**Classic injection:**

```
' OR '1'='1
```

This closes the quote and adds a boolean condition that's always true → **returns all rows**.

**Comments to cut off the rest of the query:**

- MySQL, MariaDB, Oracle, MS SQL Server: `-` (double dash)
- MySQL also accepts: `#`

```
' OR 1=1 --
```

**Reconnaissance step:** you need to know which **database server** is running. Either from earlier recon, or by submitting invalid SQL — a well-configured app returns nothing, but often you get an **error message revealing the database type**.

### Blind SQL Injection

Not all queries produce visible output. **Blind SQL injection** relies on **observing behaviour differences**:

1. Run a normal search: `jodie` → see normal results
2. Append a **false** condition: `jodie' AND 1=2 --` → different result (or no results)
3. Append a **true** condition: `jodie' AND 1=1 --` → normal results return

If results differ between steps 2 and 3, the page is **vulnerable** to SQL injection.

**Remediation:**

- **Input validation** — screen all user input
- **Parameterised queries** (prepared statements) — the query structure is fixed; user input is treated as data, never as code
- **Stored procedures** — prebuilt database functions that limit what queries can run

## Command Injection

Similar to XXE — the target is the **operating system**. The application takes user input and passes it to a **system function** or **eval function**, which hands it to the OS to execute. With no input validation, the attacker can run **arbitrary commands**.

**Chaining commands:**

- Linux: `;` (run next regardless) — `ls; cat /etc/passwd`
- Windows: `&` (run next regardless) — `dir & type C:\secrets.txt`
- Both: `&&` (run next only if first **succeeds**) — `command1 && command2`
- Both: `||` (run next only if first **fails**) — `command1 || command2`

**Remediation:**

- **Input validation**
- Never pass user input **directly** to system calls

## Directory / File Traversal

The web server serves files from a **root directory** (e.g. `/var/www/html`). A traversal attack tries to **break out of that jail** to access files elsewhere on the operating system.

**How it works:** `../` pops up one directory level. Chain enough of them and you reach the filesystem root:

```
../../../etc/passwd
```

**Remediation:** sanitise input, block `../` sequences, use chroot or containerisation to limit what the web server can see.

## URL Manipulation

Parameters are passed in the URL after the `?`:

```
www.bank.com/account.php?accno=123456
```

An attacker can **change the parameter values** directly — modifying `accno=123456` to `accno=123457` might give access to **another user's account** if the server doesn't verify authorisation.

**Forced browsing** — using a **dictionary of page/resource names** to send requests and discover hidden pages that exist but aren't linked:

```
/admin
/backup
/config.php
/.env
```

This is what **dirb** and **gobuster** do. The pages aren't meant to be public but exist on the server — if there's no access control, anyone who finds the URL can reach them.

## Web Application Protections

**Input validation** — define what the input **should** look like (type, length, format, allowed characters) and reject everything else. The first and most important defence.

**Regular expressions (regex)** — a pattern-matching language used to identify malicious patterns in input. Powerful but risky: a **poorly written regex** can be exploited for **ReDoS** (Regular Expression Denial of Service) — the engine gets stuck evaluating a crafted input and consumes all CPU.

**Web Application Firewall (WAF)** — an application-layer firewall that reads HTTP requests and matches them against **patterns of known attacks**. **mod_security** is an open-source WAF module that loads with Apache.

**mod_security's phased approach:**

| Phase | What it inspects |
| --- | --- |
| 1 | Request **headers** |
| 2 | Request **body** |
| 3 | Response **headers** |
| 4 | Response **body** |
| 5 | **Logging** |

**Limitation:** a WAF relies entirely on its **rules** — if the rules are poorly written or a rule doesn't exist for a particular attack, it gets through. A WAF is a layer of defence, not a substitute for secure code.

---

# Cheatsheet

**OWASP Top 10 (2021)**

| # | Name |
| --- | --- |
| A01 | Broken Access Control |
| A02 | Cryptographic Failures |
| A03 | **Injection** (SQLi, XSS, command injection) |
| A04 | Insecure Design |
| A05 | **Security Misconfiguration** (XXE lives here) |
| A06 | Vulnerable/Outdated Components |
| A07 | Identification & Authentication Failures |
| A08 | Software & Data Integrity Failures |
| A09 | Security Logging & Monitoring Failures |
| A10 | SSRF |

**Attack types**

| Attack | Target | Category |
| --- | --- | --- |
| **XXE** | Server (XML parser) | A05 Misconfiguration |
| **XSS (Stored)** | Client (browser) — script saved on server | A03 Injection |
| **XSS (Reflected)** | Client — script in URL, reflected by server | A03 Injection |
| **XSS (DOM)** | Client — script manipulates DOM client-side | A03 Injection |
| **SQL Injection** | Database | A03 Injection |
| **Blind SQLi** | Database (no visible output) | A03 Injection |
| **Command Injection** | Operating system | A03 Injection |
| **Directory Traversal** | Filesystem (`../../../etc/passwd`) | A01 Access Control |
| **URL Manipulation** | Application logic (change parameters) | A01 Access Control |
| **Forced Browsing** | Hidden pages (dirb, gobuster) | A01 Access Control |
| **SSRF** | Internal services via unvalidated URLs | A10 |

**SQL injection**

```
' OR '1'='1                  # basic — always true
' OR 1=1 --                  # with comment (MySQL/MSSQL/Oracle)
' OR 1=1 #                   # with comment (MySQL only)
```

Comments: `--` (MySQL, MariaDB, Oracle, MSSQL) · `#` (MySQL)

**Command injection chaining:** `;` (Linux) · `&` (Windows) · `&&` (if success) · `||` (if fail)

**URL encoding:** character → ASCII decimal → hex → `%hex` (space = `%20`, `<` = `%3C`)

**Defences**

| Defence | Protects against |
| --- | --- |
| **Input validation** | Everything — the first line |
| **Parameterised queries** | SQL injection |
| **Disable external entities** | XXE |
| **WAF (mod_security)** | Known attack patterns (rules-dependent) |
| **Output encoding** | XSS |
| **Regex filtering** | Malicious patterns (watch for ReDoS) |
| **Access control checks** | Forced browsing, URL manipulation |

**MVC mapping:** View = browser · Controller = web server · Model = database + app server

**Exam reflexes**

- "Script stored on server, affects all visitors" → **stored/persistent XSS**
- "Script in URL, victim clicks a link" → **reflected XSS**
- "Client-side JS manipulates the page" → **DOM-based XSS**
- "Input passed directly to SQL" → **SQL injection** → fix with **parameterised queries**
- "Input passed to a system call" → **command injection**
- "XML parser processes external entities" → **XXE**
- "`../../../etc/passwd`" → **directory traversal**
- "Change `?accno=123456` to another number" → **URL manipulation / IDOR**

# Denial-of-Service Attacks

Goal: take an application **out of service** so legitimate users can't use it.

## Bandwidth Attacks

Generate a huge volume of traffic that **overwhelms the network connection** the service is using.

**The problem:** your target probably has **far more bandwidth** than you do alone. The solution: use a **botnet** — a large number of compromised systems all sending requests simultaneously → **Distributed Denial of Service (DDoS)**. Every modern bandwidth-based DoS is a DDoS.

### Amplification attacks

Use a protocol that generates a **large response from a small request**, and **spoof the source address** so all replies go to the victim.

**Smurf attack** — sends an ICMP echo request to a **broadcast address** with the victim's IP as the source. Every host on that network replies to the victim. Target a large network → generate massive response volume. After the first response, every successive one is called a **duplicate**.

**DNS amplification** — DNS uses **UDP** (connectionless, no handshake) → source address spoofing is **trivial**. A small DNS query can produce a response **many times larger** → all directed at the victim.

Other amplification protocols: **NTP, SNMP, memcached, SSDP**.

### Tools

**LOIC (Low Orbit Ion Cannon)** — available for Linux and Windows. Generates flood traffic toward a target. Simple to use, often associated with hacktivist campaigns.

### Defence

- Contact your **ISP** — they can filter upstream before traffic reaches you
- Use a **load balancing / DDoS mitigation service** (Akamai, Cloudflare)
- **Cloud providers** (AWS, Azure, Google Cloud) can absorb DDoS traffic at scale
- Rate limiting, blackholing, traffic scrubbing

## Slow Attacks

Instead of overwhelming bandwidth, these attacks **exhaust server resources** (connection threads, memory) with minimal traffic — harder to detect because they look like legitimate slow clients.

### Slowloris

Sends **incomplete HTTP requests** to a web server. The 3-way handshake completes normally, but the HTTP request is never finished — the server keeps the connection open, waiting for the rest. Once all available **threads** are consumed, the server can't accept new connections.

### R-U-Dead-Yet (RUDY)

Same concept but targets the **HTTP body** rather than headers. Sends a POST request with a very long `Content-Length`, then transmits the body one byte at a time — keeps the connection alive indefinitely.

### Apache Killer

Sends requests asking for **overlapping byte ranges** of a resource. The server tries to assemble the overlapping ranges in memory → **memory consumption** spikes → crash or DoS.

### Slow Read

Makes a request for a **large file**, then reads the response back in **tiny segments** — the server keeps the connection open for an extremely long time waiting for the client to finish receiving.

### Tool

**slowhttptest** — can simulate all of these: Slowloris, RUDY, Apache Killer and slow read attacks.

## Legacy Attacks (no longer effective)

These are historical — modern operating systems and network stacks handle them, but they appear on the exam.

| Attack | How it worked |
| --- | --- |
| **SYN Flood** | Filled up the server's **connection buffers** with half-open connections (SYN sent, SYN-ACK received, ACK never sent). The server ran out of resources to track pending connections. Mitigated by **SYN cookies** |
| **LAND** (Local Area Network Denial) | Source and destination IP/port of a TCP packet are **the same** → the system gets put into a **loop** trying to respond to itself → crash |
| **Fraggle** | Like Smurf but with **UDP** — spoofed UDP messages sent to a broadcast address → amplified replies flood the victim |
| **Teardrop** | Sends fragmented IP packets with **overlapping fragment offsets** → when the OS tries to reassemble them, it can't handle the overlap → OS-level crash |

---

# Cheatsheet

**Attack types**

| Category | Attack | Mechanism |
| --- | --- | --- |
| **Bandwidth / DDoS** | Botnet flood | Massive traffic from many sources |
| **Amplification** | Smurf (ICMP) | Echo request to broadcast, spoofed source |
| **Amplification** | DNS amplification | Small query → large response, spoofed source (UDP) |
| **Slow** | Slowloris | Incomplete HTTP headers → exhaust threads |
| **Slow** | RUDY | Slow HTTP body (POST) → exhaust threads |
| **Slow** | Apache Killer | Overlapping byte ranges → memory exhaustion |
| **Slow** | Slow Read | Request large file, read in tiny segments |
| **Legacy** | SYN Flood | Half-open connections fill buffer |
| **Legacy** | LAND | Source = destination → loop/crash |
| **Legacy** | Fraggle | UDP to broadcast (Smurf but UDP) |
| **Legacy** | Teardrop | Overlapping fragments → OS crash |

**Tools**

| Tool | Purpose |
| --- | --- |
| **LOIC** | Bandwidth flood (Linux/Windows) |
| **slowhttptest** | Slowloris, RUDY, Apache Killer, slow read |

**Defences**

| Defence | What it handles |
| --- | --- |
| **ISP filtering** | Upstream DDoS mitigation |
| **CDN / DDoS service** (Cloudflare, Akamai) | Absorb and scrub flood traffic |
| **Cloud providers** (AWS, Azure, GCP) | Scale to absorb attacks |
| **SYN cookies** | SYN flood (legacy) |
| **Rate limiting** | Both bandwidth and slow attacks |
| **Connection timeouts** | Slow attacks (close idle connections faster) |

**Exam reflexes**

- "ICMP to broadcast, spoofed source" → **Smurf**
- "UDP to broadcast, spoofed source" → **Fraggle**
- "Incomplete HTTP headers exhaust threads" → **Slowloris**
- "Slow POST body" → **RUDY**
- "Overlapping byte range requests" → **Apache Killer**
- "Source IP = destination IP" → **LAND**
- "Overlapping fragment offsets crash the OS" → **Teardrop**
- "Small DNS query, large response to victim" → **DNS amplification**
- Smurf = ICMP · Fraggle = UDP (same idea, different protocol)
- Every modern bandwidth DoS = **DDoS** (uses a botnet)

# Application Exploitation

An attacker gains **control of the execution path** of a program. This commonly happens when the application receives **invalid input** and doesn't validate it.

These attacks work because of poor input handling **and** because of how programs are **placed into memory** by the operating system.

**Arbitrary code execution** = the attacker gets the system to run **whatever code they choose** — not just crash, but execute their payload. It's the most dangerous outcome of an application vulnerability.

## Buffer Overflow

Takes advantage of a memory structure called the **stack**.

### What the stack is

The **stack** = a section of memory where data is stored while program functions are executing. Think of it like a pile of plates — you can only add to the **top** or take from the **top**. You can't reach anything underneath without removing what's above it.

Every time a **function is called**, a new **stack frame** is created containing:

```
┌──────────────────────┐  ← top of stack
│  Local variables      │  (buffers for data)
│  Parameters           │
│  Return address       │  ← where to jump back when the function ends
└──────────────────────┘
```

The **return address** is critical — it tells the program **where to continue executing** after the function finishes.

### How the overflow works

Each local variable gets a fixed amount of space on the stack (a **buffer**). Data is copied into that buffer. If the data is **larger than the buffer**, it **overflows** and overwrites everything else on the stack — including the **return address**.

If the attacker carefully controls what overflows, they can **replace the return address** with the address of their own code (**shellcode**). When the function ends and the program jumps to the "return address," it jumps to the attacker's code instead.

**Segmentation fault** = what happens when the overwritten return address points somewhere **outside the program's memory segment** — the program crashes. A crash means the overflow worked but the address wasn't useful. The attacker's job is to make it point somewhere **useful**.

### The goal

Inject **shellcode** (the attacker's payload) into memory and overwrite the return address to point at it → the program executes the shellcode instead of returning normally.

### Protections against buffer overflow

| Protection | How it works |
| --- | --- |
| **Non-executable stack (NX/DEP)** | The OS marks the stack as **non-executable** — even if shellcode lands there, it can't run. Any jump to an address in the stack segment fails |
| **ASLR** (Address Space Layout Randomization) | Randomises where programs, libraries and the stack are placed in memory **each time the program runs** — the attacker can't predict where to jump |
| **Stack canary** | A known value placed **just before the return address** on the stack. Before the return address is used, the canary is checked. If it's been **altered** (by an overflow), the program terminates instead of jumping |

### Return-to-libc attack

A technique that **bypasses the non-executable stack**.

**libc** = the standard C library — contains all the standard functions (like `system()`, `exec()`) and is **loaded into memory** to be shared across all C programs.

Instead of putting shellcode on the stack, the attacker overwrites the return address with the address of a **libc function** (like `system("/bin/sh")`). The stack never executes code — it just redirects to a legitimate library function that does what the attacker wants.

**Why it works:** libc is executable code in a legitimate memory segment — NX/DEP doesn't block it.

**Defence:** **ASLR** randomises where libc is loaded, so the attacker can't easily predict the address. But if ASLR is weak or can be leaked, the attack still works.

## Heap Spraying

### Stack vs heap

The **stack** is for data known at **compile time** — variables declared in the source code with known sizes. But some data is only created at **runtime** (user input, dynamic objects). That data goes on the **heap**.

|  | Stack | Heap |
| --- | --- | --- |
| **When allocated** | Compile time | Runtime |
| **Structure** | Ordered (LIFO) | Unstructured |
| **Contains return address?** | Yes | **No** |
| **Size known?** | Yes | No — allocated as needed |

### How heap spraying works

The heap has **no return address** to overwrite directly, but the attacker can still use it:

1. The attacker **fills large areas of the heap** with copies of their shellcode (the "spray" — hundreds or thousands of copies). This makes the shellcode occupy a **large, predictable region** of memory
2. Because the heap is allocated in a **known area** of memory and the spray is large, the attacker can **predict roughly where** the shellcode sits
3. The attacker then uses a **separate vulnerability** (like a buffer overflow) to redirect execution to the heap region — with so many copies of the shellcode sprayed across it, even an approximate address is likely to **land on one of them**

Think of it as: instead of trying to hit a tiny target, you **paint the entire wall** so any dart lands on your colour.

**Why it works:** the heap is usually **executable** (unlike the stack with NX/DEP), and filling it with repeated shellcode makes the target address easy to guess even with some randomisation.

---

# Cheatsheet

**Concepts**

| Term | Meaning |
| --- | --- |
| **Buffer overflow** | Input exceeds buffer size → overwrites return address on the stack |
| **Shellcode** | Attacker's payload — code they want executed |
| **Stack frame** | Local variables + parameters + return address for one function call |
| **Segfault** | Program crashes because the overwritten return address is invalid |
| **Arbitrary code execution** | Attacker runs whatever code they choose |
| **Return-to-libc** | Redirect to a libc function instead of shellcode — bypasses NX/DEP |
| **Heap spraying** | Fill the heap with many copies of shellcode so any jump into the region hits one |
| **libc** | Standard C library loaded into memory — contains `system()`, `exec()`, etc. |

**Protections**

| Protection | What it stops | What bypasses it |
| --- | --- | --- |
| **NX / DEP** (non-executable stack) | Shellcode on the stack | Return-to-libc (jumps to legitimate code instead) |
| **ASLR** | Predictable memory addresses | Memory leaks that reveal the layout |
| **Stack canary** | Buffer overflows (detects overwrite before return) | Canary value leak or information disclosure |

**Stack frame layout (top → bottom):**

```
Local variables / buffers  ← overflow starts here
Saved registers
Stack canary               ← checked before return
Return address             ← attacker's target
Function parameters
```

**Exam reflexes**

- "Overwrite the return address" → **buffer overflow**
- "Fill memory with shellcode copies" → **heap spraying**
- "Bypass non-executable stack" → **return-to-libc**
- "Randomise memory layout" → **ASLR**
- "Value checked before return" → **stack canary**
- Stack = compile-time, ordered, has return address · Heap = runtime, unstructured, no return address
- Heap spraying works because the heap is usually **executable** and a large spray is **predictable**

# Lateral Movement

Once inside, the attacker doesn't stay on one system — they move **sideways** through the network to reach more valuable targets, more data, and more control.

## The attack lifecycle (recap)

1. Initial reconnaissance
2. Initial compromise
3. Establish foothold
4. **Escalate privileges** ↻
5. **Internal reconnaissance** ↻
6. **Move laterally** ↻
7. **Maintain presence** ↻
8. Complete mission

**Steps 4–7 are a cycle** that repeats as needed — each compromised system opens the door to more systems, which require their own escalation, recon and persistence.

## Credential Stuffing

Collect **usernames and passwords** (from the compromised system, from memory dumps, from breach databases) and **try those credentials on other systems**. Works because people **reuse passwords** across systems — a credential that works on one machine often works on others.

Not the same as brute force: credential stuffing uses **known, valid credentials**, not random guesses.

## Lateral Movement Techniques

| Technique | How it works |
| --- | --- |
| **Credential stuffing / reuse** | Use harvested credentials on other systems (SSH, RDP, SMB) |
| **Pass the hash (PtH)** | Use the **NTLM hash** directly to authenticate without knowing the plaintext password — many Windows protocols accept the hash itself |
| **Pass the ticket (PtT)** | Use a stolen **Kerberos ticket** (TGT or service ticket) to authenticate to other services without needing the password |
| **Kerberoasting** | Request service tickets for SPNs, crack them offline → get service account passwords → access those services |
| **Golden ticket** | Forge a **TGT** using the KRBTGT account hash → unlimited access to any service in the domain |
| **Silver ticket** | Forge a **service ticket** using the service account hash → access to that specific service |
| **Remote services** | Use legitimate remote access (RDP, SSH, WinRM, PSExec, SMB shares) with stolen credentials to jump between systems |
| **PsExec / SMBExec** | Execute commands on a remote Windows system via SMB — behaves like a remote shell |
| **WMI / PowerShell remoting** | Use built-in Windows management tools to run commands on remote systems — LOTL, harder to detect |
| **Token impersonation** | Steal a logged-in user's **access token** and use it to act as that user on other systems |
| **Internal pivoting** | Route traffic through a compromised host to reach networks you couldn't access directly (autoroute in Meterpreter) |

## Why lateral movement is hard to detect

- Uses **legitimate tools and protocols** (RDP, SMB, PowerShell, WMI) — looks like normal admin activity
- Stolen **valid credentials** pass authentication checks normally
- Traffic stays **inside the network** — perimeter defences don't see it

## Defence

- **Network segmentation** — limit what each system can reach
- **Least privilege** — accounts only get access to what they need
- **Privileged Access Management (PAM)** — vault and rotate admin credentials
- **MFA everywhere** — a stolen password alone isn't enough
- **Monitor internal traffic** — look for unusual authentication patterns, credential use across many systems, or admin tools used at odd times
- **Disable unnecessary remote services** (PSExec, WinRM) where not needed

---

# Cheatsheet

**Techniques**

| Technique | What moves |
| --- | --- |
| **Credential stuffing** | Known username/password on new systems |
| **Pass the hash** | NTLM hash (no plaintext needed) |
| **Pass the ticket** | Kerberos ticket (TGT or service ticket) |
| **Kerberoasting** | Crack service tickets → service account passwords |
| **Golden ticket** | Forged TGT (KRBTGT hash) → domain-wide access |
| **Silver ticket** | Forged service ticket → one specific service |
| **PsExec / WMI / PowerShell** | Remote execution via legitimate admin tools |
| **Token impersonation** | Stolen access token of a logged-in user |
| **Pivoting** | Route through a compromised host to reach new networks |

**Lifecycle loop:** escalate → internal recon → move laterally → maintain presence → repeat.

**Exam reflexes**

- "Use the hash directly, no password" → **pass the hash**
- "Use a Kerberos ticket" → **pass the ticket**
- "Forge a TGT with KRBTGT" → **golden ticket**
- "Forge a service ticket" → **silver ticket**
- "Try known credentials on new systems" → **credential stuffing**
- "Steps 4–7 repeat" → the lateral movement cycle
- Lateral movement is hard to detect because it uses **legitimate tools and valid credentials**

# Defense in Depth / Defense in Breadth

## Defense in Depth

Goal: **delay an attacker** to give defenders time to detect the intrusion and boot the attacker out. Uses **several layers of protection** — if one fails, the next catches it.

**Problems with depth alone:**

- Doesn't factor in **how the enterprise would know** an attack is happening in order to mount a response — layers delay, but who's watching?
- **Misunderstands modern attacks** — social engineering bypasses network layers entirely; the attacker walks through the front door with stolen credentials
- **Too many devices** that don't work well together — each layer is its own product, its own console, its own team. Not a **unified approach** to securing the enterprise

## Defense in Breadth

Factors in a **broader range of attack types** with the understanding that threats aren't just at the network or transport layer — attacks are far more likely at the **application layer** (phishing, web app exploits, credential theft).

Defense in breadth considers the **overall needs of the enterprise**: people, process, technology, and the full attack surface — not just stacking boxes on the network path.

## Addressing both — UTM

To combine depth and breadth: introduce a **Unified Threat Management (UTM)** device that consolidates multiple security functions:

- **Firewall**
- **IDS/IPS**
- **Anti-malware**

One device, one console — a unified approach. The trade-off: **single point of failure** and potential performance bottleneck.

## DMZ (Demilitarized Zone)

A common network architecture pattern: the DMZ **isolates systems** that may be untrustworthy or that allow **direct access from outside** the network (web servers, mail servers, DNS).

**Modern shift:** internet-facing services are increasingly being **removed from on-premise** and **outsourced to cloud providers** — the DMZ is shrinking or disappearing in many organisations.

## Honeypots

One system you may find in a DMZ is a **honeypot** — a system left out as **bait** for attackers.

**Purpose:**

- **Keep attackers occupied** — feed them bogus but apparently sensitive information while the real systems stay untouched
- **Observe the attacker** — learn their tools, techniques and objectives
- Sometimes called **tar pits** — once you're in, you're stuck (designed to slow the attacker down and waste their time)

A network of honeypots = a **honeynet**.

---

# Cheatsheet

| Concept | Focus |
| --- | --- |
| **Defense in depth** | Multiple layers on the same path — delay the attacker |
| **Defense in breadth** | Broader view — application layer, people, process, full attack surface |
| **UTM** | Combines FW + IDS + anti-malware in one device |
| **DMZ** | Isolates internet-facing systems from the internal network |
| **Honeypot** | Bait system to distract, observe and trap attackers |
| **Honeynet** | Network of honeypots |
| **Tar pit** | Honeypot designed to slow and trap |

**Key points**

- Depth alone misses social engineering and application-layer attacks
- Breadth accounts for the **full attack surface** and **modern methods**
- DMZ is shrinking as services move to the **cloud**
- Honeypots provide **intelligence** on attacker behaviour — not just defence

# Defensible Network Architecture

The idea is **not just about keeping attackers out** — it's about being able to **monitor and control them once they're in**.

## Core principle

Build the network in a way that allows for **monitoring the environment** → provides the ability to **alert on anomalous behaviour** → gives operations teams **visibility** and the ability to **respond**.

## What it requires

**Controls for visibility** — put in place logging, monitoring and detection so operations teams can actually **see** what's happening across the environment.

**Runbooks and playbooks** — develop documented procedures for operations teams to follow so that every response to an event is **repeatable** and based on an understanding of **essential assets and risks** to the business. No improvising — follow the playbook.

**Event vs incident:**

- **Event** = something that happens that is **detectable** (a login, a connection, a file access)
- **Incident** = an event that **violates policy** — defined by the organisation based on its own requirements. Not every event is an incident

## Logging

An **essential** aspect. Without logs, you have no visibility and no evidence.

**NetFlow data** can store **summary information** about connections into and out of the network — who talked to whom, on which port, for how long, how much data. Lighter than full packet capture but enough to spot anomalies.

**The cost of logging:** logs need a place to be **stored and queried** — that takes infrastructure and money. Tools for this: **Elastic Stack (ELK)**, **Splunk**, or a **SIEM**.

A **SIEM** helps **organise, correlate and manage events** — including **generating alerts** when patterns match known threats or anomalous behaviour.

## Response capability

Once an attacker has been identified, there needs to be the ability to **respond**:

- **Isolate systems** — separate a compromised system and its traffic from the rest of the network
- **Choke points** — design the network with control points where traffic can be **blocked, redirected or inspected** during an incident
- **Segmentation** — the network should already be segmented so isolation is a configuration change, not a redesign

---

# Cheatsheet

| Concept | Purpose |
| --- | --- |
| **Defensible architecture** | Monitor and control attackers once inside — not just perimeter defence |
| **Event** | Something detectable that happened |
| **Incident** | An event that violates policy |
| **Runbook / playbook** | Documented, repeatable response procedures |
| **NetFlow** | Summary data of network connections (lighter than full capture) |
| **SIEM** | Organise, correlate, alert on events (Splunk, ELK, etc.) |
| **Isolation** | Separate a compromised system from the network |
| **Choke points** | Network positions where traffic can be controlled during response |

**Key points**

- Not about keeping attackers out — about **detecting and controlling** them inside
- Logging is essential but **expensive** — needs storage, tools, and people to review
- Every response should be **repeatable** (playbooks) and based on **business risk**
- Network must be designed for **isolation** — you can't isolate what isn't segmented