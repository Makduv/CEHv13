# Chapter 3 - Security Foundations

# The Triad

Three properties that define what security *is*: **Confidentiality, Integrity, Availability** — the **CIA triad**. Attackers work to compromise one of them.

## Confidentiality

Making sure nobody gets unauthorized access to information. Main tool: **encryption**.

Two states of data:

- **Static / at rest** — not moving, sitting on a disk, not being used or manipulated (full-disk encryption, database encryption)
- **Dynamic / in motion** — being sent from one place to another (**SSL/TLS** for web-based communication; SSL came first, TLS replaced it)
- (Third one worth knowing: **data in use** — in memory, hardest to protect)

Encrypted data can't be read without the key, so confidentiality is preserved. But if the attacker gets the key, they can decrypt it — and once decrypted data has been seen by someone without permission, confidentiality is **compromised**.

Note: encryption isn't the only mechanism — access control, permissions and authentication also serve confidentiality.

## Integrity

The data is the same at the moment it's received as it was when it was sent. It can be compromised in several ways:

- **Corruption** — in transit, on disk, or in memory (accidental, not always malicious)
- **On-path attack** (MITM) — the attacker intercepts data in transit, alters it, and sends it on its way

Integrity isn't only about the *contents* of the data — it also covers the integrity of the **source** of the information.

Mechanisms: **hashing** (MD5, SHA-256) to detect changes, **HMAC** and **digital signatures** to prove both integrity and origin.

## Availability

Information or services are available to users when they're expected to be.

- **Misconfiguration** can cause availability problems — so can hardware failure or human error.
- Malicious: **Denial of Service (DoS)** — overwhelming the service with a huge volume of requests so it can't respond, making it unavailable. **DDoS** = the same from many sources at once.
- Defences: redundancy, backups, load balancing, failover, rate limiting.

Reminder: availability is a security property too — taking a system offline "to be safe" is itself a security failure.

## Parkerian Hexad

In 1998, **Donn Parker** extended the triad with three more properties. Not a standard — but it comes up in the exam.

| Property | Meaning |
| --- | --- |
| **Possession (or Control)** | Holding the data, separate from being able to read it. A stolen encrypted backup tape = possession lost, confidentiality intact. Explains breaches where data is taken but not readable |
| **Authenticity** | The source of the data or document is what it claims to be — related to **non-repudiation**: the sender can't later deny having sent it. Example: an email with a **digital signature** |
| **Utility** | The data is actually *useful* in its current form. Encrypted data with a lost key is still confidential, intact and available — but useless. Availability without utility isn't worth much |

Full hexad: Confidentiality, Integrity, Availability, Possession, Authenticity, Utility.

# Information Assurance and Risk

## The risk equation

**Risk = Loss × Probability** → expressed as a value in dollars.

Dictionary definition: *the exposure to chance of injury or loss*. "Chance" = probability = something **measurable**.

- **Probability** = number of events / number of outcomes. Easy for dice, hard for information security — we rarely have reliable data on how often a given attack succeeds.
- **Loss** = the value of what's lost if the event happens: the asset itself, downtime, recovery costs, fines, lost business, reputation. Also hard to pin down precisely.
- **Catastrophizing** = the trap of assuming the worst-case scenario for everything. If every risk is treated as maximum loss and maximum probability, the numbers stop meaning anything and you can't prioritise.

The point of risk is to **identify what actually matters**, so that limited resources go to the right places.

Formal version you'll see: **SLE** (Single Loss Expectancy) = asset value × exposure factor; **ALE** (Annualized Loss Expectancy) = SLE × ARO (annual rate of occurrence). The ALE is what justifies the spend on a control.

## Vocabulary

| Term | Definition |
| --- | --- |
| **Threat** | Something with the possibility to cause a breach of confidentiality, integrity or availability |
| **Vulnerability** | A weakness in the system |
| **Exploit** | What triggers the vulnerability |
| **Threat agent / actor** | The entity (person or group) that can instantiate a threat |
| **Threat vector** | The pathway the threat agent takes to exploit a vulnerability |

Not every vulnerability can actually be exploited — a **race condition** is the classic example: a programmatic situation where one process writes data while another reads it (a synchronisation problem). It's a real flaw, but exploiting it requires hitting a timing window you often can't control.

## Information Assurance

**Information assurance** = the practice of ensuring the CIA properties of information and managing the risk to it — broader than technical security, it includes policy, process, people and business decisions.

