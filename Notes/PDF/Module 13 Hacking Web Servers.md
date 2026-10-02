# Module 13: Hacking Web Servers

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Web Server Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-web-server-concepts)
2. [Web Server Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-web-server-attacks)
3. [Web Server Attack Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-web-server-attack-methodology)
4. [Web Server Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-web-server-attack-countermeasures)
5. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-quick-exam-cheat-sheet)

---

## 1. Web Server Concepts

### 🏗️ Typical Client-Server Communication in Web Server Operation

```
Web Client ←HTTP Request/Response→ Web Server ←Static Data Request/Response→ Static Data Store
                                         ↓ ↑
                                  Servlet Request/Response
                                         ↓ ↑
                                  Application Server (Web Container + Other Services)
                                         ↓ ↑
                                  Application Data Store
```

### 🧩 Components of a Web Server

| Component | Description |
| --- | --- |
| **Document Root** | Root file directory storing critical HTML files for web pages of a domain. E.g., if document root = "certroot" in `/admin/web`, then `/admin/web/certroot` is the document directory address |
| **Server Root** | Top-level directory in the directory tree — stores server config files, log files, executable files. Sub-directories: **conf** (config files), **logs** (access/error logs), **cgi-bin** (CGI scripts/server-side executables) |

---

### 📊 7-Stack Levels of Organizational Security (CRITICAL)

```
Stack 7: Custom Web Applications      → Business Logic Flaws
Stack 6: Third-party Components       → Open Source/Commercial
Stack 5: Web Server                   → Apache/Microsoft IIS
Stack 4: Database                     → Oracle/MySQL/MS SQL
Stack 3: Operating System             → Windows/Linux/macOS
Stack 2: Network                      → Router/Switch
Stack 1: Security                     → IPS/IDS
```

> Organizations can defend network-level/OS-level attacks with firewalls/IDS/IPS, forcing attackers to focus on **web-server and web-application-level attacks** — since a web server hosting apps is accessible from anywhere on the Internet.
> 

---

### 🎯 Common Goals Behind Web Server Hacking

```
Stealing credit-card details/credentials via phishing
Integrating server into a botnet for DoS/DDoS attacks
Compromising a database
Obtaining closed-source applications
Hiding and redirecting traffic
Escalating privileges
```

---

### ⚠️ Why Are Web Servers Compromised? (3 Perspectives)

| Perspective | Concern |
| --- | --- |
| **Webmaster's** | Web server can expose LAN/corporate intranet to Internet threats (viruses, Trojans, attackers). CGI scripts may contain bugs = potential security holes |
| **Network administrator's** | Poorly configured web server causes potential holes in LAN security; balancing controlled access vs usability |
| **End user's** | Doesn't perceive immediate threat (browsing feels safe/anonymous), but active content (JavaScript, WebAssembly) can introduce malware/ransomware risks bypassing firewalls |

**Common oversights compromising a web server:**

```
Improper file/directory permissions | Default settings installed |
Unnecessary services enabled | Security vs ease-of-use conflicts |
Lack of security policy/procedures/maintenance | Improper authentication with external systems |
Default accounts with default/no passwords | Unnecessary default/backup/sample files |
Misconfigurations (web server/OS/network) | Bugs in server software/OS/web apps |
Misconfigured SSL certificates/encryption | Admin/debugging functions enabled/accessible |
Self-signed/default certificates | Not using dedicated server for web services |
Excessive privileges / no least privilege
```

---

### 🛠️ NGINX Architecture

```
Clients → Worker Processes ←→ Master Process
              ↓  (HTTP, FastCGI, Memcache)
         Backend: Web Server | Application Server | Memcache Server
              ↑
         Proxy Cache ←→ Cache Loader / Cache Manager
```

- **Master Process:** Reads/validates config files, creates/binds/closes sockets, manages worker processes
- **Worker Processes:** Handle client requests (single-threaded, non-blocking I/O, 1000+ connections each)
- **Proxy Cache:** Stores copies of requested content to reduce backend load
- **Cache Loader/Manager:** Load cache metadata at startup / periodically remove expired entries

---

## 2. Web Server Attacks

> **Attack types covered:** DoS/DDoS, DNS server hijacking, DNS amplification, directory traversal, MITM/sniffing, phishing, website defacement, web server misconfiguration, HTTP response splitting, web cache poisoning, SSH brute force, web server password cracking.
> 

---

### 🎯 DNS Server Hijacking

> Attacker **compromises a DNS server** and changes its DNS mapping settings so requests to the target web server redirect to the attacker's own malicious server.
> 

