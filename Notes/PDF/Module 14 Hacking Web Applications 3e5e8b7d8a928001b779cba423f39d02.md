# Module 14: Hacking Web Applications

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
⚠️ This is the **largest CEH module (368 pages)** — heavily emphasized on the exam and directly relevant to web pentesting work.
> 

---

## 📋 Table of Contents

1. [Web Application Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-web-application-concepts)
2. [Web Application Threats](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-web-application-threats)
3. [Web Application Hacking Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-web-application-hacking-methodology)
4. [Web API and Webhooks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-web-api-and-webhooks)
5. [Web Application Security Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-web-application-security-techniques)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. Web Application Concepts

### 🏗️ Working of Web Applications

```
User (Login Form) → Internet → Firewall → Web Server → Web Application Server →
DBMS (SELECT * from table where id=X) → Output → Web Server → User's Browser
```

**Steps:**

1. User enters URL/website name in browser → request sent to web server
2. Web server checks file extension: static (.htm/.html) served directly; dynamic (.php/.asp/.cfm) passed to web application server
3. Web application server processes request, accesses database (update/retrieve)
4. Results sent back through web server to the browser

---

### 🏛️ Web Application Architecture — 3 Layers

```
1. Client/Presentation Layer   — physical client devices, browsers, OS
2. Business Logic Layer        — Web-server logic layer (firewall, HTTP parser,
                                  proxy caching, auth handler) + Business logic layer
                                  (.NET, Java, middleware)
3. Database Layer               — data storage
```

**Advantages of web applications:** OS-independent, accessible anytime/anywhere, customizable UI, accessible on any Internet-connected device, dedicated servers with increased workload capacity, multiple server locations increase physical security, uses flexible/scalable core tech (JSP, Servlets, ASP, SQL Server, .NET).

---

### 📊 OWASP Top 10 Application Security Risks — 2021 (CRITICAL — MEMORIZE)

| # | Risk | Example Attacks |
| --- | --- | --- |
| **A01** | **Broken Access Control** | Directory Traversal, Hidden Field Manipulation |
| **A02** | **Cryptographic Failures** | Cookie Snooping, RC4 NOMORE Attack, Same-Site Attack, Pass-the-Cookie Attack |
| **A03** | **Injection** | SQL Injection, Command Injection, LDAP Injection, Cross-Site Scripting (XSS), Buffer Overflow |
| **A04** | **Insecure Design** | Business Logic Bypass Attack, Web-based Timing Attacks, CAPTCHA Attacks |
| **A05** | **Security Misconfiguration** | XML External Entity (XXE) Attack, Directory Traversal, Unvalidated Redirects and Forwards, Hidden Field Manipulation |
| **A06** | **Vulnerable and Outdated Components** | Platform Exploits, Magecart Attack, Buffer Overflow |
| **A07** | **Identification and Authentication Failures** | Cross-Site Request Forgery, Cookie/Session Poisoning, Cookie Snooping |
| **A08** | **Software and Data Integrity Failures** | Insecure Deserialization, Unvalidated Redirects and Forwards, Watering Hole Attack, Denial-of-Service (DoS), Buffer Overflow, Web Service Attacks, Platform Exploits, Magecart Attack |
| **A09** | **Security Logging and Monitoring Failures** | Web Service Attacks |
| **A10** | **Server-Side Request Forgery (SSRF)** | Injecting an SSRF Payload, Cross-Site Port Attack (XSPA), DNS Rebinding Attack, H2C Smuggling Attack |

> **A01 – Broken Access Control:** Improperly enforced restrictions on authenticated user actions — access other user accounts/data, modify access rights.
**A02 – Cryptographic Failures:** Sensitive data (financial, healthcare, PII) not properly protected; weak keys, old algorithms, cleartext transmission.
**A10 – SSRF:** Attacker forces the application to send a crafted request to an unexpected destination, bypassing firewall/VPN protections.
> 

---

### 🎯 SSRF Attack Examples (Real Payloads)

```
# Gaining access to internal pages
https://www.certifiedhacker.com/page?url=http://127.0.0.1/admin
https://www.certifiedhacker.com/page?url=http://127.0.0.1/pgadmin
https://www.certifiedhacker.com/page?url=http://127.0.0.1/phpmyadmin

# Using a URL scheme to access internal files
https://www.certifiedhacker.com/page?url=file://etc/passwd
https://www.certifiedhacker.com/page?url=file:///etc/passwd
https://www.certifiedhacker.com/page?url=file://path/to/file
```

---

## 2. Web Application Threats

### 📊 35 Web Application Attacks (CRITICAL — Numbered List)

