# Chapter 1 - Ethical Hacking

# Cyber Kill Chain (Lockheed Martin) — 7 steps

**1. Reconnaissance** — research, identify and select targets
**2. Weaponization** — pair malware with an exploit into a deliverable payload (e.g. malicious PDF, macro doc)
**3. Delivery** — transmit the weapon to the target (email, USB, web)
**4. Exploitation** — trigger the exploit against the vulnerable app or system
**5. Installation** — install a backdoor for persistent access
**6. Command & Control (C2)** — the compromised host phones home to an outside server, attacker gets remote control
**7. Actions on Objectives** — the attacker does what he came for (exfiltration, destruction, pivot)

Key idea: it's a chain — break any one link and the attack fails.

# Attack Lifecycle (Mandiant)

**1. Initial Recon** — research the target
**2. Initial Compromise** — first successful code execution on a target machine
**3. Establish Foothold** — persistence, C2 channel set up

↻ **Loop** (repeats until the attacker gets what he needs):

- **Escalate Privileges** — get higher rights, harvest credentials
- **Internal Recon** — map the internal network from inside
- **Move Laterally** — pivot to other systems
- **Maintain Presence** — keep persistent access across the environment

**4. Complete Mission** — exfiltrate the data or achieve the goal

Difference with the Kill Chain: the loop. It reflects how a real intrusion goes back and forth inside the network instead of running straight through.

# MITRE ATT&CK

Knowledge base of real-world attacker behaviour, organised as a matrix.

- **Tactics** = the *why* — the attacker's goal at that step (Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, C2, Exfiltration, Impact)
- **Techniques** = the *how* — the way a tactic is achieved (e.g. T1566 Phishing)
- **Sub-techniques** = a more precise variant (T1566.001 Spearphishing Attachment)
- **Procedures** = the concrete implementation used by a specific group or malware

Together: **TTPs**. Not a linear model like the kill chain — it's a matrix you pick from, used to map detections and describe adversary behaviour.

# Methodology — the 5 phases

**1. Reconnaissance & Footprinting** — gather info about the target

- Recon: collect information, passive (no contact) + active (touching the target)
- Footprinting: part of recon → draw the picture of the org — domains, IP ranges, subdomains, employees, emails, tech stack, network layout
- Goal: define the attack surface
- Tools: whois, nslookup, theHarvester, Maltego, Shodan, Google dorks, Netcraft

**2. Scanning & Enumeration** — probe the live systems

- Scanning: which hosts are up, which ports are open, which services and versions
- Enumeration: dig into those services to extract real assets — usernames, shares, groups, banners, SNMP, AD data
- Tools: Nmap, hping3, Nessus, Angry IP

**3. Gaining Access** — exploit what was found

- A vulnerability, a weak or cracked password, a misconfiguration
- Result: a foothold, at whatever privilege the entry point gives

**4. Maintaining Access** — keep the foothold

- Escalate privileges, plant backdoors / rootkits / persistence
- Often harden the box to keep other attackers out

**5. Covering Tracks** — erase the evidence

- Clear or tamper with logs, disable auditing
- Hide files and tools (steganography, ADS, rootkits), alter timestamps
- Goal: stay undetected and untraceable

Exam tip: Nmap = scanning, never footprinting.