```
1. Attacker compromises DNS server, changes DNS settings
2. User sends request to (compromised) DNS server
3. DNS server checks DNS mapping for requested domain
4. Request redirected to Fake Site (instead of Legitimate Site)
```

---

### 🎯 DNS Amplification Attack

**Recursive DNS Query (8-step process):**

```
1. User sends DNS query to primary DNS server
2-7. If mapping not found, forwarded to root server → .com namespace →
     primary DNS server of target domain (recursively)
8. Primary DNS server found, cache generated in user's primary DNS server
```

> Attackers exploit recursive DNS queries to perform a DNS amplification attack, resulting in **DDoS attacks** on the victim's DNS server — attacker instructs compromised hosts (bots) to make DNS queries with the VICTIM's spoofed source IP, causing amplified responses to flood the victim.
> 

---

### 🎯 Directory Traversal Attacks

> Attackers use the **dot-dot-slash (../) sequence** to access restricted directories outside the web server's root directory by manipulating a URL. Exploits vulnerability in web app code or poorly patched/configured web server software.
> 

```
http://server.com/scripts/..%5c../Windows/System32/cmd.exe?/c+dir+c:\
```

> Attacker uses trial-and-error method to navigate outside root directory. Web server vulnerable if it accepts browser input without proper validation.
> 

---

### 🎯 Web Server Misconfiguration

> Configuration weaknesses in web infrastructure exploited to launch directory traversal, server intrusion, data theft.
> 

**Common misconfigurations:**

```
Verbose debug/error messages | Anonymous/default users/passwords |
Sample configuration/script files | Remote administration functions |
Unnecessary services enabled | Misconfigured/default SSL certificates
```

**Examples:**

- Apache `httpd.conf`: `<Location "/server-status"> SetHandler server-status Require host example.com </Location>` — allows anyone to view server status page
- Nginx `nginx.conf`: unsafe variable usage — `location / { set $variable $arg_user_input; proxy_pass http://backend/$variable; }`
- IIS `web.config`: `<system.webServer><directoryBrowse enabled="true"/></system.webServer>` — enables directory browsing, exposing sensitive files

---

### 🎯 HTTP Response Splitting Attack

> Attacker sends a response-splitting request; the server splits its response into TWO — the FIRST goes to the attacker, the SECOND to the victim.
> 

```
1. Attacker sends response-splitting request
2. Attacker receives first response
3. Victim requests service (e.g., .../account?id=214)
4. Victim receives second response (from attacker's earlier request)
5. Attacker requests index.html
6. Attacker gets response of victim's request
```

> Server code vulnerability example: `String author = request.getParameter(AUTHOR_PARAM); Cookie webcomic = new Cookie("author", author);` — unsanitized input allows CRLF injection to split the HTTP response.
> 

---

### 🎯 Web Cache Poisoning Attack

> Attacker forces the web server's cache to **flush its actual cache content** and sends a specially crafted request, which gets stored in cache instead.
> 

```
1. Attacker sends request to remove page from cache
2. Normal response after clearing cache
3. Attacker sends malicious request generating two responses
4. Attacker gets first server response
5. Attacker requests page again to generate cache entry
6. Second response (points to attacker's page) generated
7. Attacker gets second response — Server Cache now poisoned
   (points www.certifiedhacker.com → Attacker's page)
```

---

## 3. Web Server Attack Methodology

### 📊 6-Stage Web Server Attack Methodology (CRITICAL)

```
1. Information Gathering
2. Web Server Footprinting
3. Website Mirroring
4. Vulnerability Scanning
5. Session Hijacking
6. Web Server Passwords Hacking
```

---

### 1️⃣ Information Gathering

> Collect as much info as possible about the target server using tools/techniques to assess security posture. Sources: Internet, newsgroups, bulletin boards. Tools: **who.is, Whois Lookup, Domain Dossier, Subdomain Finder** extract domain name, IP address, autonomous system number.
> 

---

### 2️⃣ Web Server Footprinting

> Gather info about the security aspects of a web server via tools/techniques — remote access capabilities, ports, services, other security aspects.
> 

**robots.txt analysis:** Reveals disallowed paths/bots (e.g., `User-agent: Googlebot Disallow: /`) — can reveal directory structure attackers shouldn't normally see.

**AI-assisted footprinting:** Attackers use ChatGPT with prompts like *"Perform webserver footprinting on target IP X with netcat"* to auto-generate commands:

```bash
nc -v 10.10.1.22 80 <<EOF
HEAD / HTTP/1.1
Host: 10.10.1.22
EOF
```

---

### 3️⃣ Website Mirroring

> Method of copying a website and its content onto another server for offline browsing. Allows attacker to view detailed website structure.
> 