```
1.  Directory Traversal              19. Cross-Site Request Forgery
2.  Hidden Field Manipulation        20. Cookie/Session Poisoning
3.  Cookie Snooping                  21. Insecure Deserialization
4.  RC4 NOMORE Attack                22. Watering Hole Attack
5.  Pass-the-Cookie Attack           23. Denial-of-Service (DoS)
6.  Same-Site Attack                 24. Web Service Attacks
7.  SQL Injection                    25. Injecting an SSRF Payload
8.  Command Injection                26. Cross-Site Port Attack (XSPA)
9.  LDAP Injection                   27. DNS Rebinding Attack
10. Cross-Site Scripting (XSS)       28. H2C Smuggling Attack
11. Buffer Overflow                  29. Clickjacking Attack
12. Business Logic Bypass Attack     30. JavaScript Hijacking
13. Web-based Timing Attacks         31. Cross-Site WebSocket Hijacking
14. CAPTCHA Attacks                  32. Obfuscation Application
15. Platform Exploits                33. Network Access Attacks
16. XML External Entity (XXE) Attack 34. DMZ Protocol Attacks
17. Unvalidated Redirects/Forwards   35. MarioNet Attack
18. Magecart Attack
```

---

### 🎯 Directory Traversal

> Allows attackers to access restricted directories (source code, config, system files) and execute commands outside the web server's root directory. Manipulates variables referencing files with **"dot-dot-slash (../)"** sequences.
> 

```
http://www.certifiedhacker.com/process.aspx?page=../../../../some dir/some file
http://www.targetsite.com/../../../sitebackup.zip
```

**Enables attackers to:** Enumerate file/directory contents, access pages requiring auth, gain secret app knowledge, discover credentials in hidden files, locate source code, view sensitive customer data.

---

### 🎯 Same-Site Attack (Related-Domain Attack)

> Targets a subdomain of a trusted organization to redirect users to an attacker-controlled page. Exploits unused/misconfigured subdomains sharing the legitimate site's **top-level domain (TLD)**, creating "dangling records" using extended TLDs (eTLDs).
> 

```
1. User browses for a website (www.certifiedhacker.com)
2. Attacker hijacks the subdomain
3. User redirected to a dangling website
4. Attacker steals user data (both domains belong to certifiedhacker.com)
```

---

### 🎯 Cross-Site Scripting (XSS) Attacks

> Exploits vulnerabilities in dynamically generated web pages, enabling attackers to inject client-side scripts (JavaScript, VBScript, ActiveX, HTML, Flash) into web pages viewed by other users. Occurs when unvalidated input data is included in dynamic content.
> 

**Exploitations:** Malicious script execution, redirect to malicious server, exploit user privileges, hidden IFRAME ads/pop-ups, data manipulation, session hijacking, brute-force password cracking, data theft, intranet probing, keylogging/remote monitoring.

**XSS Example — Stealing Users' Cookies:**

```html
<A HREF=http://juggyboybank.com/registration.cgi?clientprofile=<SCRIPT>malicious code</SCRIPT>>Click here</A>
```

1. Attacker constructs malicious link, emails URL to user
2. User's browser requests the page from legitimate server
3. Page with malicious script returned
4. Script runs, sends unauthorized request/steals cookies to attacker's server

> **Note:** Check the CEH Tools, Module 14: Hacking Web Applications for the XSS cheat sheet.
> 

---

### 🎯 CRLF Injection

> Attackers inject **carriage return (\r)** and **line feed (\n)** characters into user input, tricking the server/application into believing an object is terminated and a new one initiated. Can lead to **HTTP request smuggling** and **HTTP response splitting**.
> 

```
Original log: 10.10.10.10 - 09:25 - /index.php?page=about
Injected:      /index.php?page=about&%0d%0a127.0.0.1 - 09:25- /index.php?page=about&restrictedaction=edit
```

> `%0d` and `%0a` are CR and LF encoded characters — used to hide malicious activities in log entries.
> 

---

### 📊 Web-based Timing Attacks — 3 Types

| Type | Description |
| --- | --- |
| **Direct Timing Attack** | Measures approximate server time to process a POST request to deduce username existence (character-by-character password examination) |
| **Cross-site Timing Attack** | Sends crafted request packets to website via JavaScript; analyzes time consumed by user to download requested file |
| **Browser-based Timing Attack** | Exploits side-channel leaks of a browser to estimate time taken to process resources; enables video parsing/cache storage timing attacks |

---

### 🎯 Insecure Deserialization

> **Deserialization** = reverse of serialization; object data recreated from linear serialized data. **Insecure deserialization** = attacker injects malicious code into serialized linear formatted data forwarded to victim.
> 

```
<Employee><Name>Rinni</Name><Age>26</Age><City>Nevada</City><EmpID>2201</EmpID>MALICIOUS PROCEDURE</Employee>
```

