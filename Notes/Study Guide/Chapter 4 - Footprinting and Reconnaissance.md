# Chapter 4 - Footprinting and Reconnaissance

# Open Source Intelligence

**Footprinting** = trying to pick up the footprint of the target organization.

**OSINT** = identifying information about your target using **freely available sources**. Used when you have little or no information about the target, to find details about individuals or the organization.

**Key points:**

- Organizations aren't aware of how much information they leak
- Can reveal a foothold, or name targets for **social engineering** attacks
- **White box pentest** = information is already provided, so OSINT isn't needed. It belongs to **black box** engagements
- Also useful defensively — for **awareness**: showing the company what an attacker can find about it
- Entirely **passive**: you never touch the target's systems, so there's nothing for them to detect

## Companies

Resources to gather information about companies — either for social engineering, or about the company's network. Companies try to hide some of it, but public obligations force a lot out.

### EDGAR

Public **US** companies are required to file information about themselves. The **SEC** (Securities and Exchange Commission) hosts **EDGAR**, a database of these public filings.

You can find:

- Organizational structure — who holds which position
- Company finances, employee stock option plans
- **Schedule 14A** — the proxy statement, which includes the annual report to shareholders

Limitation: US public companies only.

### Domain Registrars

EDGAR only covers public companies — domain data is another source.

**How the internet is governed** for names and addresses:

- **ICANN** — Internet Corporation for Assigned Names and Numbers — responsible for names and numbering
- **IANA** — Internet Assigned Numbers Authority — manages IP address allocation

Several companies perform **registrar** functions — GoDaddy, DomainMonger.

Information is pulled from the registrar or the regional registry with the **whois** program.

Expect to find: addresses, phone numbers, **administrative contact**, **technical contact**, name servers.

Often **redacted for privacy** now → you may get nothing useful.

### Regional Internet Registries (RIRs)

IANA knows all IP addresses and hands them out, based on need, to the **RIRs**, which allocate them to organizations in their geographic region. Each RIR has its own database.

| RIR | Region |
| --- | --- |
| **AfriNIC** | Africa |
| **ARIN** | US, Canada, Antarctica, parts of the Caribbean |
| **APNIC** | Asia, Australia, New Zealand and neighbouring countries |
| **LACNIC** | Latin America and parts of the Caribbean |
| **RIPE NCC** | Europe, Russia, Greenland, Middle East, part of Central Asia |

Why it matters: a whois on an IP tells you which **netblock** the organization owns → that defines the ranges you'll later scan.

## People

**theHarvester** — script that searches through different sources to locate contact information based on a domain name. Many sources require an **API key**.

**Other sources for email addresses:** **PGP key servers** — encryption keys sometimes used for email. One of the oldest is hosted at MIT (`pgp.mit.edu`); better for individuals than companies.

theHarvester is good for finding information about people at a company automatically, but to go deeper use **people search engines**: Spokeo, BeenVerified, Pipl, Wink, Intelius. Paid, and mostly US-focused.

**PeekYou** — more focused on social networking presence; searchable by **username**.

## Social Networking

Shows how people connect.

- **Facebook** — communities, news, reconnecting with people. People let their guard down. Provides a **Graph API Explorer** where API queries can be tested. Results depend on privacy settings. Not always the best place for business/employee information
- **X (Twitter)** — news, updates, marketing information. Needs an **API key** from the X developer site
- **LinkedIn** — business networking; lists employees, job titles and **technologies** in use. Tool: **CrossLinked**. *InSpy* was the older tool but now returns 404s and asks for an API key
- **MySpace and older sites** — music and personal information sharing

theHarvester also searches through some social networking sites.

### Username Search

**Sherlock** — takes a username and checks it across many social networks to find where that identity exists. Useful for pivoting from a handle to someone's wider online presence.

### Aggregation tools

- **recon-ng** — modular OSINT framework; you load a module, set the target, run it, and results are stored in a workspace. Most modules need API keys
- **Maltego** — OSINT in general, with a **visual representation** of the reconnaissance data collected (entities linked by transforms)

## Job Sites

