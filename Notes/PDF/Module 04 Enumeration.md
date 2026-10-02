# Module 04: Enumeration

> **Exam:** 312-50 | **Phase in Hacking:** Phase 1 (extended active reconnaissance)
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Enumeration Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-enumeration-concepts)
2. [NetBIOS Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-netbios-enumeration)
3. [SNMP Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-snmp-enumeration)
4. [LDAP Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-ldap-enumeration)
5. [NTP Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-ntp-enumeration)
6. [NFS Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-nfs-enumeration)
7. [SMTP Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-smtp-enumeration)
8. [DNS Enumeration](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#8-dns-enumeration)
9. [Other Enumeration Techniques (IPsec, VoIP, RPC, Unix/Linux, SMB)](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#9-other-enumeration-techniques)
10. [Enumeration Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#10-enumeration-countermeasures)
11. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#11-quick-exam-cheat-sheet)

---

## 1. Enumeration Concepts

### 🎯 What is Enumeration?

> **Enumeration** = process of **extracting** usernames, machine names, network resources, shares, and services from a system or network by creating **active connections** and sending **directed queries**.
> 
- Attacker uses collected info to identify **vulnerabilities** in system security
- Enables the attacker to perform **password attacks** to gain unauthorized access
- Enumeration techniques are conducted in an **intranet environment** (requires connectivity to target)
- ⚠️ Enumeration activities may be **illegal** depending on org policy and law — always get authorization

### 📦 Information Enumerated by Intruders

- Network resources & network shares
- Routing tables
- Audit and service settings
- SNMP and FQDN details
- Machine names
- Users and groups
- Applications and banners

> 💡 Attackers may find a remote **IPC$ share** (Windows) — probe it further via brute-forcing admin credentials to obtain a full file-system listing.
> 

---

### 🛠️ Techniques for Enumeration

| Technique | Description |
| --- | --- |
| **Extract usernames using email IDs** | Email format = `username@domainname` |
| **Extract info using default passwords** | Many online resources list manufacturer default passwords |
| **Brute force Active Directory** | AD design flaw — "logon hours" feature causes different error messages during auth, allowing username enumeration |
| **Extract info using DNS Zone Transfer** | If DNS server misconfigured, zone transfer reveals named hosts, sub-zones, IPs (via `nslookup`/`dig`) |
| **Extract user groups from Windows** | Attacker needs valid AD user ID; extract group membership via Windows interface or CLI |
| **Extract usernames using SNMP** | Guess read-only/read-write community strings via SNMP API |
| **Extract network resources/topology using SNMP** | Query SNMP tree methodically |

---

### 🔌 Key Services and Ports to Enumerate

| Port | Protocol | Service |
| --- | --- | --- |
| **TCP/UDP 53** | DNS | Zone Transfer |
| **TCP/UDP 135** | Microsoft RPC Endpoint Mapper |  |
| **UDP 137** | NetBIOS Name Service (NBNS / WINS) |  |
| **UDP 138** | NetBIOS Datagram Service |  |
| **TCP 139** | NetBIOS Session Service (most well-known Windows port) |  |
| **TCP/UDP 445** | SMB over TCP (Direct Host — no NetBIOS needed) |  |
| **UDP 161** | SNMP (agent listens here) |  |
| **TCP/UDP 162** | SNMP Trap |  |
| **TCP/UDP 389** | LDAP |  |
| **TCP 2049** | NFS |  |
| **TCP 25** | SMTP |  |
| **TCP/UDP 3268** | Global Catalog Service |  |
| **TCP/UDP 5060, 5061** | SIP (VoIP) — 5060 unencrypted, 5061 TLS-encrypted |  |
| **TCP 20/21** | FTP (data/control) |  |
| **TCP 23** | Telnet (cleartext credentials!) |  |
| **UDP 69** | TFTP |  |
| **TCP 179** | BGP |  |
| **UDP 500** | ISAKMP/IKE (IPsec) |  |
| **TCP 22** | SSH / SFTP |  |
| **UDP 123** | NTP |  |

> 💡 **Malware note:** ADM worm and Bonk Trojan exploit port 53 (DNS) vulnerabilities.
> 

**TCP vs UDP:**

- **TCP** = connection-oriented; supports ACK sliding window, retransmission, multiplexing, QoS, flow control
- **UDP** = connectionless; used for audio streaming, video/teleconferencing — unreliable but fast

**SMTP Commands (Table):**

| Command | Syntax |
| --- | --- |
| Hello | `HELO <sending-host>` |
| From | `MAIL FROM:<from-address>` |
| Recipient | `RCPT TO:<to-address>` |
| Data | `DATA` |
| Reset | `RESET` |
| Verify | `VRFY <string>` |
| Expand | `EXPN <string>` |
| Help | `HELP [string]` |
| Quit | `QUIT` |

---

## 2. NetBIOS Enumeration

### 🎯 What is NetBIOS?

> **NetBIOS name** = unique **16-character ASCII string** identifying a network device over TCP/IP. 15 characters = device name; **16th character** = service/record type.
> 
- NetBIOS was originally an API for LAN client software
- Windows uses NetBIOS for file and printer sharing
- Uses **UDP 137** (name services), **UDP 138** (datagram services), **TCP 139** (session services)
- ⚠️ **Note:** NetBIOS name resolution is **NOT supported by Microsoft for IPv6**

### 📋 NetBIOS Name List (CRITICAL — Memorize)

| Name | NetBIOS Code | Type | Information Obtained |
| --- | --- | --- | --- |
| `<host name>` | `<00>` | UNIQUE | Hostname |
| `<domain>` | `<00>` | GROUP | Domain name |
| `<host name>` | `<03>` | UNIQUE | Messenger service running for the computer |
| `<username>` | `<03>` | UNIQUE | Messenger service running for the logged-in user |
| `<host name>` | `<20>` | UNIQUE | Server service running |
| `<domain>` | `<1D>` | GROUP | Master browser name for the subnet |
| `<domain>` | `<1B>` | UNIQUE | Domain master browser name — identifies PDC |
| `<domain>` | `<1E>` | GROUP | Browser service elections |

### 🎯 What Attackers Obtain via NetBIOS Enumeration

- List of computers that belong to a domain
- List of shares on individual hosts in the network
- Policies and passwords

> An attacker who finds Windows with port 139 open can check for accessible/viewable resources on the remote system, provided file/printer sharing is enabled. This may allow reading/writing to a remote system, or launching a DoS attack.
> 

---

### 🛠️ Nbtstat Utility

> Source: https://learn.microsoft.com — Windows utility for troubleshooting NetBIOS name resolution.
> 

**Syntax:**

```
nbtstat [-a <remotename>] [-A <IPaddress>] [-c] [-n] [-r] [-R] [-RR] [-s] [-S] [<interval>][-?]
```

| Parameter | Function |
| --- | --- |
| `-a <remotename>` | Displays NetBIOS name table of remote computer by NetBIOS name |
| `-A <IPaddress>` | Displays NetBIOS name table of remote computer by IP address |
| `-c` | Lists contents of the NetBIOS name cache |
| `-n` | Displays names registered locally by NetBIOS applications |
| `-r` | Displays count of names resolved by broadcast or WINS |
| `-R` | Purges name cache and reloads #PRE-tagged entries from Lmhosts |
| `-RR` | Releases and re-registers all names with the name server |
| `-s` | Lists NetBIOS sessions, converting destination IPs to NetBIOS names |
| `-S` | Lists current NetBIOS sessions and status with IP addresses |
| `<interval>` | Re-displays stats, pausing for specified seconds |
| `-?` | Displays help |

**Example:**

```bash
nbtstat -a 10.10.1.11
```

Output shows: Name, Type, Status (e.g., `WINDOWS11 <00> UNIQUE Registered`, `WORKGROUP <00> GROUP Registered`)

---

### 🛠️ NetBIOS Enumeration Tools

- **NetBIOS Enumerator** — GUI tool; specify IP range → get NetBIOS names, usernames, domain names, MAC addresses
- **Nmap** — NSE script: `nmap -sV -v --script nbstat.nse <target IP>`
- **Global Network Inventory** (magnetosoft.com)
- **Advanced IP Scanner**
- **Hyena** (systemtools.com)
- **Nsauditor Network Security Auditor**

**AI-assisted:** `nbtscan -A 10.10.1.11` (via ChatGPT prompt "Perform NetBIOS enumeration on target IP")

---

### 🛠️ Enumerating Shared Resources — Net View

```bash
net view \\<computername>              # Shows resources of specific computer
net view \\<computername> /ALL         # Shows ALL shares including hidden
net view /domain                       # Shows all shares in the domain
net view /domain:<domain name>         # Shows shares on specified domain
```

---

### 🛠️ Enumerating User Accounts — PsTools Suite

> Source: https://learn.microsoft.com — Sysinternals suite for remote system management via CLI.
> 

| Tool | Function | Syntax |
| --- | --- | --- |
| **PsExec** | Lightweight Telnet replacement; executes processes on remote systems with full interactivity | `psexec [\\computer[,computer2[,...]] | @file]] [-u user][-p passwd]...` |
| **PsFile** | Shows list of remotely opened files; can close by name or ID | `psfile [\\RemoteComputer [-u Username [-p Password]]] [[Id | path] [-c]]` |
| **PsGetSid** | Translates SIDs to display names and vice versa (works on built-in, domain, local accounts) | `psgetsid [\\computer[,computer[,...]] | @file] [-u username] [-p password]] [account|SID]` |
| **PsKill** | Kill utility for processes on remote/local systems (by ID or name) | `pskill [-] [-t] [\\computer [-u username] [-p password]] <process name | process id>` |
| **PsInfo** | Gathers key system info: install type, kernel build, org/owner, CPU count/type, RAM, install date | `psinfo [[\\computer[,computer[,..]] | @file [-u user [-p psswd]]] [-h][-s][-d][-c[-t delimiter]] [filter]` |
| **PsList** | Displays CPU/memory info or thread statistics | — |
| **PsLoggedOn** | Displays locally logged-in users AND users logged in via resources; scans HKEY_USERS | — |

---

## 3. SNMP Enumeration

### 🎯 What is SNMP?

> **SNMP** = application-layer protocol running on **UDP** that maintains/manages routers, hubs, and switches on an IP network.
> 
- SNMP agents run on Windows and Unix networking devices
- SNMP enumeration = creating a list of user accounts and devices on target using SNMP
- Two components: **SNMP agent** (on device) and **SNMP management station** (communicates with agent)

### 🔑 SNMP Passwords (Community Strings)

| Type | Description |
| --- | --- |
| **Read Community String** | Views device/system config; strings are **public** |
| **Read/Write Community String** | Can change/edit device config; strings are **private** |

> ⚠️ Default community strings left unchanged = huge attack vector. Attackers extract: hosts, routers, devices, shares, ARP tables, routing tables, device-specific info, traffic statistics.
> 

---

### 🔄 How SNMP Works (4 Phases)

1. **Initialization** — Agent boots up, listens on UDP port 161 (default)
2. **Discovery** — Manager discovers SNMP-enabled devices via broadcast/specific IPs
3. **Information Exchange** — via **PDUs** (Protocol Data Units):
    - **Get Request** — retrieve value of specific variable
    - **GetNext Request** — fetch next variable in MIB tree (sequence query)
    - **Set Request** — modify a variable's value
    - **GetBulk Request** (SNMPv2+) — retrieve large volumes in one request
    - **Response** — agent's reply to Get/GetNext/Set/GetBulk
    - **Inform Request** — unsolicited info from agent to manager (manager-to-manager too)
    - **Trap** — unsolicited alert about significant events (e.g., reboot, link failure)
4. **Monitoring and Management** — Manager polls (Get Requests) and listens for Traps/Informs

> 💡 **SNMPv3** introduced "Notifications" (encompasses Traps + Informs) with authentication and encryption.
> 

---

### 🗄️ Management Information Base (MIB)

> **MIB** = virtual database — formal description of all network objects SNMP manages. Hierarchically organized; elements identified by **Object Identifiers (OIDs)**.
> 
- OID = numeric name given to an object, starts at MIB tree root
- MIB-managed objects: **scalar** (single instance) and **tabular** (group of related instances)
- OIDs include: object type (counter/string/address), access level (read/read-write), size, range

**Major MIBs:**

| MIB | Purpose |
| --- | --- |
| **DHCP.MIB** | Monitors DHCP traffic |
| **HOSTMIB.MIB** | Monitors/manages host resources |
| **LNMIB2.MIB** | Object types for workstation/server services |
| **MIB_II.MIB** | Manages TCP/IP-based Internet |
| **WINS.MIB** | For Windows Internet Name Service |

---

### 🛠️ SNMP Enumeration Tools

**SnmpWalk:**

```bash
snmpwalk -v1 -c public <Target IP Address>
snmpwalk -v2c -c public <Target IP Address>
snmpwalk -v2c -c public <Target IP Address> hrSWInstalledName  # search installed software
```

- Scans numerous SNMP nodes instantly; identifies variables via **OID**
- Reveals: server used, user credentials, other parameters in transit

**Other tools:** OpUtils (manageengine.com), Network Performance Monitor (solarwinds.com)

**Nmap SNMP scripts:**

```bash
nmap -sU -p 161 --script snmp-info <target>
nmap -sU -p 161 --script snmp-processes <target>
```

---

## 4. LDAP Enumeration

### 🎯 What is LDAP?

> **LDAP (Lightweight Directory Access Protocol)** = Internet protocol for accessing distributed directory services (hierarchical/logical, like a company org chart).
> 
- Client starts LDAP session by connecting to a **Directory System Agent (DSA)**, typically on **TCP port 389**
- Sends operation request to DSA
- Uses **Basic Encoding Rules (BER)** format for client-server transmission
- Uses DNS for quick lookups

> An attacker can **anonymously query** LDAP for sensitive info: usernames, addresses, departmental details, server names — used to launch attacks.
> 

---

### 🛠️ LDAP Enumeration Commands (ldapsearch)

```bash
# Get naming contexts
ldapsearch -h <Target IP Address> -x -s base namingcontexts

# Get info about primary domain (once DC=htb,DC=local identified)
ldapsearch -h <Target IP Address> -x -b "DC=htb,DC=local"

# Retrieve objects of a specific class
ldapsearch -h <Target IP Address> -x -b "DC=htb,DC=local" '(objectClass=Employee)'

# Retrieve ALL objects in directory tree
ldapsearch -x -h <Target IP Address> -b "DC=htb,DC=local" "objectclass=*"

# Retrieve list of users belonging to a particular object class
ldapsearch -h <Target IP Address> -x -b "DC=htb,DC=local" '(objectClass=Employee)' sAMAccountName sAMAccountType
```

**LDAP Enumeration Tools:**

- **Softerra LDAP Administrator** (idapadministrator.com)
- **ldapsearch** (linux.die.net)
- **AD Explorer** (docs.microsoft.com)
- **LDAP Admin Tool** (ldapsoft.com)

---

## 5. NTP Enumeration

### 🎯 What is NTP?

> **NTP** = synchronizes clocks of networked computers. Uses **UDP port 123**. Maintains time within 10ms error over the public Internet; ~200µs accuracy on LANs.
> 

**Information obtainable via NTP query:**

- List of hosts connected to the NTP server
- Clients' IP addresses, system names, OSes
- Internal IPs if NTP server is in the DMZ

---

### 🛠️ NTP Enumeration Commands

| Command | Purpose | Syntax |
| --- | --- | --- |
| **ntpdate** | Collects time samples from several sources | `ntpdate [-46bBdqsuv] [-a key] [-e authdelay] [-k keyfile] [-o version] [-p samples] [-t timeout] [-U user_name] server [...]` |
| **ntptrace** | Traces chain of NTP servers back to primary source | `ntptrace [-n] [-m maxhosts] [servername/IP_address]` |
| **ntpdc** | Queries ntpd daemon's current state, requests changes | `ntpdc [-46dilnps] [-c command] [hostname/IP_address]` |
| **ntpq** | Monitors ntpd operations, determines performance | `ntpq [-inp] [-c command] [host] [...]` |

**ntptrace example:**

```
# ntptrace
localhost: stratum 4, offset 0.0019529, synch distance 0.143235
10.10.0.1: stratum 2, offset 0.0114273, synch distance 0.115554
10.10.1.1: stratum 1, offset 0.0017698, synch distance 0.011193
```

**ntpdc parameters:**

| Flag | Function |
| --- | --- |
| `-4` / `-6` | Force IPv4/IPv6 DNS resolution |
| `-d` | Debugging mode |
| `-c` | Interactive format command |
| `-i` | Interactive mode |
| `-l` | List of peers (`-c listpeers`) |
| `-n` | Dotted-quad numeric format |
| `-p` | List peers + state summary (`-c peers`) |
| `-s` | List peers + state summary, different format (`-c dmpeers`) |

---

## 6. NFS Enumeration

### 🎯 What is NFS?

> **NFS (Network File System)** = enables users to access, view, store, update files over a remote server as if mounted locally. Uses **TCP port 2049**. RPC routes/processes requests between client and server.
> 
- **Exporting** = process of sharing files/directories over network
- **Mounting** = client makes file available for sharing
- `/etc/exports` on NFS server = list of clients allowed to share files
- Only credential used = **client's IP address** (weak!)
- NFS versions before v4 share the same weak security spec

### 🛠️ NFS Enumeration Commands

```bash
# Scan for open NFS port (2049) and RPC services
rpcinfo -p <Target IP Address>

# View list of shared files/directories
showmount -e <Target IP Address>
```

**Example rpcinfo output:**

```
program vers proto  port  service
100003    3   tcp   2049  nfs
100003    4   tcp   2049  nfs
```

**Example showmount output:**

```
Export list for 10.10.1.9:
/home *
```

**NFS Enumeration Tools:** RPCScan, SuperEnum

---

## 7. SMTP Enumeration

### 🎯 What is SMTP?

> **SMTP** = TCP/IP mail delivery protocol. Runs on TCP ports **25, 2525, or 587**. Uses MX servers to direct mail via DNS. Common with POP3 and IMAP.
> 

### 🔑 3 Built-in SMTP Commands (CRITICAL for exam)

| Command | Function |
| --- | --- |
| **VRFY** | Validates users |
| **EXPN** | Displays actual delivery addresses of aliases and mailing lists |
| **RCPT TO** | Defines the recipients of a message |

> SMTP servers respond **differently** to VRFY, EXPN, RCPT TO for valid vs invalid users → attacker determines valid users on the server.
> 

**Example — VRFY:**

```
$ telnet 192.168.168.1 25
220 NYmailserver ESMTP Sendmail 8.9.3
HELO x
250 NYmailserver Hello [10.0.0.86], pleased to meet you
VRFY Jonathan
250 Super-User <Jonathan@NYmailserver>
VRFY Smith
550 Smith... User unknown
```

**Example — EXPN:**

```
EXPN Jonathan
250 Super-User <Jonathan@NYmailserver>
EXPN Smith
550 Smith... User unknown
```

**Example — RCPT TO:**

```
MAIL FROM:Jonathan
250 Jonathan... Sender ok
RCPT TO:Ryder
250 Ryder... Recipient ok
RCPT TO: Smith
550 Smith... User unknown
```

---

### 🛠️ SMTP Enumeration Tools

- **NetScanTools Pro** — SMTP Email Generator tests sending, extracts header params, logs sessions
- **smtp-user-enum** (pentestmonkey.net) — enumerates OS-level users via VRFY/EXPN/RCPT TO responses
- **Nmap NSE script:**

```bash
nmap -p25 --script smtp-enum-users --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} <target> -oN output.txt
```

- **Metasploit:**

```bash
msfconsole -q -x "use auxiliary/scanner/smtp/smtp_enum; set RHOSTS <target>; run; exit"
```

---

## 8. DNS Enumeration

### 🔄 DNS Zone Transfer

> **DNS Zone Transfer** = transferring a copy of DNS zone file from primary → secondary DNS server (for redundancy/backup). If misconfigured, attacker can perform zone transfer to obtain: DNS server names, hostnames, machine names, usernames, IP addresses, aliases.
> 

The attacker sends a zone-transfer request pretending to be a client; if allowed, DNS server sends a portion of its database as a zone.

### 🛠️ DNS Zone Transfer Commands

**Linux (dig):**

```bash
dig ns <target domain>                              # Get all name servers
dig @<name server> <target domain> axfr             # Attempt zone transfer
```

**Windows (nslookup):**

```
nslookup
set querytype=any
server <target DNS server>
ls -d <domain>
```

**Example:**

```bash
dig ns www.certifiedhacker.com
dig @ns1.bluehost.com www.certifiedhacker.com axfr
# Result: "Transfer failed" (properly configured) OR full zone dump (vulnerable)
```

---

### 🛠️ DNS/DNSSEC Enumeration Tools

**Nmap DNS enumeration:**

```bash
nmap --script=broadcast-dns-service-discovery <Target Domain>   # List services
nmap -T4 -p 53 --script dns-brute <target domain>                # Brute-force subdomains
nmap -Pn -sU -p 53 --script=dns-recursion <target>                # Check DNS recursion enabled
```

**DNSSEC Enumeration (NSEC/NSEC3 zone walking):**

```bash
nmap -sU -p 53 --script dns-nsec-enum --script-args dns-nsec-enum.domains=<domain> <target>
```

**DNSSEC Zone Walking Tools:**

| Tool | Command |
| --- | --- |
| **LDNS (ldns-walk)** | `ldns-walk @<IP of DNS Server> <Target domain>` |
| **DNSRecon** | `dnsrecon -d <target domain> -z` |

**DNS Enumeration Tools:** Knock, Raccoon, Subfinder, Turbolist3r

**DNS Cache Snooping (via dig):**

```bash
dig @<DNS server IP> <target domain> +recurse      # recursive method
dig @<DNS server IP> <target domain> +norecurse    # non-recursive method
```

---

## 9. Other Enumeration Techniques

### 🔒 IPsec Enumeration

> Most IPsec-based VPNs use **ISAKMP** (part of IKE) to establish/negotiate/modify/delete Security Associations (SA) and cryptographic keys. IPsec components: **ESP** (Encapsulating Security Payload), **AH** (Authentication Header), **IKE** (Internet Key Exchange).
> 

```bash
nmap -sU -p 500 <target IP address>                          # Check ISAKMP presence
nmap -sU -p 500 --script=ike-version <target IP address>     # Detect IKE version
ike-scan <target>                                              # Dedicated IPsec scanning tool
```

- Reveals: encryption/hashing algorithm, authentication type, key distribution algorithm, SA LifeDuration

---

### 📞 VoIP Enumeration

> VoIP uses **Session Initiation Protocol (SIP)** for voice/video calls over IP. SIP typically uses **UDP/TCP ports 2000, 2001, 5060, and 5061**.
> 

**Information obtained:** VoIP gateway/servers, IP-PBX systems, client software (softphones)/VoIP phones, User-Agent IP addresses, user extensions.

**Attacks enabled:** DoS, session hijacking, caller ID spoofing, eavesdropping, SPIT (Spam over Internet Telephony), Vishing (VoIP phishing).

**Tools:**

- **Svmap** (github.com) — open-source scanner identifying SIP devices/PBX servers; scans large network ranges, default and non-default ports
- **Metasploit** — `auxiliary/scanner/sip/*` modules for SIP enumeration/options scanning

---

### 🔗 RPC Enumeration

> **RPC (Remote Procedure Call)** = technology for creating distributed client/server programs. Components: client, server, endpoint, endpoint mapper, client stub, server stub.
> 
- **Portmapper service** listens on **TCP/UDP port 111** to detect endpoints
- In firewalled networks, portmapper is often filtered → attackers scan wide port ranges

```bash
nmap -sR <target IP/network>              # RPC scan
nmap -T4 -A <target IP/network>           # Aggressive scan revealing RPC/NetBIOS/LDAP info
```

**Tools:** NetScanTools Pro RPC Info (detects portmapper daemon on port 111 of Unix/Linux)

---

### 🐧 Unix/Linux User Enumeration

| Command | Function | Syntax |
| --- | --- | --- |
| **rusers** | Lists users logged in to remote/local network machines | `/usr/bin/rusers [-a] [-l] [-u] [-h] [-i] [Host ...]` |
| **rwho** | Lists users logged in to hosts on local network | `rwho [-a]` |
| **finger** | Shows system user info: login name, real name, terminal, idle time, login time, office location/phone | `finger [-l] [-m] [-p] [-s] [user ...] [user@host ...]` |

**rusers options:**

- `a` — report for machine even if no users logged in
- `h` — sort alphabetically by hostname
- `l` — longer listing (like `who`)
- `u` — sort by number of users
- `i` — sort by idle time

**finger example:**

```bash
$ finger
Login    Name    Tty    Idle   Login Time   Office   Office Phone
ubuntu   Ubuntu  *:1           Mar 12 03:23 (:1)

$ finger ubuntu
Login: ubuntu          Name: Ubuntu
Directory: /home/ubuntu    Shell: /bin/bash
On since Tue Mar 12 03:23 (EDT) on :1 from :1 (messages off)
```

---

### 🗂️ SMB Enumeration

> **SMB (Server Message Block)** runs over **TCP 445** (direct host) or **TCP 139** (via NetBIOS). Used for Windows file/printer sharing.
> 

```bash
nmap -p 445 -A <Target IP>                                   # Full detail: OS, SMB version, security mode
nmap -p 445 --script smb-protocols <Target IP>               # List SMB dialects supported
nmap -p 139 --script smb-protocols <Target IP>
nmap -p 445 --script smb-enum-shares <Target IP>             # Enumerate shares
nmap -p 445 --script smb-enum-users <Target IP>              # Enumerate users
nmap -p 445 --script smb-os-discovery <Target IP>            # OS discovery via SMB
```

**Example output includes:** OS version, computer name, NetBIOS name, domain, workgroup, message signing status, shares (ADMIN$, C$, IPC$, NETLOGON), users with RIDs and flags (password expiry, account disabled, etc.)

---

## 10. Enumeration Countermeasures

### 🔒 SNMP Enumeration Countermeasures

- Remove the SNMP agent or turn off the SNMP service
- If disabling isn't possible, **change default community string names**
- **Upgrade to SNMPv3** — encrypts passwords and messages
- Implement Group Policy: **"Additional restrictions for anonymous connections"**
- Restrict null session pipes, null session shares, and IPsec filtering
- **Block access to TCP/UDP port 161**
- Don't install management/monitoring Windows component unless required
- Encrypt or authenticate using IPsec
- Don't misconfigure SNMP with read-write authorization
- Configure ACLs for all SNMP connections
- Encrypt credentials using **"AuthNoPriv"** mode (MD5/SHA)
- Avoid **"NoAuthNoPriv"** mode (no encryption)
- Implement RBAC policies to SNMP communities/users
- For SNMPv1/v2c: change default "public"/"private" strings to complex unique values
- Keep SNMP management traffic on separate/secure VLAN
- Regularly audit network traffic and SNMP access logs

### 🔒 LDAP Enumeration Countermeasures

- By default, LDAP traffic is **unsecured** — use **SSL or STARTTLS** to encrypt
- Select username **different from email address**; enable account lockout
- Use **NTLM, Kerberos**, or basic auth mechanism to limit access
- Log access to Active Directory (AD) services
- Deploy **canary accounts** to mislead attackers
- Create **decoy admin groups** to mislead attackers
- Enable **multi-factor authentication (MFA)**
- **Disable anonymous binds** unless absolutely necessary
- Configure ACLs; restrict LDAP traffic via firewalls to authorized systems only

### 🔒 NFS Enumeration Countermeasures

- Implement proper permissions (read/write restricted to specific users)
- Implement firewall rules to **block NFS port 2049**
- Proper configuration of `/etc/smb.conf`, `/etc/exports`, `/etc/hosts.allow`
- Keep **`root_squash`** option ON in `/etc/exports` (untrust root requests from client)
- Implement NFS tunneling through SSH to encrypt traffic
- Ensure users aren't running **suid/sgid** on exported filesystem
- Migrate to **NFSv4** (supports Kerberos encryption/authentication)
- Use deep packet inspection (DPI) firewall for NFS traffic

### 🔒 SMTP Enumeration Countermeasures

- Ignore email messages to unknown recipients
- Exclude sensitive mail server/local host info in mail responses
- **Disable open relay** feature
- Limit accepted connections per source (prevent brute-force)
- **Disable EXPN, VRFY, RCPT TO** commands or restrict to authenticated users
- Implement **SPF, DKIM, DMARC**
- Provide limited info in error messages
- Use TLS to encrypt SMTP communication
- Use ACLs to restrict SMTP commands to authorized users/IPs

### 🔒 SMB Enumeration Countermeasures

- **Disable SMB protocol on Web and DNS servers**
- Disable SMB protocol on Internet-facing servers
- **Disable ports TCP 139 and TCP 445** (also UDP 137/138)
- Restrict anonymous access via **`RestrictNullSessAccess`** registry parameter:
(1 = enabled/restricted, 0 = disabled)
    
    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters
    ```
    
- Use SMBv3 or higher — **avoid SMBv1** (outdated/vulnerable)
- Configure ACLs to restrict SMB share access
- Enable Windows Firewall/endpoint protection
- Implement digitally signed data transmission

### 🔒 DNS Enumeration Countermeasures

- **Restrict resolver access** to hosts inside the network only
- **Randomize source ports** (not just UDP 53) — defend against cache poisoning
- Audit DNS zones for vulnerabilities
- Patch nameservers (BIND, Microsoft DNS) regularly
- **Restrict DNS zone transfers** to specific slave nameserver IPs
- Use different servers for authoritative vs. resolving functions
- Use isolated dedicated DNS servers (not combined with app servers)
- **Disable DNS recursion** to mitigate amplification/poisoning attacks
- Harden OS — close unused ports, block unnecessary services
- Implement **DNSSEC** for digitally signed DNS requests (mitigates DNS hijacking)
- Use DNS change lock / two-factor authentication for DNS management

---

## 11. Quick Exam Cheat Sheet

### 🔑 Ports to Memorize

```
53    = DNS (zone transfer)
111   = RPC portmapper
123   = NTP
135   = MS RPC Endpoint Mapper
137   = NetBIOS Name Service (UDP)
138   = NetBIOS Datagram Service (UDP)
139   = NetBIOS Session Service (TCP)
161   = SNMP agent
162   = SNMP Trap
389   = LDAP
445   = SMB (direct host, no NetBIOS)
500   = ISAKMP/IKE (IPsec)
2049  = NFS
2000/2001/5060/5061 = SIP (VoIP)
25/2525/587 = SMTP
3268  = Global Catalog
```

---

### 📋 NetBIOS Codes — Quick Reference

```
<00> UNIQUE = Hostname
<00> GROUP  = Domain name
<03> UNIQUE = Messenger service
<20> UNIQUE = Server service running
<1D> GROUP  = Master browser (subnet)
<1B> UNIQUE = Domain master browser (PDC)
<1E> GROUP  = Browser service elections
```

---

### 🛠️ Command Reference by Service

| Service | Command |
| --- | --- |
| NetBIOS | `nbtstat -a <IP>`, `net view \\<computer> /ALL` |
| SNMP | `snmpwalk -v1 -c public <IP>` |
| LDAP | `ldapsearch -h <IP> -x -s base namingcontexts` |
| NTP | `ntptrace`, `ntpdc`, `ntpq`, `ntpdate` |
| NFS | `rpcinfo -p <IP>`, `showmount -e <IP>` |
| SMTP | `telnet <IP> 25` then `VRFY`/`EXPN`/`RCPT TO` |
| DNS | `dig ns <domain>`, `dig @<NS> <domain> axfr` |
| IPsec | `nmap -sU -p 500 <IP>`, `ike-scan <IP>` |
| RPC | `nmap -sR <IP>`, port 111 |
| Unix/Linux | `rusers`, `rwho`, `finger` |
| SMB | `nmap -p 445 --script smb-enum-shares <IP>` |

---

### 🔥 Common Exam Scenarios

**Q: What are the 3 SMTP commands used for user enumeration?**
→ **VRFY, EXPN, RCPT TO**

**Q: What port does NetBIOS Session Service use?**
→ **TCP 139**

**Q: What NetBIOS code identifies the PDC?**
→ **`<1B>` UNIQUE** (Domain master browser)

**Q: What's the default SNMP agent port?**
→ **UDP 161** (Trap = UDP 162)

**Q: What are the two SNMP community string types?**
→ **Read (public)** and **Read/Write (private)**

**Q: What port does LDAP use by default?**
→ **TCP/UDP 389** (Global Catalog = 3268)

**Q: What command retrieves NFS shared directories?**
→ **`showmount -e <target>`**

**Q: What port does NFS use?**
→ **TCP 2049**

**Q: What tool performs DNS zone transfer testing on Linux?**
→ **`dig @<nameserver> <domain> axfr`**

**Q: What Windows registry parameter restricts null session access?**
→ **`RestrictNullSessAccess`**

**Q: What SIP ports are used for VoIP?**
→ **UDP/TCP 2000, 2001, 5060 (unencrypted), 5061 (TLS)**

**Q: What port does the RPC portmapper use?**
→ **TCP/UDP 111**

**Q: What Unix command shows detailed user login info?**
→ **`finger`**

**Q: What's the safest SMB version to use?**
→ **SMBv3 or higher** (avoid SMBv1)

**Q: What protocol does IPsec use for key exchange, and what port?**
→ **ISAKMP/IKE, UDP port 500**

**Q: What Windows utility troubleshoots NetBIOS name resolution?**
→ **`nbtstat`**

**Q: What AD design flaw enables username enumeration?**
→ The **"logon hours"** feature causing different error messages during service auth

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 04*