> Injected code remains undetected and executes along with the deserialization process.
> 

---

### 🎯 Watering Hole Attack

> Attacker identifies websites frequently visited by a target, tests for vulnerabilities, injects malicious script/code, and waits for the victim to access the infected application (like a predator waiting at a watering hole).
> 

---

### 🎯 Clickjacking Attack (UI Redress Attack)

**3 Techniques:**

| Technique | Description |
| --- | --- |
| **Hidden Overlay** | Attacker creates a 1×1 pixel iframe with malicious content secretly under the mouse cursor |
| **Click Event Dropping** | Hides malicious page behind legitimate page; sets CSS `pointer-events` to none, causing clicks to "drop through" to the malicious page |
| **Rapid Content Replacement** | Targeted controls covered by opaque overlays removed only momentarily to register a click; requires precise timing prediction |

```
1. Attacker sends malicious website link via email
2. Victim opens link in browser
3. Victim's browser opens the target website (overlaid)
4. Victim clicks legitimate UI element and gets clickjacked
```

---

### 🎯 Other Key Attack Definitions (Compact)

| Attack | Definition |
| --- | --- |
| **CAPTCHA Attacks** | Exploits challenge-response tests designed to distinguish humans from computers — despite being designed unbreakable, various attack techniques exist |
| **Platform Exploits** | Exploits vulnerabilities in web app platforms (BEA WebLogic, Cold Fusion) |
| **DoS** | Reduces/restricts/prevents access to system resources for legitimate users |
| **H2C Smuggling Attack** | Exploits vulnerabilities in handling HTTP/2 connections when a web app supports both HTTP/1.1 and HTTP/2. "H2C" = HTTP/2 over TCP (no TLS); attacker crafts requests misleading security controls/frontend-backend communication — leads to cache poisoning, bypassing security, unauthorized access |
| **JavaScript Hijacking (JSON Hijacking)** | Captures sensitive info from systems using JSON as data carrier; exploits flaws in browser's same-origin policy |
| **Cross-Site WebSocket Hijacking (CSWH)** | Attacker establishes WebSocket connection with a vulnerable app using victim's identity — possible when WebSocket handshake uses HTTP cookies without CSRF tokens |
| **Obfuscation Application** | Attackers hide attacks from IDS/IPS signature detection |
| **Redirection Attacks** | Attacker develops code/links resembling legitimate sites; URL redirects user to malicious site |
| **Frame Injection** | Injects code through frames when scripts don't validate input |
| **Session Fixation** | Attacker authenticates with a known session ID, then uses it to hijack the user-validated session |
| **ActiveX Attacks** | Lures victims via email/link to exploit remote execution code loopholes |

---

## 3. Web Application Hacking Methodology

### 📊 12-Phase Web Application Hacking Methodology (CRITICAL)

```
1.  Footprint web infrastructure
2.  Analyze web applications
3.  Bypass client-side controls
4.  Attack authentication mechanisms
5.  Attack authorization schemes
6.  Attack access controls
7.  Attack session management mechanisms
8.  Perform injection attacks
9.  Attack application logic flaws
10. Attack shared environments
11. Attack database connectivity
12. Attack web application clients
    (+ Attack web services)
```

---

### 1️⃣ Footprint Web Infrastructure

**Server Discovery:**

- **Whois Lookup** — IP address/DNS names of web server (tools: Netcraft, Whois Lookup, Batch IP Converter, Whois Domain Lookup)
- **DNS Interrogation** — locations/types of servers (tools: DNSRecon, DNS Records, Domain Dossier, DNSdumpster.com)
- **Banner Grabbing** — server response header field to identify make/model/version (tools: Telnet, Netcat, ID Serve, Netcraft)

**Port and Service Discovery:**

1. Scan target to identify common ports web servers use for different services
2. Initiate port scanning to connect to TCP/UDP ports to discover services
3. Identified services act as **attack paths** for web application hacking

Tools: NetScanTools Pro, Advanced Port Scanner, Open Port Scanner, Port Scanner

---

### 2️⃣ Analyze Web Applications

> Determine vulnerable areas, reduce "attack surface." Analyzing target reveals:
> 
- **Software used and its version** — off-the-shelf software fingerprinting
- **Operating system used**
- **Sub-directories and parameters** — via URL browsing
- **Filename, path, database field name, or query** — check for SQL injection opportunities
- **Scripting platform** — via file extensions (.php, .asp, .jsp)

**Tools:** Wappalyzer, BuiltWith (technology stack detection)

**Website Mirroring:** Copy a website and its content to another server for offline browsing — reveals detailed site structure. Tool: `httrack`

```bash
httrack https://certifiedhacker.com -O ~/Desktop/certifiedhacker_mirror
```