Job postings leak the **technology stack** and **organizational structure**: the required skills tell you what's deployed, and the reporting line tells you how the team is built.

---

# Cheatsheet — Tools

| Tool | Purpose | Command |
| --- | --- | --- |
| **EDGAR** | Filings of US public companies — org structure, finances, Schedule 14A | Web: `sec.gov/edgar` |
| **whois** | Domain/IP registration: contacts (admin/tech), netblocks, name servers | `whois google.com
whois 63.12.58.112` |
| **theHarvester** | Emails, subdomains, contact info from a domain name | `theHarvester -d domain.com -b source
theHarvester -d domain.com -b all` |
| **PGP key servers** | Email addresses of individuals via their encryption keys | Web: `pgp.mit.edu` |
| **People search** | Personal details — Spokeo, BeenVerified, Pipl, Wink, Intelius (paid, mostly US) | Web |
| **PeekYou** | Social networking presence, searchable by username | Web |
| **Facebook Graph API Explorer** | Test queries against the Facebook API (limited by privacy settings) | Web |
| **CrossLinked** | List LinkedIn employees (replaces InSpy, now dead) | `crosslinked "Company Name"` |
| **Sherlock** | Find a username across social networks | `sherlock --fo smithsearch jsmith smith johnsmith` |
| **recon-ng** | Modular OSINT framework, needs API keys | `recon-ng` |
| **Maltego** | General OSINT with visual representation of collected data | GUI |
| **Job sites** | Technology stack in use + organizational structure | Web |

# Domain Name System (DNS)

Transforms domain names (**FQDNs**) into IP addresses.

An FQDN like `www.labs.domain.com.` refers to a specific host or system, and DNS reads it **right to left**.

## The DNS tree

| Level | Example |
| --- | --- |
| **Root** | the implicit `.` at the very end |
| **TLD** (top-level domain) | `.com`, `.org`, `.edu`, and country codes `.fr`, `.us`, `.uk`, `.es` |
| **SLD** (second-level domain) | `domain` |
| **Subdomains** | `labs` — as many as you want |
| **Hostname** | `www` |
| **FQDN** (fully qualified domain name) | `www.labs.domain.com` |

## Name Lookups

- **URI** (Uniform Resource Identifier) — the full identifier of a resource, including the scheme/protocol (`http`, `https`, `ftp`)
- **URL** (Uniform Resource Locator) — a URI that also says *where* to find the resource and how to get it (a URL is a URI, not the reverse)

Before opening a website, the system needs an **IP** to put in the layer 3 header → it issues a **name resolution request** to a **name resolver**.

Caching happens at every level: your computer caches results so it doesn't resolve the same name again, and the resolver caches too so it doesn't always have to ask the authoritative server. In TCP/IP the name resolver is the **DNS server**.

**How a first-time lookup works (recursive resolution):**

1. Your machine asks its configured **resolver** (recursive DNS server)
2. The resolver asks a **root** server → gets referred to the **TLD** server (`.com`)
3. It asks the **TLD** server → gets referred to the domain's **authoritative** name server
4. It asks the **authoritative** server → gets the actual **A record**
5. The resolver caches the answer (for the length of the **TTL**) and returns it to you

## Record types

| Record | Meaning |
| --- | --- |
| **A** | Maps a hostname to an **IPv4** address |
| **AAAA** | Maps a hostname to an **IPv6** address |
| **MX** | Mail exchanger — which server mail for that domain should be sent to |
| **NS** | Name server — the authoritative name server(s) for the domain |
| **SOA** | Start of Authority — info about the zone: primary NS, admin contact, **serial number** (changes when the zone is updated), refresh/retry timers |
| **CNAME** | Canonical name — an **alias** pointing one FQDN to another |
| **PTR** | Pointer — maps an **IP back to an FQDN** (reverse lookup) |
| **TXT** | Arbitrary text — heavily used for **email security** (SPF, DKIM, DMARC) and domain ownership verification |

Note: a hostname needs to map to an IP, but an IP does **not** need to map back to a hostname (PTR is optional).

## Using `host`

On most Unix-like systems, pass the hostname to `host` and you get the IP:

```
host www.domain.com        # forward lookup → IP
host 21.25.65.110          # reverse lookup → hostname(s) via PTR
```

**Reverse lookup caveat:** `host <IP>` looks for a **PTR** record. PTR is optional, so you may get:

```
Host x.x.x.x not found: 3(NXDOMAIN)
```

This does **not** mean the IP doesn't exist or the site is down — only that **no PTR record exists** for that IP.

## Using `nslookup`

```
nslookup domain.com          # quick A lookup
```

Interactive shell for specific record types:

```
nslookup
> set type=ns
> domain.com
> set type=mx
> domain.com
```

## Using `dig`

The most detailed and scriptable of the three — the pentester's default.

```
dig domain.com              # A record
dig domain.com MX           # specific type
dig domain.com ANY          # all records
dig +short domain.com       # just the answer
```

### host vs nslookup vs dig

| Tool | Best for |
| --- | --- |
| **host** | Quick, simple one-line lookups |
| **nslookup** | Available everywhere including **Windows**; interactive mode for changing record types |
| **dig** | Detailed, full DNS output, scripting, zone transfers — the tool of choice for recon |

## Zone Transfers

A zone transfer dumps **every hostname in a domain** at once. It's a legitimate mechanism used between authoritative name servers (primary → secondary) to keep them **in sync**.

For an attacker it's a jackpot — the entire internal map in one query — so most domains **only allow transfers to their configured secondary NS**, not to anyone. Many also use **split DNS** (see below), so even a successful transfer only reveals the external view.

```
dig axfr domain.com @192.168.25.3
```

## Brute Force

When zone transfers are refused, guess hostnames from a wordlist. **dnsrecon** extracts common records and discovers hostnames through repeated requests based on a wordlist:

```
dnsrecon -d domain.com -D /usr/share/wordlists/dnsmap.txt -t brt
```

## Passive DNS

Uses the **cached DNS entries** already on a local system — no queries sent to the target, so fully passive. Entries live for the length of the record's **TTL**.

Dump the cache:

- **Windows:** `ipconfig /displaydns`
- **Linux:** only if a caching service is running (`dnsmasq` or `nscd`)