---

### 4️⃣ Vulnerability Scanning

> Method of finding vulnerabilities/misconfigurations of a web server using automated vulnerability scanners.
> 

---

### 5️⃣ Session Hijacking

> Attackers hijack/steal valid session content using session token prediction, session replay, session fixation, sidejacking, XSS to capture valid session cookies/IDs. Tools: **Burp Suite** (Sequencer tool tests session token randomness), **JHijack, Ettercap**.
> 

---

### 6️⃣ Web Server Passwords Hacking

> Attackers use password-cracking methods: brute-force, hybrid, dictionary attacks. Default credential lookup: **cirt.net** (lookup database for default passwords/credentials/ports), fortypoundhead.com, defaultpassword.com, default-password.info, routerpasswords.com.
> 

---

### 🛠️ Web Server Attack Tools

```
Immunity CANVAS | OpenVAS | THC Hydra | HULK DoS | MPack
```

---

## 4. Web Server Attack Countermeasures

### 🏛️ Place Web Servers in Separate Secure Server Security Segment on Network

> An ideal web hosting network has THREE segments: **Internet segment**, **secure server security segment (DMZ)**, and **internal network**.
> 

```
Internet → External Firewall → DMZ (Web Server, FTP Server, Mail Server) →
Internal Firewall → Internal Network (Database Server, Application Server)
```

> This separation lets administrators apply firewalls/access control based on security rules for internal network AND Internet traffic toward the DMZ — preventing attacks from outside attackers or malicious insiders.
> 

---

### 🛡️ How to Defend Against Web Server Attacks (19-Point Checklist — CRITICAL)

**Ports:**

- Regularly audit ports to ensure insecure/unnecessary services aren't active
- Limit inbound traffic to port 80 (HTTP) and port 443 (HTTPS/SSL)
- Encrypt or restrict intranet traffic

**Server Certificates:**

- Ensure certificate data ranges are valid, used for intended purpose
- Ensure no certificate revoked; public key valid to a trusted root authority

**Machine.config:**

- Map protected resources to HttpForbiddenHandler; remove unused HttpModules
- Ensure tracing disabled (`<trace enable="false"/>`), debug compiles turned off

**Code Access Security:**

- Implement secure coding practices
- Restrict code access security policy settings
- Configure IIS to reject URLs with "../" ; install new patches/updates

**Numbered items:**

1. Apply restricted ACLs; block remote registry administration; secure the SAM (stand-alone servers only)
2. Ensure security-related settings configured appropriately; restrict metabase file access with hardened NTFS permissions
3. Remove unnecessary ISAPI filters
4. Remove unnecessary file shares (including default admin shares); secure remaining shares with restricted NTFS permissions
5. Relocate sites/virtual directories to non-system partitions; use IIS web permissions to restrict access
6. Remove unnecessary IIS script mappings for optional file extensions
7. Enable minimum level of auditing; use NTFS permissions to protect log files
8. Use a dedicated machine as a web server
9. Create URL mappings to internal servers cautiously
10. Do not install the IIS server on a domain controller
11. Use server-side session ID tracking; match connections with timestamps, IP addresses
12. If a database server (e.g., MS SQL) is used as backend, install it on a separate server
13. Use security tools provided with web server software/scanners to automate securing
14. Physically protect the web server machine in a secure machine room
15. Do not connect an IIS Server to the Internet until fully hardened
16. Do not allow anyone to locally log in except the administrator
17. Configure a separate anonymous user account for each application if hosting multiple web apps
18. Limit server functionality to support only the web technologies to be used
19. Screen and filter incoming traffic requests

---

### 🛡️ Countermeasures: Accounts

- Remove all unused modules and application extensions
- Disable unused default user accounts created during OS installation
- Grant appropriate (least possible) NTFS permissions when creating a new web root directory
- Eliminate unnecessary database users/stored procedures; follow least privilege for database apps
- Use secure web permissions, NTFS permissions, .NET Framework access control (URL authorization)
- Slow down brute-force/dictionary attacks with strong password policies; audit/alert on login failures
- Run processes using least privileged accounts/services/user accounts
- Limit administrator/root-level access to minimum number of users; maintain a record
- Maintain encrypted logs of all user activity
- Disable all noninteractive accounts not requiring interactive login
- Use secure VPN networks (e.g., **OpenVPN**) for multi-server platform access
- Use password managers (e.g., **KeePass**) for proper password policy across accounts
- Enable Separation of Duties (SoD) on server config settings
- Force periodic password changes via password expiry policy

---

### 🛡️ Countermeasures: Files and Directories / SSL / Other