**Identify Entry Points for User Input:** Attackers identify how the web app accepts/handles user input to launch injection attacks.

**Directory/File Enumeration:** Tool: Gobuster

```bash
gobuster dir -u https://www.certifiedhacker.com -w common.txt
gobuster -u <target URL> -w common.txt -s 200    # filter by status code
```

**WAF Detection:** Tool: WAFW00F

```bash
wafw00f certifiedhacker.com
```

Other tools: WhatWaf, Nmap, Web Application Firewall Detector, SHIELDFY, Advanced WAF detection

---

### 3️⃣ Bypass Client-Side Controls

- **Analyzing HTML/Decompiling Browser Extensions/Flash Objects** to modify data before submission
- **Intercepting proxies** (e.g., Burp Suite) to capture/modify web page component requests
- **Attacking Google Chrome Browser Extensions** — exploit Chrome Sync to add fake/malicious extensions gathering autofill info, bookmarks, search history, passwords, synced data

---

### 4️⃣ Attack Authentication Mechanism

> Exploit design and implementation flaws (failure to check password strength, insecure credential transmission) to bypass authentication.
> 

**5 Categories (CRITICAL TABLE):**

| Category | Techniques |
| --- | --- |
| **Username Enumeration** | Verbose failure messages, Predictable usernames |
| **Password Attacks** | Password functionality exploits, Password guessing, Brute-force attack, Dictionary attack, Attack password reset mechanism |
| **Session Attacks** | Session prediction, Session brute-forcing, Session poisoning |
| **Cookie Exploitation** | Cookie poisoning, Cookie sniffing, Cookie replay |
| **Bypass Authentication** | Bypass SAML-based SSO, Bypass rate limit, Bypass multi-factor authentication |

### 📊 14 Design and Implementation Flaws in Authentication Mechanism

```
1.  Bad Passwords                     8.  User Impersonation
2.  Brute-Forcible Login              9.  Improper Validation of Credentials
3.  Verbose Failure Messages          10. Predictable Usernames and Passwords
4.  Insecure Transmission of Credentials  11. Insecure Distribution of Credentials
5.  Password Reset Mechanism          12. Fail-Open Login Mechanism
6.  Forgotten Password Mechanism      13. Flaws in Multistage Login Functionality
7.  "Remember Me" Functionality       14. Insecure Storage of Credentials
```

**Password Reset Poisoning Attack:**

```
1. Attacker obtains email address used by target (social engineering/OSINT)
2. Attacker sends password reset request with altered Host header:
   POST https://certifiedhacker.com/reset.php HTTP/1.1
   Host: badhost.com
   → resultant URL: https://badhost.com/reset-password.php?token=87654321-...
3. Attacker waits for victim to receive the modified email
4. Once victim clicks the link, attacker extracts the password reset token
```

**'Remember Me' Exploit:** Implemented via persistent cookie (`RememberUser=jason`) or session identifier (`RememberUser=ABY112010`). Attacker enumerates/predicts to bypass authentication.

---

### 5️⃣ Attack Authorization Schemes

> First access with low privileges, then escalate to protected resources. Manipulate HTTP requests via input field modification.
> 

**6 Techniques (CRITICAL):**

```
1. Uniform Resource Identifier    4. Parameter Tampering
2. POST Data                      5. HTTP Headers
3. Query String and Cookies       6. Hidden Tags
```

**HTTP Request Tampering example:**

```
GET http://certifiedhacker:8180/Applications/Download?ItemID=201 HTTP/1.1
...
Referer: http://certifiedhacker:8180/Applications/Download?Admin=False
```

> ItemID=201 inaccessible because Admin=False — change to `Admin=true` to access protected items.
> 

---

### 6️⃣ Attack Access Controls

**5 Access Controls Attack Methods:**

```
1. Attack with different user accounts   — test broken access control across user contexts
2. Attack Multistage Processes           — capture/test each request in multi-step processes;
                                            switch session tokens between privilege levels
3. Attack Static Resources               — request protected URLs directly
4. Attack Direct Access to Methods       — exploit server-side API access weaknesses
5. Attack Restrictions on HTTP Methods   — test GET/POST/PUT/DELETE/TRACE/OPTIONS modifications
```

---

### 7️⃣ Attack Session Management Mechanism

**Session token exploitation techniques:**

- **MITM Attack** — intercepts communication, splits connection into two
- **Session Hijacking** — steals session ID from trusted website
- **Session Replay** — obtains and reuses session ID

**Attacking Session Token Generation — Weak Encoding Example:**

```
Original: user=jason;app=admin;date=08/01/2020 (hex encoded)
https://www.certifiedhacker.com/checkout?SessionToken=%75%73%65%72...
```

