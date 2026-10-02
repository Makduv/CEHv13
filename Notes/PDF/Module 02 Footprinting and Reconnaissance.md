# Module 02: Footprinting and Reconnaissance

## 📋 Table of Contents

1. [Footprinting Concepts](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#1-footprinting-concepts)
2. [Footprinting through Search Engines](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#2-footprinting-through-search-engines)
3. [Footprinting through Internet Research Services](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#3-footprinting-through-internet-research-services)
4. [Footprinting through Social Networking Sites](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#4-footprinting-through-social-networking-sites)
5. [Whois Footprinting](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#5-whois-footprinting)
6. [DNS Footprinting](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#6-dns-footprinting)
7. [Network and Email Footprinting](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#7-network-and-email-footprinting)
8. [Footprinting through Social Engineering](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#8-footprinting-through-social-engineering)
9. [Footprinting Tools and AI Automation](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#9-footprinting-tools-and-ai-automation)
10. [Footprinting Countermeasures](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#10-footprinting-countermeasures)
11. [Quick Exam Cheat Sheet](https://www.notion.so/Module-02-Footprinting-and-Reconnaissance-36be8b7d8a92800f8af1ca6f2f2074b5#11-quick-exam-cheat-sheet)

---

## 1. Footprinting Concepts

### 🎯 Definition

> **Footprinting (Reconnaissance)** = preparatory phase where an attacker seeks to gather **as much information as possible** about a target of evaluation **prior to launching an attack**.
> 
- It is the **first step** in evaluating the security posture of a target's IT infrastructure
- Provides a **security profile blueprint** of the organization
- Helps identify **vulnerabilities** and plan exploitation strategies
- Must be done in an organized, methodological manner

---

### 📦 Types of Footprinting

| Type | Description | Techniques |
| --- | --- | --- |
| **Passive Footprinting** | Gather info **without direct interaction** — target cannot detect | OSINT, proprietary databases, paid services, partner intel sharing |
| **Active Footprinting** | Gather info **with direct interaction** — may leave traces | DNS interrogation, social engineering, network/port scanning, user enumeration |

> 💡 **Exam tip:** Passive = no traffic to target. Active = direct contact, may trigger alerts.
> 

---

### 🗂️ Information Obtained in Footprinting

| Category | Data Collected |
| --- | --- |
| **Organization Information** | Employee names/contact details, phone numbers, branch/location details, partners, web links, background, web technologies, news/press releases, legal documents, patents/trademarks |
| **Network Information** | Domain and sub-domains, network blocks, network topology, trusted routers and firewalls, IP addresses of reachable systems, Whois records, DNS records |
| **System Information** | Web server OS, location of web servers, publicly available email addresses, usernames and passwords |

---

### ⚠️ Footprinting Threats

- **Social Engineering** — extract info from employees without intrusion
- **System and Network Attacks** — find vulnerabilities from config/OS info
- **Information Leakage** — sensitive data falling into attacker hands
- **Privacy Loss** — privilege escalation, accessing private info
- **Corporate Espionage** — competitors gaining trade secrets, product designs
- **Business Loss** — financial damage to e-commerce/banking businesses

---

### 🗺️ Footprinting Methodology (Big Picture)

```
PASSIVE TECHNIQUES                    ACTIVE TECHNIQUES
├── Search Engines                    ├── DNS Footprinting
│   ├── Google Advanced Operators     │   ├── DNS Interrogation
│   ├── GHDB (Google Hacking DB)      │   └── Reverse DNS Lookup
│   └── Shodan                        ├── Network & Email Footprinting
├── Internet Research Services        │   ├── Traceroute
│   ├── TLD/Subdomain discovery       │   └── Email Tracking
│   ├── People Search Services        └── Social Engineering
│   ├── Financial/Job Sites               ├── Eavesdropping (ecoute clandestine)
│   ├── archive.org                       ├── Shoulder Surfing (voyeurisme)
│   ├── Competitive Intel Sites           ├── Dumpster Diving (regarde poubelle)
│   └── Dark Web Tools                    └── Impersonation
├── Social Networking Sites
│   ├── Social Media Analysis
│   └── Social Graph Analysis
└── Whois Footprinting
    ├── Whois Lookup
    └── IP Geolocation Lookup
```

---

## 2. Footprinting through Search Engines

### 🔍 Overview

Search engines (Google, Bing, Yahoo, DuckDuckGo, Baidu, Yandex) are the main source for target information. They extract: technology platforms, employee details, login pages, intranet portals, contact information.

---

### 🧠 Advanced Google Hacking Techniques (Google Dorking)

> **Google hacking** = using advanced search operators to create complex queries that extract **sensitive or hidden information** about targets.
> 

**Syntax:** `operator:search_term` (NO space between operator and query)

### Complete Google Operators Table

| Operator | Purpose | Example |
| --- | --- | --- |
| `site:` | Restrict to specific site/domain | `site:microsoft.com` |
| `inurl:` | Pages with keyword in URL | `inurl:admin login` |
| `allinurl:` | All keywords in URL | `allinurl:google career` |
| `intitle:` | Keyword in page title | `intitle:index.of` |
| `allintitle:` | All keywords in title | `allintitle:detect malware` |
| `intext:` | Keyword in body text | `intext:"vpn configuration"` |
| `inanchor:` | Keyword in anchor links | `inanchor:Norton` |
| `allinanchor:` | All keywords in anchor | `allinanchor:best cloud service` |
| `cache:` | Google's cached version | `cache:www.eff.org` |
| `link:` | Pages linking to URL | `link:www.googleguide.com` |
| `related:` | Similar websites | `related:www.microsoft.com` |
| `info:` | Info about web page | `info:gothotel.com` |
| `location:` | Pages for a specific location | `location:4 seasons restaurant` |
| `filetype:` | Search by file extension | `filetype:pdf` |
| `source:` | From specific source in News | `source:"Hacker News"` |
| `phonebook:` | Phone numbers | `phonebook:Sundar Pichai` |
| `before:` | Content published before date | `ransomware before:2020-06-29` |
| `after:` | Content published after date | `site:wikipedia.org after:2023-01-01` |

### 🔥 Critical Google Dorks for the Exam

```
# Find intranet pages with HR info
intitle:intranet inurl:intranet +intext:"human resources"

# Find admin login pages
inurl:admin intitle:login

# Find exposed configuration files
filetype:cfg OR filetype:conf intext:password

# FTP servers with juicy info
intitle:"index of" inurl:ftp

# Find login portals
inurl:/login intitle:"login"

# Find OpenVPN keys
"-----BEGIN OpenVPN Static key V1-----" ext:key
```

---

### 💾 Google Hacking Database (GHDB)

> Source: **[https://www.exploit-db.com/google-hacking-database**](https://www.exploit-db.com/google-hacking-database**)
> 

GHDB is a database of **"Google Dorks"** — pre-built queries to find sensitive information inadvertently exposed. Part of the Exploit-DB.

**What GHDB can find:**

- Sensitive files (config files, DB dumps, log files with credentials)
- Exposed directories (open directories with sensitive data)
- Error messages (server configs, vulnerabilities)
- Vulnerable devices (devices with known vulnerabilities)

**GHDB Categories:**
Footholds | Files Containing Usernames | Sensitive Directories | Web Server Detection | Vulnerable Files | Vulnerable Servers | Error Messages | Files Containing Juicy Info | Files Containing Passwords | Sensitive Online Shopping Info | Network/Vulnerability Data | Pages Containing Login Portals | Various Online Devices | Advisories and Vulnerabilities

**How attackers leverage GHDB:**

- Reconnaissance (exposed files, directories, devices)
- Exploiting Misconfigurations
- Finding Vulnerable Systems (outdated software versions)
- Credential Harvesting (usernames/passwords)
- Identifying Open Ports and Services

> **SearchSploit** = CLI tool to search Exploit-DB offline (useful for air-gapped networks)
> 

---

### 🌐 Footprinting through Shodan Search Engine

> Source: [**https://www.shodan.io**](https://www.shodan.io/)
> 

Shodan is a search engine that **scans the entire Internet** for internet-connected devices — servers, IoT devices, webcams, industrial systems. Used for VPN and VoIP footprinting.

**What Shodan reveals:**

- Open ports and running services
- Web server banners (OS, software versions)
- Geographic location of devices
- Default or no authentication devices
- Industrial control systems (SCADA/ICS)

**Censys** ([https://censys.io](https://censys.io/)) — similar to Shodan, shows OS, services, ports, geographic location.

---

### 🤖 Google Hacking with AI

Attackers use AI tools (ShellGPT, ChatGPT) to automate Google hacking:

```bash
# Example AI-assisted command using ShellGPT
sgpt --chat footprint --shell "Use filetype search operator to obtain pdf
files on the target website eccouncil.org and store the result in recon1.txt"
```

This generates and executes:

```bash
lynx --dump "<http://www.google.com/search?q=site:eccouncil.org+filetype:pdf>" \\
| grep "http" | cut -d "=" -f2 | grep -o "http[^&]*" > recon1.txt
```

---

## 3. Footprinting through Internet Research Services

### 🏢 Finding Company TLDs and Sub-domains

**Tools to find sub-domains:**

- **Netcraft** ([https://www.netcraft.com](https://www.netcraft.com/)) — Internet security services, lists sub-domains, web server OS, hosting providers
- **DNSdumpster** ([https://dnsdumpster.com](https://dnsdumpster.com/)) — discovers hosts related to a domain
- **Sublist3r** — subdomain enumeration: `sublist3r -d eccouncil.org -o eccouncil_subdomains.txt`
- **subfinder** — `subfinder -d certifiedhacker.com`
- Google dork: `site:microsoft.com -inurl:www` → finds subdomains

---

### 👤 People Search Services

Services that aggregate public personal information:

- **Spokeo** ([https://www.spokeo.com](https://www.spokeo.com/))
- **pipl** ([https://pipl.com](https://pipl.com/))
- **Been Verified** ([https://www.beenverified.com](https://www.beenverified.com/))
- **Intelius** ([https://www.intelius.com](https://www.intelius.com/))
- **US Search** ([https://www.ussearch.com](https://www.ussearch.com/))
- **Whitepages** ([https://www.whitepages.com](https://www.whitepages.com/))

**Info attackers get from people search:** name, age, address, phone, email, social profiles, relatives

---

### 💼 Financial Services and Job Sites

**Job sites as intelligence source:**

- **LinkedIn, Glassdoor, Monster, Indeed** → reveal: tech stack used, internal tools, org structure, hiring patterns → infer security posture weaknesses

**Financial sites:**

- **SEC EDGAR** ([https://www.sec.gov](https://www.sec.gov/)) → public company filings, financials, board members
- **OpenCorporates** ([https://opencorporates.com](https://opencorporates.com/)) → company data worldwide
- **Google Finance, Yahoo Finance** → financial data

---

### 🗄️ [archive.org](http://archive.org/) (Wayback Machine)

> Source: [**https://web.archive.org**](https://web.archive.org/)
> 

The Internet Archive stores historical snapshots of websites. Attackers use it to:

- View deleted/modified pages
- Find old employee directories
- Discover old login portals no longer secured
- Access archived sensitive documents

> 💡 **Exam tip:** To remove pages → request deletion directly from [archive.org](http://archive.org/)
> 

---

### 🧩 Competitive Intelligence and Business Profile Sites

| Tool | Purpose |
| --- | --- |
| **SEMRush** | Competitive keyword research, competitor analysis |
| **Euromonitor** | Market research, industry reports |
| **Experian** | Consumer data, marketing insights |
| **The Search Monitor** | Brand/trademark monitoring, competitive intel |
| **USPTO** ([https://www.uspto.gov](https://www.uspto.gov/)) | Patent and trademark information |
| **BizStats** | Industry financial ratios |

---

### 🌑 Dark Web Footprinting

Attackers use **Tor Browser** to access the dark web and search for:

- Leaked credential databases
- Sensitive company documents
- Stolen PII data about employees
- Information about previous breaches

**Advanced search parameters on dark web:**

```
"John Doe" site:facebook.com              → Personal profiles
"John Doe" site:scholar.google.com        → Scientific publications
"John Doe" court records                  → Legal records
"John Doe" site:example.com "employee directory"  → Member directories
"John Doe" medical records                → Medical info
"John Doe" location history               → Location records
```

---

### ⚠️ Monitoring Targets Using Alerts

- **Google Alerts** — monitors web for target mentions
- **X Alerts (Twitter Alerts)** — social media mentions
- **Giga Alerts** — broader web monitoring

---

## 4. Footprinting through Social Networking Sites

### 📱 What Attackers Harvest from Social Media

**From Individual Users:**

| User Activity | Attacker Gets |
| --- | --- |
| Maintain profile | Contact info, location, related information |
| Connect to friends, chat | Friends list, friends' info, related info |
| Share photos/videos | Identity of family members, interests, info |
| Play games, join groups | Interests |
| Create events | Activities |

**From Organizations:**

| Org Activity | Attacker Gets |
| --- | --- |
| User surveys | Business strategies |
| Promote products | Product profile |
| User support | Social engineering material |
| Recruitment | Platform/technology information |
| Background checks | Type of business |

---

### 🔎 Social Media Analysis Tools

- **BuzzSumo** ([https://buzzsumo.com](https://buzzsumo.com/)) — finds most shared content by topic, author, or domain across Twitter, Facebook, LinkedIn, Google+, Pinterest
- **Google Trends** — track trending topics
- **Hashatit** — track hashtags across platforms
- **Ubersuggest** — SEO and social data

---

### 🛠️ theHarvester — LinkedIn Enumeration

```bash
theHarvester -d eccouncil -l 200 -b linkedin
```

Obtains: employee names, job titles, email formats

---

### 🔗 Public Source Code Repositories

GitHub, GitLab, Bitbucket can reveal:

- API keys, tokens, passwords accidentally committed
- Internal architecture details
- Employee handles and emails
- Infrastructure code (Terraform, Ansible configs)

**Recon-ng** — framework for gathering info from public source-code repositories

---

### 🤖 Social Networking Footprinting with AI

```
AI Prompt Example: "Use Sherlock to gather personal information about
Sundar Pichai and save the result in recon2.txt"
```

Generates: `sherlock SundarPichai --output recon2`
Returns: All associated accounts across 100+ platforms

---

## 5. Whois Footprinting

### 🌐 Whois Overview

> **Whois** = query/response protocol (port **TCP 43**) for querying databases that store registered users/assignees of Internet resources (domain names, IP blocks, autonomous systems).
> 

**Regional Internet Registries (RIRs)** maintain Whois databases:

| RIR | Region |
| --- | --- |
| **ARIN** | North America |
| **RIPE NCC** | Europe, Middle East, Central Asia |
| **APNIC** | Asia Pacific |
| **LACNIC** | Latin America and Caribbean |
| **AFRINIC** | Africa |

---

### 📋 What Whois Returns

- Domain name details
- Contact details of domain owners
- Domain name servers
- NetRange (IP block)
- When domain was created
- Expiry records
- Last updated record

**What attackers do with Whois data:**

- Gather personal info for social engineering
- Create a map of the target's network
- Obtain internal details of the target network

---

### 🛠️ Whois Tools

- [**https://whois.domaintools.com**](https://whois.domaintools.com/) — full Whois lookup
- [**https://www.tamos.com**](https://www.tamos.com/) — Whois lookup
- **Batch IP Converter** ([http://www.sabsoft.com](http://www.sabsoft.com/)) — bulk IP/hostname/domain info

**Example Whois Record fields:**
Registrant, Registrar, Registrar Status, Dates (Created/Expires/Updated), Name Servers, Tech Contact, IP Address, IP Location, ASN, Domain Status, IP History, Registrar History, Hosting History

---

### 📍 IP Geolocation Lookup

Determines the **physical location** of an IP address: country, region, city, ISP.

**Tools:**

- **IP2Location** ([https://www.ip2location.com](https://www.ip2location.com/))
- **MaxMind** ([https://www.maxmind.com](https://www.maxmind.com/))
- [**ipinfo.io**](http://ipinfo.io/) ([https://ipinfo.io](https://ipinfo.io/))

---

## 6. DNS Footprinting

### 🗺️ DNS Overview

DNS footprinting reveals **zone data**: DNS domain names, computer names, IP addresses, much more about a network.

Attackers use DNS tools like **SecurityTrails, Fierce, DNSChecker, zdns** to retrieve DNS records.

---

### 📋 DNS Record Types (MUST KNOW)

| Record Type | Description |
| --- | --- |
| **A** | Maps hostname → IPv4 address |
| **AAAA** | Maps hostname → IPv6 address |
| **MX** | Points to domain's mail server |
| **NS** | Points to hostname's name server |
| **CNAME** | Canonical name — allows aliases to a host |
| **SOA** | Start of Authority — indicates authority for domain |
| **SRV** | Service records (locate specific services) |
| **PTR** | Maps IP address → hostname (reverse DNS) |
| **RP** | Responsible person |
| **HINFO** | Host information record — CPU type and OS |
| **TXT** | Unstructured text records (often SPF, DKIM) |

> 💡 **Exam tip:** A/AAAA/MX/NS/CNAME/SOA/PTR are the most tested record types!
> 

---

### 🔍 DNS Interrogation Techniques

**nslookup:**

```bash
nslookup certifiedhacker.com          # Basic lookup
nslookup -type=MX certifiedhacker.com # MX records only
nslookup -type=NS certifiedhacker.com # NS records
```

**dig:**

```bash
dig certifiedhacker.com ANY            # All records
dig +short google.com NS               # Short NS output
dig @8.8.8.8 certifiedhacker.com A    # Query specific DNS server

# Enumerate all NS with IP using dig
dig +short google.com NS | xargs I{} dig +nocmd +noall +answer @{} google.com \\
| grep -E 'CNAME A|AAAA'
```

**DNSRecon** (powerful DNS enumeration):

```bash
dnsrecon -d certifiedhacker.com -D wordlist.txt -t std, brt, axfr
```

---

### 🔄 Reverse DNS Lookup

Resolves an IP address back to a hostname:

```bash
dig -x 162.241.216.11
nslookup 162.241.216.11
```

**Why useful:** Reveals hostnames of servers in target IP range, maps internal naming conventions.

---

### ⚡ Zone Transfer (AXFR) Attack

> **DNS Zone Transfer (AXFR)** = mechanism to replicate DNS database between servers. If not restricted, an attacker can dump ALL DNS records for a domain.
> 

```bash
dig axfr @ns1.bluehost.com certifiedhacker.com
nslookup
> server ns1.bluehost.com
> set type=any
> ls -d certifiedhacker.com
```

**Why dangerous:** Reveals ALL hosts, subdomains, IP addresses — complete network map.

> 💡 **Countermeasure:** Restrict AXFR to authorized secondary DNS servers only (split DNS / authorized zone transfer).
> 

---

### 🤖 DNS Lookup with AI

```
AI Prompt: "Install and use DNSRecon to perform DNS enumeration on
the target domain www.certifiedhacker.com"
```

Generates: `sudo apt-get install -y dnsrecon && dnsrecon -d certifiedhacker.com -t std`

---

## 7. Network and Email Footprinting

### 🌐 Locate the Network Range

- Use Whois to find IP blocks assigned to target
- **IANA private IP ranges:**
    - `10.0.0.0 – 10.255.255.255` (/8)
    - `172.16.0.0 – 172.31.255.255` (/12)
    - `192.168.0.0 – 192.168.255.255` (/16)

---

### 🛤️ Traceroute Analysis

> **Traceroute** maps the route packets take from source to destination — reveals intermediate routers, hops, TTLs, potential firewall locations.
> 

**Three traceroute variants:**

| Type | Command | Protocol | Notes |
| --- | --- | --- | --- |
| **ICMP Traceroute** | `tracert target` (Windows) / `traceroute target` (Linux) | ICMP | Default; often blocked by firewalls |
| **TCP Traceroute** | `sudo tcptraceroute www.google.com` | TCP | Bypasses ICMP-blocking firewalls |
| **UDP Traceroute** | `traceroute www.google.com` (Linux) | UDP | Linux default |

**Traceroute Tools:**

- **NetScanTools Pro** — ICMP, UDP, TCP traceroute; IPv4/IPv6; identifies country per hop
- **PingPlotter** — visual traceroute with historical analysis
- **Traceroute NG** — advanced network path analysis
- **tracert** (Windows built-in)

**What traceroute reveals:**

- Hop-by-hop path to target
- Geographic location of routers
- Network topology and ISP info
- Firewall locations (hops with `* *` = filtered)

---

### 📧 Email Footprinting

Email headers contain a wealth of information about the sender's mail infrastructure.

### What Email Headers Reveal

- **Sender's IP address** (originating mail server)
- **Mail server** software and version
- **Route** the email traveled through
- **Time/date** stamps per hop
- **Spam filter** decisions

### Email Tracking Tools

| Tool | Purpose |
| --- | --- |
| **eMailTrackerPro** | Traces email path, shows on world map, shows server info |
| **IP2LOCATION Email Header Tracer** | Trace email paths using headers |
| **Read Notify** | Email open/click tracking |
| **Email Tracker Pro** | Track email delivery and geographic origin |

---

### 🌐 theHarvester — Email Harvesting

```bash
theHarvester -d eccouncil.com -b all -l 500
# -d = domain
# -b = source (google, bing, linkedin, all)
# -l = limit results
```

**Returns:** Email addresses, subdomains, virtual hosts, open ports, banners

---

## 8. Footprinting through Social Engineering

### 🎭 Social Engineering Techniques in Footprinting

| Technique | Description |
| --- | --- |
| **Eavesdropping** | Listen to conversations to gather sensitive info |
| **Shoulder Surfing** | Observe someone entering passwords/PIN at physical proximity |
| **Dumpster Diving** | Search trash for discarded documents, hardware, printed credentials |
| **Impersonation** | Pose as IT support, vendor, employee to extract info |

---

## 9. Footprinting Tools and AI Automation

### 🛠️ Key Footprinting Tools (Exam Critical)

| Tool | Purpose | URL |
| --- | --- | --- |
| **Maltego** | Visual relationship mapping — people, orgs, websites, infrastructure | [https://www.maltego.com](https://www.maltego.com/) |
| **Recon-ng** | Full-featured web recon framework with independent modules | [https://github.com](https://github.com/) |
| **OSINT Framework** | Organized collection of OSINT tools by category | [https://osintframework.com](https://osintframework.com/) |
| **theHarvester** | Email, subdomain, host, IP harvesting | [https://github.com](https://github.com/) |
| **Shodan** | Internet-connected device search engine | [https://www.shodan.io](https://www.shodan.io/) |
| **Netcraft** | Subdomain enumeration, web tech identification | [https://www.netcraft.com](https://www.netcraft.com/) |
| **subfinder** | Fast passive subdomain enumeration | [https://projectdiscovery.io](https://projectdiscovery.io/) |
| **Sublist3r** | Subdomain enumeration using OSINT | [https://github.com](https://github.com/) |
| **DNSRecon** | DNS enumeration | [https://github.com](https://github.com/) |
| **BillCipher** | Multi-function info gathering for website/IP | [https://github.com](https://github.com/) |
| **Recon-Dog** | Automated recon with multiple API integrations | [https://github.com](https://github.com/) |
| **FOCA** | Metadata extraction from documents | — |
| **Sudomy** | Subdomain enumeration | [https://github.com](https://github.com/) |
| **whatweb** | Identify website technologies | [https://github.com](https://github.com/) |
| **Raccoon** | Offensive security recon/info gathering | [https://github.com](https://github.com/) |
| [**OSINT.SH**](http://osint.sh/) | Online OSINT gathering | [https://osint.sh](https://osint.sh/) |

---

### 🔬 Maltego Deep Dive

> **Maltego** = automated tool for determining **real-world links and relationships** between people, groups, organizations, websites, Internet infrastructure, documents.
> 
- Uses **"entities"** and **"transforms"** to map connections
- Can discover: email addresses, phone numbers, domains, DNS names, Netblocks, IP addresses
- Add a **Website entity** → transform → reveals domain ownership, related infrastructure

---

### 🕸️ OSINT Framework

> [https://osintframework.com](https://osintframework.com/) — organized OSINT tools tree by category.
> 

**Tool indicators:**

- **(T)** — local tool install required
- **(D)** — Google dork
- **(R)** — requires registration
- **(M)** — URL must be manually edited

---

### 🤖 AI-Powered Footprinting (CEH v13 New Topic)

### AI Tools for OSINT

| Tool | Description |
| --- | --- |
| **Taranis AI** | NLP + AI to gather and organize news/threat intel |
| [**Cylect.io**](http://cylect.io/) | Integrates multiple databases for OSINT |
| **ChatPDF** | AI analysis of PDF documents |
| [**Bardeen.ai**](http://bardeen.ai/) | Automates data collection from online sources |
| **DarkGPT** | GPT-4 to query leaked databases |
| **PenLink Cobwebs** | AI-powered OSINT for cybersecurity investigations |
| **Explore AI** | AI-powered YouTube search for OSINT |

### AI Benefits in OSINT

- **Improved Efficiency** — automates web scraping, data extraction
- **Greater Scope** — analyzes surface web, deep web, AND dark web simultaneously
- **Enhanced Visibility** — connects disparate data points, reveals hidden relationships
- **Increased Investigator Safety** — anonymous, automated dark web investigation

### Custom Python Script with AI

```
AI Prompt: "Develop a Python script which will accept domain name
www.microsoft.com as input and execute a series of website footprinting
commands, including DNS lookups, WHOIS records retrieval, email
enumeration, and more"
```

Generated script structure:

```python
import subprocess

def dns_lookup(domain):
    return subprocess.getoutput(f"dig {domain} ANY +noall +answer")

def whois_lookup(domain):
    return subprocess.getoutput(f"whois {domain}")

def email_enumeration(domain):
    return subprocess.getoutput(f"theHarvester -d {domain} -b all -l 100")

def run_footprinting(domain):
    print("\\nPerforming DNS Lookup...")
    dns_info = dns_lookup(domain)
    print(dns_info)
    # ... etc

domain = 'www.microsoft.com'
run_footprinting(domain)
```

---

## 10. Footprinting Countermeasures

> Actions taken to **prevent or offset information disclosure** about the organization.
> 

### ✅ Complete Countermeasures List

**Access & Policy Controls:**

- Restrict employees' access to social networking sites from the org's network
- Develop and enforce **security policies** to regulate what employees can reveal to third parties
- Implement **multi-factor authentication** (MFA) for all systems
- Disable or delete accounts of employees who left the organization

**DNS & Network Controls:**

- Set apart internal and external DNS — use **split DNS**; restrict **zone transfer** to authorized servers only
- Disable directory listings in web servers
- Do not enable protocols that are not required
- Always use TCP/IP and IPsec filters for defense-in-depth
- Hide IP address behind a **VPN or secure proxy**

**Web Server Controls:**

- Configure web servers to **avoid information leakage** (disable verbose error messages)
- Configure IIS to avoid info disclosure through **banner grabbing**
- Prevent search engines from **caching** web pages (robots.txt, meta noindex)
- Use **anonymous registration services** for domains
- Keep domain name profile **private** (Whois privacy services)
- Sanitize details provided to Internet registrars to **hide direct contact details**
- Request [archive.org](http://archive.org/) to delete website history

**Physical & Operational Security:**

- Educate employees to use **pseudonyms** on blogs, groups, forums
- Do NOT reveal critical info in press releases, annual reports, product catalogs
- Limit amount of information published on websites/Internet
- Place critical documents (business plans, proprietary docs) **offline**
- Train employees to thwart **social engineering** techniques and attacks
- Ensure no critical info is displayed on **notice boards or walls**

**Digital & Social Media Security:**

- Use footprinting techniques to **discover and remove sensitive publicly available info**
- Avoid domain-level **cross-linking** for critical assets
- Encrypt and **password-protect** sensitive information
- Implement **captchas and rate limiting** on public-facing services
- Disable **geo-tagging** on cameras to prevent geolocation tracking
- Avoid revealing **location or travel plans** on social networking sites
- Turn off **geolocation access** on all mobile devices when not required
- Configure mail servers to **ignore mails from anonymous individuals**

**Honeypots & Detection:**

- Deploy **honeypots or honeynets** within the network to attract and detect footprinting activities

---