### Controls by function

| Function | Purpose |
| --- | --- |
| **Preventive** | Keep something bad from happening |
| **Detective** | Identify that a violation of C, I or A has occurred |
| **Corrective** | Reduce the damage after it has happened |

(Sometimes extended with: deterrent, compensating, recovery.)

### Controls by type

| Type | Examples |
| --- | --- |
| **Technical** | Hardware or software implemented to protect assets — antivirus, firewall, EDR, encryption |
| **Administrative** | Policies, procedures, guidelines giving direction on how protection should be implemented — also training and background checks |
| **Physical** | Keep people away from the asset — locked doors, cameras, badges, guards, fences |

The two axes combine: a locked door is physical + preventive, a camera is physical + detective, a backup is technical + corrective.

## Handling risk

Information assurance is about managing risk — making a **business decision** on how to handle it.

**Risk appetite** = the level of risk the business is comfortable managing.

| Strategy | Meaning |
| --- | --- |
| **Acceptance** | The business understands the risk, the impact and the probability, and chooses to do nothing. If it happens, the business carries all the losses and costs |
| **Transference** | Hand the risk to another entity — most commonly by buying **insurance**, or outsourcing to a provider (note: you can transfer the cost, not the accountability) |
| **Mitigation** | Implement controls that reduce the risk — reduction can apply to the **loss**, the **probability**, or both |
| **Avoidance** | Choose not to engage in the activity that introduces the risk at all |

**Residual risk** = what remains after the controls are in place. There's no zero risk — the goal is to get it inside the risk appetite, and have it formally accepted by the business.

# Policies, Standards and Procedures

The hierarchy: **Policies** → **Standards** are built on them → **Procedures** are where the work actually gets done. **Guidelines** sit alongside as optional advice.

## Security Policies

Defines what the company considers security to be:

- Which resources need to be protected
- How resources should be used properly
- How resources can or should be accessed
- Expectations of employees — what a user can and cannot do

Policies are the **lines management draws**. Their goals should be the confidentiality, integrity and availability of information resources.

They should be revisited regularly, but a well-written policy shouldn't need frequent changes — it's high-level and technology-agnostic.

**Key point:** a policy has no weight without management sponsorship. It's a management artefact, not a technical one.
**Examples:** acceptable use policy, password policy, data classification policy, remote access policy, BYOD policy.

## Security Standards

Direction on **how** policies should be implemented. Two meanings of the word:

1. **External frameworks** that provide guidance — NIST, ISO 27001 (the management system: how to run security) and ISO 27002 (the catalogue of controls). Also PCI DSS, CIS Benchmarks.
2. **Internal standards** — detailed guidance for implementing policies within the organization.

Example: a policy says *"all systems will be kept up to date."* The standards define what that means for desktops, servers, network devices, mobiles — each handled differently (patch windows, delays, exceptions).

Standards must be updated more often than policies: any change in information assets results in the standard being updated.

**Note:** standards are **mandatory**, unlike guidelines.

## Procedures

The implementation of the standards. They provide guidance on how the standard is achieved in a granular way — **step-by-step instructions** on what needs to be done.

They change even more often than standards, and are revisited regularly to be made more efficient.

**Examples:** how to onboard a user, how to apply a patch, the incident response runbook.

## Guidelines

No requirements — **suggestions** on how policies may be implemented. Optional, not enforceable. They typically provide information about **best practices**.

## Summary

|  | Level | Mandatory? | Changes |
| --- | --- | --- | --- |
| **Policy** | What and why | Yes | Rarely |
| **Standard** | What specifically | Yes | Regularly |
| **Procedure** | How, step by step | Yes | Often |
| **Guideline** | Suggested how | No | As needed |

# Organizing Your Protections

To evaluate how well an asset is protected, review what attackers are **most likely to do**. If we know the actions an attacker will take, we can build detection for them and be prepared.

## MITRE ATT&CK Framework

Created by the **MITRE Corporation in 2013** to collect the **Tactics, Techniques and Procedures (TTPs)** used by real attackers.

**Why it exists:** the Cyber Kill Chain was too high-level and didn't map well to real-world incidents. ATT&CK describes TTPs in enough detail that detection and protection controls can be built to block them.

**Structure:**

- **Tactic** = the attacker's goal at that stage (the columns below) — the *why*
- **Technique** = how the tactic is achieved (e.g. T1566 Phishing) — the *how*
- **Procedure** = the concrete implementation used by a specific group or malware