> Attacker predicts another session token by just changing the date value.
> 

**Session Token Prediction Process:**

1. Obtain valid session tokens via sniffing or legitimate login; analyze for encoding pattern (hex, Base64)
2. Reverse engineer meaning from sample tokens; guess tokens recently issued to other users
3. Make large number of requests with predicted tokens to a session-dependent page

---

### 8️⃣ Perform Injection Attacks

> Covers SQL Injection (complete coverage in Module 15), Command Injection, LDAP Injection, XSS, and related techniques discussed in Section 2 above.
> 

---

### 9️⃣ Attack Application Logic Flaws

> Most application flaws occur due to **negligence and false assumptions** of developers. Logic flaws lack common signatures — harder to identify than SQL Injection/XSS. Automated scanners can't reliably detect them.
> 

**Retail Web App Logic Flaw Exploitation Scenario:**

```
Normal:  Select Product → Finalize Order → Proceed to pay → Delivery Details
Attack:  Select Product → Finalize Order → [SKIP "Proceed to pay"] → Delivery Details
```

> Attacker uses Burp Suite to manipulate requests, skipping payment stage entirely — this is called **forced browsing**.
> 

---

### 🔟 Attack Shared Environments

> Third-party service providers host multiple clients' web apps on shared infrastructure.
> 

**Attacks on the access mechanism:** Check remote access mechanism for unpatched vulnerabilities/config errors; check whether access privileges are properly separated between clients (e.g., shell access instead of file access).

**Attacks between applications:** Vulnerabilities in one hosted app (e.g., SQL injection) may allow attackers to compromise OTHER hosted apps sharing the environment.

---

### 1️⃣1️⃣ Attack Database Connectivity

**3 Types of Data Connectivity Attacks:**

```
1. Connection String Injection
2. Connection String Parameter Pollution (CSPP) Attacks
3. Connection Pool DoS
```

**Connection String Injection example:**

```
Before: "Data Source=Server,Port; Network Library=DBMSSOCN; Initial Catalog=DataBase; User ID=Username; Password=pwd;"
After:  "Data Source=Server,Port; Network Library=DBMSSOCN; Initial Catalog=DataBase; User ID=Username; Password=pwd; Encryption=off"
```

> Inject parameters by appending them with the **semicolon (;)** character; occurs with dynamic string concatenation for connection strings.
> 

**Hijacking Web Credentials:**

```
Inject User_Value: ; Data Source=Target_Server
Password_Value: ; Integrated Security = true
```

> Overwrites "integratedsecurity" parameter to "true," allowing connection with the web app's system account instead of user credentials.
> 

**Connection Pool DoS:** Construct large malicious SQL query, run multiple queries simultaneously to consume all connections in the pool (e.g., ASP.NET default max = 100 connections, 30s timeout — run 100+ queries with 30+ second execution time to exhaust the pool).

---

### 1️⃣2️⃣ Attack Web Application Clients

```
Redirection Attacks | Frame Injection | Session Fixation | ActiveX Attacks
```

---

### 🌐 Attack Web Services (SOAP/REST)

| Attack | Description |
| --- | --- |
| **SOAP Injection** | Inject malicious query strings in user input fields to bypass web service authentication and access backend databases (works similarly to SQL Injection) |
| **SOAPAction Spoofing** | SOAPAction HTTP header informs the receiving web service about the SOAP body operation without XML parsing; attackers manipulate this header. Tool: WS-Attacker |
| **WS-Address Spoofing** | Attacker sends SOAP message with fake WS-Address info; `<ReplyTo>` header set to attacker-controlled endpoint, redirecting unnecessary traffic there |

---

## 4. Web API and Webhooks

### 🔑 Key API Concepts

**RESTful API characteristics:**

```
Stateless | Cacheable | Client-server Environment |
Uniform Interface | Layered System | Code on Demand (optional)
```

**XML-RPC:** Communication protocol using specific XML format to transfer data — simpler than SOAP, less bandwidth.
**JSON-RPC:** Same as XML-RPC but uses JSON format instead of XML.

---

### 📊 OWASP Top 10 API Security Risks (CRITICAL TABLE)