For **external** recon it's not much use. **Internally** it's valuable — it can reveal internal address blocks and IPs, including `.local` names (a TLD that can't be used across the internet).

This ties back to **split DNS**: organizations run one view for the outside (limited public records) and one for the inside (full internal records), so external attackers never see the internal namespace.

---

# Cheatsheet — Commands

| Command | Purpose |
| --- | --- |
| `host www.domain.com` | Forward lookup → IPv4 |
| `host 21.25.65.110` | Reverse lookup → hostname via PTR |
| `nslookup domain.com` | Quick lookup (works on Windows too) |
| `nslookup` → `set type=ns` → `domain.com` | Interactive: name servers |
| `nslookup` → `set type=mx` → `domain.com` | Interactive: mail servers |
| `dig domain.com` | Detailed A record lookup |
| `dig domain.com MX` | Specific record type |
| `dig domain.com ANY` | All records |
| `dig +short domain.com` | Answer only |
| `dig axfr domain.com @192.168.25.3` | Zone transfer against a specific NS |
| `dnsrecon -d domain.com -D wordlist.txt -t brt` | Hostname brute force |
| `ipconfig /displaydns` | Dump local DNS cache (Windows) |

**Record types to memorise:** A (IPv4) · AAAA (IPv6) · MX (mail) · NS (name server) · SOA (zone info + serial) · CNAME (alias) · PTR (reverse) · TXT (SPF/DKIM/DMARC).

# Passive Reconnaissance

Passive recon means gathering information **without sending traffic to the target** — you observe what's already available or already flowing, so there's nothing for the target to detect.

## Tools

**p0f** — passive OS fingerprinting. It watches network headers (things like TTL, window size, TCP options) to guess the operating system of a host **without sending a single packet**. Largely obsolete now: modern traffic is **encrypted**, and there's less to read from headers alone, so it isn't very useful anymore.

**Wireshark** — packet capture and analysis. Gives far more information: it lets you inspect every layer of captured traffic in detail. Passive as long as you're only **capturing**, not injecting.

**Recon (browser plugin)** — a tool to quickly look up information about a site directly from your web browser while you're on it.

---

# Cheatsheet — Tools

| Tool | Purpose |
| --- | --- |
| **p0f** | Passive OS fingerprinting from network headers (mostly obsolete — encryption) |
| **Wireshark** | Packet capture and deep traffic analysis (passive when only capturing) |
| **Recon** | Browser plugin for quick site information lookup |

# Website Intelligence

Any site with **pragmatic (dynamic) elements** has the potential to be compromised — applications are a common point of attack for adversaries.

Working **from the bottom of the stack up**, you can look at what the server is, the operating system, then the web server, then the application layers on top.

One way to get information is simply to **connect to the web server and issue a request** to it (the response headers and behaviour reveal a lot).

## Tools

**Netcraft** (`netcraft.com`) — gives the **hosting history** of a website:

- The owner of the **netblock** containing the IP address
- The **operating system** it runs on
- Sometimes the **web server version** and which modules are enabled

**Wappalyzer** (plugin) — lists the **technologies** it identifies: web server, programming framework, ad networks, tracking technology, CMS, JS libraries.

**Firebug** — performs deep investigation on the page (inspecting the DOM, scripts, network requests). *(Now folded into the browser's built-in developer tools.)*

**HTTrack** — **mirrors** a website so you can examine it **offline without leaving tracks** on the target. Caveat: not all technology comes across — server-side and dynamic elements won't mirror.

---

# Cheatsheet — Tools

| Tool | Purpose |
| --- | --- |
| **Netcraft** | Hosting history — netblock owner, OS, web server version |
| **Wappalyzer** | Identify site technologies (server, framework, CMS, trackers) |
| **Firebug** | Deep page investigation (now part of browser dev tools) |
| **HTTrack** | Mirror a site to browse offline without touching the target again |

# Technology Intelligence

## Google Dorking (Hacking)

Using Google **keywords/operators** to narrow a search and surface useful responses — it can help identify **vulnerabilities and technology** exposed on a site.

A **dork** = a string built from Google operators designed to return specific, useful results.

**Key operators:**

| Operator | What it does |
| --- | --- |
| `site:domain.com` | Restrict results to a single domain |
| `inurl:index` | Pages with a term in the **URL** |
| `intitle:"index of"` | Pages with a term in the **page title** (classic for open directory listings) |
| `filetype:pdf` | Restrict to a **file type** (pdf, xls, docx, conf, log…) |
| `intext:password` | A term in the **body** of the page |
| `cache:domain.com` | Google's **cached** copy of a page |
| `-keyword` | **Exclude** a term |

Operators can be combined: `site:example.com filetype:pdf intext:confidential`.

**GHDB (Google Hacking Database)** — stores ready-made dorks in categories: **footholds, vulnerable files, error messages, sensitive directories**, files containing passwords, and more.

## Internet of Things (IoT)

Devices with little to no input/output capability — refrigerators, thermostats, fans, light bulbs, cameras. *(Devices with keyboards and screens that run full applications — smartphones, computers — are **not** IoT.)*

Why they matter to an attacker: they're often poorly secured and can be a **starting point into the enterprise network** — a foothold to pivot from.

**Shodan** (`shodan.io`) — a search engine **specifically for internet-connected/IoT devices**. It indexes a huge number of devices along with their **vendor, device type and capabilities** (from their service banners).

- Results show a **map with device counts by country**
- Shodan also identifies the **organizations** where the devices are located

---

# Cheatsheet — Tools & Operators

| Tool / Operator | Purpose |
| --- | --- |
| **Google dork** | Search string using operators to find exposed tech/vulns |
| `site:` | Limit to one domain |
| `inurl:` | Term in the URL |
| `intitle:` | Term in the page title |
| `filetype:` | Limit to a file type |
| `intext:` | Term in the page body |
| `cache:` | Google's cached copy |
| `-term` | Exclude a term |
| **GHDB** | Database of prebuilt dorks (footholds, exposed files, errors, passwords) |
| **Shodan** (`shodan.io`) | Search engine for IoT/internet-connected devices — vendor, type, location, org |