Matrices exist for Enterprise, Mobile and ICS. The TTPs are regularly updated as new behaviour is observed.

## The stages (tactics)

| Tactic | What the attacker does |
| --- | --- |
| **Reconnaissance** | Gathering information about the target — scanning, phishing for info, open sources and closed sources |
| **Resource Development** | Building the infrastructure they'll attack from — registering domains, creating accounts, acquiring systems and tools |
| **Initial Access** | Compromising the first system |
| **Execution** | Getting code to run — scheduled tasks, interprocess communication, system services, OS-specific techniques like Windows Management Instrumentation (WMI) |
| **Persistence** | Keeping consistent access. A reboot or logout kills the program, so they need a way to restart it — usually software installed on the system that executes at boot |
| **Privilege Escalation** | Getting the highest level of privilege they can |
| **Defense Evasion** | Getting past the defences in place — rootkits, disabling logging, obfuscation, living off the land |
| **Credential Access** | Obtaining credentials to reuse elsewhere — phishing, dumping credentials from memory (LSASS), keylogging |
| **Discovery** | Looking for additional systems and seeing what they can reach in the target environment |
| **Lateral Movement** | After discovery, compromising more systems — more systems means more data to steal or use |
| **Collection** | Gathering the information they want to steal, or that helps attack other systems. More visible, because the stolen data has to be staged somewhere — leaves artefacts behind |
| **Command and Control (C2)** | Maintaining remote control over the environment for as long as possible. Commonly embeds commands in **HTTP/HTTPS** traffic between their server and the system; can also use **DNS** or **ICMP** |
| **Exfiltration** | Getting the collected data out |
| **Impact** | Tearing down as much as they can — data destruction, disk wiping, encryption (ransomware), denial of service |

## What it's used for in practice

- Map existing detections to techniques → find the **gaps** in coverage
- Describe adversary behaviour in a shared vocabulary across teams
- Drive purple teaming and threat hunting hypotheses
- **ATT&CK Navigator** = the tool for visualising coverage on the matrix
- **D3FEND** = the defensive counterpart, mapping countermeasures to techniques

Key difference from the Kill Chain: ATT&CK is a **matrix, not a sequence**. An attacker picks techniques from several tactics, loops back, and doesn't necessarily go left to right.

# Security Technology

## Firewalls (FW)

Like a wall that keeps fire contained — it separates zones so a problem on one side doesn't spread to the other.

### Packet Filters

The most basic firewall. Routers and switches also offer filtering (ACLs).

Makes a decision on the disposition of a packet based on **protocol, port and addresses** — ports and addresses can be filtered on both source and destination. Layer 3/4 only, no notion of context.

Three possible dispositions:

| Action | Effect |
| --- | --- |
| **Drop** | Packet isn't forwarded, no message or notification sent back (looks like the host doesn't exist → "filtered" in nmap) |
| **Reject** | Packet isn't sent to the destination, but an **ICMP destination unreachable** is returned |
| **Accept** | Packet is forwarded |

Decisions come from a set of **rules / policy**. A blanket rule applies to everyone, with exceptions listed:

- **Default deny** — drops everything that isn't explicitly allowed through *(the secure choice)*
- **Default accept** — everything passes unless explicitly blocked

Rules are read **top to bottom, first match wins** — order matters.

### Stateful Filtering

Keeps track of the **state** of messages within a conversation between client and server. Requires a **state table**.

For TCP it's easier since the flags tell part of the story about the traffic flow — but not the whole story. A stateful firewall doesn't just look at flags and headers: it watches messages going in and out and records them, so it knows which end is the client and which end is the server.

**Consequence:** return traffic for a connection you initiated is allowed automatically, without an explicit rule. And a lone ACK or SYN-ACK with no matching entry in the state table gets dropped — which is what defeats some nmap scan types (ACK scan, FIN scan) against stateful firewalls.

### Deep Packet Inspection (DPI)

Looks into the **payload** of the packet, not just the headers.

- Requires **signatures** to look for
- Needs every fragment, so it has to wait for the message to be reassembled → adds a little latency
- Can inspect higher-level protocols
- **Encrypted traffic cannot be inspected.** Headers aren't encrypted (otherwise no intermediate device could route the packet), but the payload is → DPI does little to protect a web server where everything is HTTPS
- Workaround: **TLS/SSL interception** (break-and-inspect) — the firewall terminates the session, inspects, re-encrypts. Costly and raises privacy issues