| API | Risk | Description |
| --- | --- | --- |
| **API1** | Broken Object-Level Authorization | Endpoints managing object identifiers expose broad attack surface; manipulating object ID in request → unauthorized data disclosure/loss/manipulation |
| **API2** | Broken Authentication | Flawed auth mechanisms let attackers compromise tokens or assume other users' identities |
| **API3** | Broken Object Property Level Authorization | Object properties exposed without regard to sensitivity; unauthorized access to private/sensitive properties |
| **API4** | Unrestricted Resource Consumption | Automated tools send multiple concurrent requests causing DoS with high traffic loads; targets APIs lacking limits |
| **API5** | Broken Function Level Authorization | Complex access control policies across hierarchies/groups/roles cause authorization errors between admin and regular functions |
| **API6** | Unrestricted Access to Sensitive Business Flows | Vulnerable APIs expose business flows (ticket purchasing, posting comments) without excessive-use protection |
| **API7** | Server-Side Request Forgery | SSRF flaw allows forcing an app to send crafted requests to unexpected destination, bypassing firewall/VPN |
| **API8** | Security Misconfiguration | Unpatched flaws, common endpoints, insecure default configurations |
| **API9** | Improper Inventory Management | Unauthorized access through old API versions/endpoints running unpatched with weaker security |
| **API10** | Unsafe Consumption of APIs | Developers trust third-party API data more than user input, adopting weaker security standards |

---

### 📊 8-Point API Vulnerabilities Table

```
1. Improper Use of CORS      — misconfigured Access-Control-Allow-Origin causes hotlinking
2. Code Injections           — unsanitized input allows SQLi/XSS on API input fields
3. RBAC Privilege Escalation — role-based access control changes without proper attention
4. No ABAC Validation        — allows unauthorized viewing/updating/deleting of API objects
5. Business Logic Flaws      — exploits legitimate API workflows for malicious purposes
```

(Numbered 4-8 in the original table alongside earlier API concepts)

---

### 🪝 Webhooks

> User-defined HTTP callback/push APIs raised based on triggered events (e.g., comment received on a post). Also called **"Reverse APIs"** — they provide what's required for API specification rather than developers building calls to fetch it.
> 

**Operation of Webhooks:**

```
System-1 (Event 1/2/3) → Web/HTTP (POST Request) → System-2
```

> Webhooks enrolled with domain registration; generated path contains code that auto-executes on event occurrence.
> 

### 📊 Webhooks vs APIs

| Webhooks | APIs |
| --- | --- |
| Automated messages FROM websites TO server | Used for server-to-website communication |
| Get reports/notifications via HTTP POST only on new updates | Make calls irrespective of data updates |
| Update apps/services with real-time info | Need additional implementation for real-time |
| Less control over data flow | Easy control over data flow |

---

### 🔓 Hacking APIs — Key Techniques

**Exploiting Insecure Configurations:**

| Type | Description |
| --- | --- |
| **Insecure SSL Configuration** | SSL config vulnerabilities allow MITM attacks; sniff traffic, manipulate client-side certificate |
| **Insecure Direct Object References (IDOR)** | Direct object references used as API call arguments without access rights checks; identifiable via API metadata |
| **Insecure Session/Authentication Handling** | Reused session tokens, sequential tokens, long timeout, unencrypted tokens, tokens in URL — allows hijacking client session |

**Login/Credential Stuffing Attacks:**

> Exploit password reuse across platforms. Do NOT guess/brute-force — instead automate previously identified credential pairs using tools like **Sentry MBA** and **PhantomJS** to break into accounts.
> 

**API DDoS Attacks:** Saturate API with massive traffic from botnet to delay service; may bypass rate limits, load balancers, security implementations — not always volumetric, may exploit specific API vulnerabilities.

**REST API Vulnerability Scanning Tools:** Astra, Fuzzapi, w3af, AppSpider, Vooki, OWASP ZAP

---

## 5. Web Application Security Techniques

### 🧪 Web Application Fuzz Testing (Fuzzing)

> Black-box testing method — quality checking/assurance technique to identify coding errors and security loopholes. Fuzz testing tools ("Fuzzers") generate huge amounts of random data ("fuzz") against target app.
> 

**Steps of Fuzz Testing:**

```
Identify target system → Identify inputs → Generate fuzzed data →
Execute test using fuzz data → Monitor system behavior → Log defects
```

**Fuzz Testing Strategies:** Mutation-Based (mutates valid sample data repeatedly)

**AI-Powered Fuzz Testing:** Uses ML to automate crafting diverse, complex inputs (vs random data); recognizes patterns, predicts effective inputs, continuously learns from real-time feedback.

**Tool: wfuzz**

```bash
wfuzz -c -z file,common.txt --hc 404 http://www.moviescope.com/FUZZ
```

---

### 🔍 Source Code Review / SAST & DAST

**SAST (Static Application Security Testing):** Analyzes source code without execution.
**DAST (Dynamic Application Security Testing):** Actively interacts with running applications, simulates attacks.

**AI-powered SAST:** e.g., Code Genie AI — automated vulnerability scanning, advanced pattern recognition, prioritized risk assessment, actionable recommendations.
**AI-powered DAST:** e.g., ZeroThreat.ai — intelligent crawling, threat intelligence integration, minimizes false positives, CI/CD integration.