- Redirect all HTTP traffic to HTTPS
- Use **HSTS headers** to force secure connections, preventing downgrade attacks
- Automate SSL/TLS certificate renewal to avoid expired certificates
- Implement rate-limiting to mitigate DDoS on the SSL/TLS handshake process
- Employ file integrity checkers to verify web content and intrusion detection
- Scan uploaded files for malware; store outside the web root
- Use **WAF (Web Application Firewall)** against SQL injection and other common attacks
- Use **SFTP instead of FTP** to encrypt file transfers
- Secure configuration files (.htaccess, web.config) — not accessible from the Web
- Implement version control for web application files to track/revert changes

---

### 🛡️ How to Defend Against DNS Hijacking (7-Point Checklist)

1. Choose an **ICANN-accredited registrar**; encourage setting **Registrar-Lock** on the domain
2. Safeguard the registrant's account information
3. Include DNS hijacking in incident response and business continuity planning
4. Use DNS monitoring tools/services to monitor the IP address of the DNS server; set alerts
5. Avoid downloading audio/video codecs and other downloaders from untrusted websites
6. Install an antivirus program; update regularly
7. Change the default router password (factory settings)

**Additional:** Restrict zone transfers, use script blockers in browser, implement **DNSSEC**, enforce strong password policies/user management, secure SLAs from DNS service providers.

---

### 🛠️ Patch Management Tools

```
GFI LanGuard | Symantec Client Management Suite | Solarwinds Patch Manager |
Kaseya Patch Management | Software Vulnerability Manager (Flexera) | Ivanti Patch for Endpoint Manager
```

> **GFI LanGuard:** Scans network automatically, installs/manages security and non-security patches across Windows/macOS/Linux and third-party apps. Supports auto-download of missing patches and patch rollback.
> 

---

## 5. Quick Exam Cheat Sheet

### 📊 7-Stack Organizational Security Levels

```
Stack 7: Custom Web Applications → Business Logic Flaws
Stack 6: Third-party Components  → Open Source/Commercial
Stack 5: Web Server              → Apache/IIS
Stack 4: Database                → Oracle/MySQL/MS SQL
Stack 3: Operating System        → Windows/Linux/macOS
Stack 2: Network                 → Router/Switch
Stack 1: Security                → IPS/IDS
```

---

### 📊 6-Stage Web Server Attack Methodology

```
1. Information Gathering → 2. Web Server Footprinting → 3. Website Mirroring →
4. Vulnerability Scanning → 5. Session Hijacking → 6. Web Server Passwords Hacking
```

---

### 🔑 Web Server Components

```
Document Root = stores HTML files for web pages
Server Root   = top-level dir with conf/logs/cgi-bin subdirectories
```

---

### 🔥 Common Exam Scenarios

**Q: What sequence do attackers use in directory traversal attacks?**
→ **../ (dot-dot-slash)**

**Q: What are the 3 segments of an ideal web hosting network?**
→ **Internet segment, secure server security segment (DMZ), internal network**

**Q: In an HTTP Response Splitting attack, how many responses does the server generate?**
→ **Two** — the first goes to the attacker, the second to the victim

**Q: What attack forces a web server's cache to flush and store a malicious crafted response?**
→ **Web Cache Poisoning**

**Q: What is the goal of DNS server hijacking?**
→ To redirect user requests from a legitimate site to the attacker's fake/malicious site by changing DNS mapping settings

**Q: What technique do attackers exploit to cause DDoS via amplified DNS responses?**
→ **DNS Amplification Attack** (exploiting recursive DNS queries with spoofed victim IP)

**Q: What tool's Sequencer feature tests the randomness of session tokens?**
→ **Burp Suite**

**Q: What database provides default passwords/credentials/ports for web server administrative interfaces?**
→ **cirt.net**

**Q: What ports should inbound traffic be limited to on a web server?**
→ **Port 80 (HTTP) and Port 443 (HTTPS/SSL)**

**Q: What should be used instead of FTP to encrypt file transfers?**
→ **SFTP**

**Q: What DNS security extension adds an extra protective layer against DNS hacking?**
→ **DNSSEC**

**Q: What HTTP header forces browsers to use secure connections and prevent downgrade attacks?**
→ **HSTS (HTTP Strict Transport Security)**

**Q: What should be done with an IIS server before connecting it to the Internet?**
→ Fully harden it first — never connect until fully hardened

**Q: Where should a backend database server (e.g., MS SQL) be installed relative to the web server?**
→ On a **separate server**

**Q: What type of accounts should have their administrator/root-level access limited?**
→ Limited to the **minimum number of users**, with a maintained record

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 13*