### Application Layer Firewall

Inspects packets, but specific to a particular protocol — it understands the application semantics.

- **VoIP networks** → a **Session Border Controller (SBC)** is used
- **Web Application Firewall (WAF)** → uses a set of rules to detect and block requests and responses. **ModSecurity** is open source and can be used with Apache. Often deployed as a **reverse proxy**: the client sends the message to the proxy, which handles it on behalf of the server before forwarding it; responses pass back through the proxy too
- WAFs target the OWASP-style attacks a packet filter can't see: SQL injection, XSS, path traversal

### Unified Threat Management (UTM)

Users are the most vulnerable point (social engineering, malware), so they need protecting.

**UTM consolidates security functions at one point in the network** — would replace the firewall, IDS, IPS and antivirus with a single appliance.

- Advantage: simpler to manage, cheaper, one console
- Problem: a **single point** where all security is handled — single point of failure, and a performance bottleneck when all features are enabled
- Modern equivalent: **NGFW** (Next-Generation Firewall) — stateful + DPI + application awareness + user identity + IPS

## Intrusion Detection System (IDS)

Detects and alerts — it does **not** block.

- **HIDS (Host-based)** — watches activity on a local system: files, logs, processes, registry. If an attacker triggers a HIDS, it means they're already **on** the system
- **NIDS (Network-based)** — watches all traffic passing the network interface, applies rules and generates log messages

**Placement:**

- **In parallel (out-of-band)** — the normal way for an IDS: it gets a **copy** of the traffic via a SPAN/mirror port or a network TAP. It sees everything but can't stop anything; no impact on performance or availability
- **In series (inline)** — the traffic physically passes through the device. That's what makes prevention possible, but it becomes a bottleneck and a point of failure. This is really an IPS setup
- **Distributed sensors** — sensors placed at several points in the network (per segment, per VLAN, DMZ, datacenter) all reporting to a central console, so you get visibility beyond a single choke point

Rules can be based on **any layer** of the network stack. Open source: **Snort** (Cisco), also **Suricata** and **Zeek**.

**Detection methods:** signature-based (known patterns, misses novel attacks) vs anomaly/behaviour-based (baseline deviation, more false positives).

## Intrusion Prevention System (IPS)

To be an IPS, it **has to be placed in the path of traffic** entering and leaving the network (inline).

Being on the flow, it can act like a firewall — accepting or rejecting packets as they enter the network. It can **drop** the message (discard silently) or **reject** it with an appropriate message (e.g. ICMP destination unreachable).

**Difference between a firewall and an IPS:** IPS rules are **dynamic and temporary**, and the decision is made on the **content** of the packet, not just headers and ports.

**Practical points:**

- Even when it blocks attacks, the logs and alerts should still be investigated
- Rules are not infallible → false positives happen, and it can block legitimate traffic from customers or partners. That's why many rules are left in detect-only mode first
- The main challenge for IDS/IPS is the **volume of logs** produced — depends on how many rules you have, how well they're written, how much traffic there is, and where the sensors are placed

## Endpoint Detection and Response (EDR)

**EDR** = an agent installed on endpoints that continuously records what happens on the system (processes, network connections, file and registry changes), detects malicious behaviour, and gives responders the ability to investigate and act remotely. Beyond antivirus: AV blocks known files, EDR gives visibility and response on **behaviour**, and keeps the telemetry for investigation.

Functions it provides:

- **Antimalware** — signature + behaviour-based detection
- **Investigation** of logs on the system to detect malicious behaviour
- **Remote access** to the system from a central console
- **Pulling back artefacts** — process listings, memory dumps, files, logs
- **Isolation** — cut the machine off the network while keeping the console connection

Open source example: **GRR** (Google Rapid Response).
Related terms: **XDR** (extends the same idea across endpoint, network, mail and cloud), **MDR** (the same, run as a managed service).

## Security Information and Event Management (SIEM)

Logs help diagnose problems on systems and the network, and support investigation of potential issues. They're essential for **forensic investigators and incident responders**.

But logs consume **disk space and processing power**, and they're spread across hundreds of sources.

**SIEM** = log management solution (all your logs in one place) + **correlation** + analysis of security alerts + better visualisation of the data.