---

### 🔐 Encoding Schemes (CRITICAL TABLE)

| Scheme | Description | Example |
| --- | --- | --- |
| **URL Encoding** | Converts URL into valid ASCII format; replaces unusual chars with "%" + hex ASCII code | `%3d` = "=", `%0a` = newline, `%20` = space |
| **HTML Encoding** | Represents unusual characters safely within HTML documents | `&amp;` = &, `&lt;` = <, `&gt;` = > |
| **Unicode Encoding** | 16-bit encoding replaces unusual Unicode chars with "%u" + code point | `%u2215` = / |
| **UTF-8** | Variable-length encoding; each byte in hex preceded by % | `%c2%a9` = ©, `%e2%89%a0` |
| **Base64 Encoding** | Represents binary data using printable ASCII characters; used for email attachments/user credentials | "cake" binary → Base64: `01011001 00110010...` |
| **Hex Encoding** | Uses hex value of every character | Hello → `48 65 6C 6C 6F` |

---

### 🛡️ Application Whitelisting and Blacklisting Tools

```
ManageEngine Application Control Plus | BitDefender | Cisco Umbrella |
Symantec Endpoint Application Control | BrowseControl | Sucuri WAF
```

---

### 🛡️ How to Defend Against Injection Attacks (Countermeasures Table)

| Attack | Countermeasures |
| --- | --- |
| **HTML Injection** | Validate user inputs to remove HTML-syntax substrings; check for unwanted `<script></script>`, `<html></html>`; encode/examine/validate user outputs; enable HttpOnly flag on cookies |
| **CRLF Injection** | Encode CRLF special characters; avoid using user input in response headers; check/remove newline strings before HTTP header; encrypt data passed to HTTP headers; configure XSSUrlFilter |
| **XSS Attacks** | Validate all headers/cookies/query strings/form fields/hidden fields against rigorous spec; use testing tools during design phase; use a WAF to block malicious script execution; convert non-alphanumeric characters to HTML entities; encode input/output; filter metacharacters |
| **SQL Injection** | Limit user input length; custom error messages; monitor DB traffic with IDS+WAF; disable `xp_cmdshell`; isolate DB/web servers; use POST method + low-privileged DB account; use `isNumeric()` for typesafety; use prepared statements/parameterized queries/stored procedures; avoid dynamic SQL |

**Additional XSS defense:**

- Deploy PKI for authentication
- Implement Content Security Policy (CSP)
- Escape untrusted HTTP request data built on context (resolves Reflected/Stored XSS)
- Employ context-sensitive encoding for DOM-XSS defense
- Use "positive security policy" (specify what's allowed, not what's blocked)

---

### 🛡️ XXE Prevention (Additional)

- Parse documents with a securely configured parser
- Configure XML processor to use local static DTD; disable declared DTD in documents
- Implement whitelisting, input validation, sanitization
- Update/patch latest XML processors and libraries
- Validate XML/XLS file uploads using XSD validation
- Employ API security gateways, IAST tools, WAFs
- Use application server instrumentation (ASI) to monitor execution flow
- Limit size/complexity of XML documents; set limits on entities/nesting depth

---

### 🛡️ Best Practices for API Security (14-Point Checklist — CRITICAL)

1. Use HTTPS via SSL/TLS certificates
2. Use server-generated tokens embedded in HTML as hidden fields for request validation
3. Sanitize data to eliminate malicious scripts; validate user input
4. Use an optimized firewall; revoke unused/unnecessary files and permissive rules
5. Use **IP whitelisting** to create a trusted IP list
6. Use **rate-limiting** to limit API calls per client per time frame
7. Implement a **pagination technique** dividing single response into fragments (prevents oversized payloads)
8. Use **parameterized statements** in SQL queries
9. Conduct regular security assessments using automated tools
10. Use tokens to establish **trusted identities** and control access
11. Use **signatures** so only authorized users can decrypt/modify data
12. Employ **packet sniffers** to track info disclosure events and detect insecure API calls
13. Use techniques such as **quotas and throttling** to control/track API usage
14. Implement **API gateways** to authenticate traffic and control/analyze API usage
    - Implement **MFA** and use authentication protocols such as **AppToken, OAuth2, OpenID Connect**

---

### 🛡️ Webhook Security Best Practices

```
Store tokens against store_hash (not user data) | Verify clients via mutual TLS |
Don't send confidential info via webhooks (use authorized APIs instead) |
Use HMAC-based signatures for message verification | Use unique event ID per payload |
Log sent webhooks for debugging | Use API keys to authenticate requests |
Limit webhook payload size (prevent DoS) | Use a webhook proxy service as extra security layer |
Verify incoming requests come from expected sources (IP whitelist/DNS resolution)
```