Key functions: collection, normalisation (making different log formats comparable), correlation rules, alerting, retention, dashboards and reporting for compliance.

A **SOC (Security Operations Center)** is built around monitoring systems, including a SIEM that can correlate data from different sources.

**Main advantage:** it brings a lot of data together to give a **broader picture** of what's happening — an event that looks harmless on one system becomes meaningful when correlated with events on three others.

**Main challenges:** cost (usually licensed by volume), tuning to keep false positives manageable, and the fact that a SIEM only sees what you actually send to it.

# Being Prepared

## Defense in Depth

A **layered** approach to network design, with multiple security gateways controlling access between layers.

**Objective:** delay the attacker, and ideally create **artifacts** along the way (logs, alerts, traces) so you can detect them. It doesn't stop a determined attacker — it buys time and visibility.

A better version of the idea: provide **redundant controls across multiple areas**, so no single failure exposes everything.

| Layer | What it covers |
| --- | --- |
| **Physical** | Making sure unauthorized people can't get physical access to systems — locks, badges, cameras, guards |
| **Technical** | Security technology (FW, SIEM, IDS, IPS, EDR), software, configuration controls like a password policy requiring complexity. Any hardware or software in place to prevent unauthorized use |
| **Administrative** | Policies, standards and procedures. Sets the tone for how the organization is protected — also training and awareness |

## Defense in Breadth

Covers what defense in depth misses. Depth stacks controls on the same predictable path; breadth widens the view.

- Looks at the **whole lifecycle** and the whole attack surface: software development, supply chain, third parties, cloud, mobile, users
- Focuses on **realistic risk** and on **people**, not only on stacking technical boxes
- **DevOps / DevSecOps** angle: security built into the development and deployment pipeline (code review, dependency scanning, secure configuration as code) instead of bolted on at the network perimeter afterwards
- Assumes prevention will fail → invests in **detection and response**, not just prevention

## Defensible Network Architecture

Building the network in a way you can actually **monitor and control**. Characteristics: it can be watched, inventoried, segmented, kept current, and defended by people who understand it. A network you can't observe can't be defended, however many controls you bolt onto it.

## Logging

Three sources: **system logging**, **security equipment logging**, and **application logging** — routers, switches, firewalls, PCs, servers.

### Syslog (Unix-like)

- Text-message based protocol, runs over **UDP port 514** (TCP and TLS variants exist)
- Supports **remote logging** → can be used for centralised logging; logs can be stored locally *and* centrally, so the central copy acts as a **backup**
- **Why this matters:** an attacker covering their tracks wipes the local logs — if the logs were already shipped off the box, the evidence survives. That's the main defence against log tampering
- Syslog defines **severities** so you can determine the level of an event: emergency, alert, critical, error, warning, notice, informational, debug. Different severities can be routed to different destinations
- Also uses **facilities** (auth, cron, mail, kern…) to say which subsystem produced the message

### Windows

Different model: events are sent to the **event log subsystem** by default, using a **binary storage format** that can be queried as if it were a database.

- Categories: **System, Security, Application** (plus Setup and Forwarded Events)
- Each event has an **Event ID** — a numeric code identifying the type of event, so you can filter and alert on it regardless of the message text
    - Useful ones: **4624** logon success, **4625** logon failure, **4688** process creation, **4720** account created, **1102** security log cleared
- **Windows Event Viewer** is where you browse events and see their IDs
- Windows Event Forwarding (WEF) is the equivalent of remote syslog

Developers can write messages to the log on both Unix and Windows.

## Auditing

Logs are important, but we rely on **application developers** to implement logging — and to make it configurable, with different levels of log messages. Turning logging up to **verbose** makes troubleshooting much easier (though it costs disk and performance).

Both Unix-like systems and Windows offer the ability to enable and configure **auditing**.

### Windows

- Auditing is a security function based on the **success or failure** of system events
- Configured in the **audit policy** within the Local Security Policy application (or via GPO)
- Triggered audit events show up in the **Security log** in Event Viewer

### Linux

- Audit policy is managed with **`auditctl`**
- The **auditing subsystem in the kernel** can watch files and directories for activity, monitor application execution, and monitor **system calls**
- You create rules used by the **`auditd`** daemon (persistent rules in `/etc/audit/audit.rules`, logs in `/var/log/audit/audit.log`)

**Difference between logging and auditing:** logging records what the system did; auditing is the deliberate configuration of *which* events must be recorded, for accountability and evidence.