---

## 6. Quick Exam Cheat Sheet

### 📊 OWASP Top 10 — 2021 (Web Apps)

```
A01 Broken Access Control        A06 Vulnerable/Outdated Components
A02 Cryptographic Failures        A07 Identification/Auth Failures
A03 Injection                     A08 Software/Data Integrity Failures
A04 Insecure Design                A09 Security Logging/Monitoring Failures
A05 Security Misconfiguration      A10 Server-Side Request Forgery (SSRF)
```

---

### 📊 OWASP Top 10 API Security Risks

```
API1 Broken Object-Level Authorization    API6 Unrestricted Access to Sensitive Business Flows
API2 Broken Authentication                 API7 Server-Side Request Forgery
API3 Broken Object Property Level Auth     API8 Security Misconfiguration
API4 Unrestricted Resource Consumption     API9 Improper Inventory Management
API5 Broken Function Level Authorization   API10 Unsafe Consumption of APIs
```

---

### 🎯 12-Phase Web App Hacking Methodology

```
1. Footprint Web Infrastructure    7. Attack Session Management
2. Analyze Web Applications        8. Perform Injection Attacks
3. Bypass Client-Side Controls     9. Attack Application Logic Flaws
4. Attack Authentication           10. Attack Shared Environments
5. Attack Authorization Schemes    11. Attack Database Connectivity
6. Attack Access Controls          12. Attack Web App Clients (+ Web Services)
```

---

### 🔐 Encoding Schemes Quick Reference

```
URL:     %3d = "="   %0a = newline   %20 = space
HTML:    &amp; = &   &lt; = <        &gt; = >
Base64:  represents binary as printable ASCII
Hex:     H=48 e=65 l=6C l=6C o=6F
```

---

### 🔥 Common Exam Scenarios

**Q: What OWASP risk covers SSRF-related attacks?**
→ **A10 – Server-Side Request Forgery**

**Q: What OWASP risk covers XSS, SQL Injection, and Command Injection?**
→ **A03 – Injection**

**Q: What attack targets a misconfigured/unused subdomain sharing a trusted site's TLD?**
→ **Same-Site Attack** (related-domain attack)

**Q: What technique injects CR/LF characters to manipulate logs or split HTTP responses?**
→ **CRLF Injection**

**Q: What are the 3 types of web-based timing attacks?**
→ **Direct Timing Attack, Cross-site Timing Attack, Browser-based Timing Attack**

**Q: What clickjacking technique uses a 1×1 pixel iframe hidden under the cursor?**
→ **Hidden Overlay**

**Q: What attack exploits HTTP/2-over-TCP-without-TLS request smuggling?**
→ **H2C Smuggling Attack**

**Q: What's the difference between JavaScript Hijacking and Cross-Site WebSocket Hijacking?**
→ JS Hijacking exploits same-origin policy flaws to capture JSON data; CSWH establishes a WebSocket connection using the victim's identity when handshake lacks CSRF tokens

**Q: What are the 14 design/implementation flaws in authentication mechanisms?**
→ Bad Passwords, Brute-Forcible Login, Verbose Failure Messages, Insecure Transmission of Credentials, Password Reset Mechanism, Forgotten Password Mechanism, "Remember Me" Functionality, User Impersonation, Improper Validation of Credentials, Predictable Usernames/Passwords, Insecure Distribution of Credentials, Fail-Open Login Mechanism, Flaws in Multistage Login, Insecure Storage of Credentials

**Q: What are the 3 types of data connectivity attacks?**
→ **Connection String Injection, Connection String Parameter Pollution (CSPP), Connection Pool DoS**

**Q: What character is used to append parameters in a Connection String Injection attack?**
→ **Semicolon (;)**

**Q: What's the difference between webhooks and APIs?**
→ Webhooks = automated push messages from website to server on events (HTTP POST); APIs = used for server-to-website pull communication with easier data-flow control

**Q: What tools automate credential stuffing attacks?**
→ **Sentry MBA and PhantomJS**

**Q: What tool detects Web Application Firewalls (WAF)?**
→ **WAFW00F**

**Q: What is "forced browsing" in the context of application logic flaws?**
→ Skipping stages in a multistage process (e.g., skipping payment) by manipulating requests directly

**Q: What are the 6 techniques for attacking authorization schemes?**
→ **URI, POST Data, Query String and Cookies, Parameter Tampering, HTTP Headers, Hidden Tags**

**Q: What tool automates website mirroring for phishing/reconnaissance?**
→ **httrack**

**Q: What does IDOR stand for and what does it exploit?**
→ **Insecure Direct Object References** — direct object references used as API arguments without proper access rights checks

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 14*