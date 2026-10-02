# Module 01: Introduction to Ethical Hacking

> **Exam:** 312-50 | **Duration:** 4 hours | **Questions:** 125
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Information Security Overview](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#1-information-security-overview)
2. [Hacking Concepts & Hacker Classes](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#2-hacking-concepts--hacker-classes)
3. [Ethical Hacking Concepts](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#3-ethical-hacking-concepts)
4. [Hacking Methodologies & Frameworks](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#4-hacking-methodologies--frameworks)
5. [Information Security Controls](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#5-information-security-controls)
6. [Information Security Laws & Standards](https://www.notion.so/Module-1-364e8b7d8a928093832df19e308be407#6-information-security-laws--standards)

---

## 1. Information Security Overview

### 🎯 Definition

> **Information Security** = protection/safeguarding of information and information systems against unauthorized access, disclosure, alteration, and destruction.
> 

Information is a **critical asset** — its compromise causes financial losses, brand damage, and customer loss.

---

### 🔑 The 5 Elements of Information Security (CIA + 2)

> **State:** "Well-being of information & infrastructure where theft, tampering, and disruption is LOW or TOLERABLE."
> 

| Element | Definition | Controls |
| --- | --- | --- |
| **Confidentiality** | Only **authorized users** can access the information | Data classification, encryption, proper equipment disposal |
| **Integrity** | **Trustworthiness** of data — no improper/unauthorized changes | Checksums, access control |
| **Availability** | Systems are accessible **when required by authorized users** | Disk arrays, clustered machines, antivirus, DDoS prevention |
| **Authenticity** | Data/communication is **genuine and uncorrupted** | Biometrics, smart cards, digital certificates |
| **Non-Repudiation** | Sender **cannot deny** having sent a message; recipient cannot deny receiving it | Digital signatures |

> 💡 **Exam tip:** CIA is the triad. Authenticity and Non-repudiation are the 2 extras — don't forget them!
> 

---

### ⚔️ Information Security Attacks

### Formula

```
Attack = Motive (Goal) + Method (TTP) + Vulnerability
```

### Motives Behind Attacks

- Disrupt business continuity
- Perform information theft / manipulate data
- Create fear and chaos by disrupting critical infrastructures
- Bring financial loss to the target
- Propagate religious or political beliefs
- Achieve military objectives
- Damage the reputation of the target
- Take revenge
- Demand ransom

---

### 🧩 TTPs — Tactics, Techniques, and Procedures

| Term | Definition |
| --- | --- |
| **Tactics** | The **strategy** adopted by the attacker to perform the attack from start to finish |
| **Techniques** | **Technical methods** used to achieve intermediate results during the attack |
| **Procedures** | **Systematic approach** followed by threat actors to launch an attack |

> TTPs help analyze threats, profile threat actors, and strengthen security infrastructure.
> 

---

### 🕳️ Vulnerability

> A **weakness** in the design or implementation of a system that can be exploited to compromise security.
> 

### Common Reasons Vulnerabilities Exist

1. **Hardware/Software Misconfiguration** — misconfig leading to security loopholes
2. **Insecure or poor design** — of network or application (firewalls, IDS, VPN)
3. **Inherent technology weaknesses** — some hardware/software can't defend against certain attacks
4. **End-user carelessness** — sharing credentials, connecting to insecure networks
5. **Intentional end-user acts** — ex-employees abusing residual access

### Vulnerability Tables

| **Technological Vulnerabilities** | Description |
| --- | --- |
| TCP/IP protocol vulnerabilities | HTTP, FTP, ICMP, SNMP, SMTP are inherently insecure |
| Operating System vulnerabilities | Inherently insecure or unpatched OS |
| Network Device Vulnerabilities | No password protection, no auth, insecure routing protocols, firewall flaws |

| **Configuration Vulnerabilities** | Description |
| --- | --- |
| User account vulnerabilities | Insecure transmission of credentials over network |
| System account vulnerabilities | Weak passwords for system accounts |
| Internet service misconfiguration | JS enabled, IIS/Apache/FTP/Terminal services misconfigured |
| Default password and settings | Devices left with factory defaults |
| Network device misconfiguration | Misconfigured network devices |

---

### 🗂️ Classification of Attacks (IATF)

> According to **IATF**, attacks are classified into **5 categories**:
> 

| Category | Description | Examples |
| --- | --- | --- |
| **Passive Attacks** | Intercept & **monitor** traffic without tampering | Footprinting, sniffing, eavesdropping, network traffic analysis, decryption of weak traffic |
| **Active Attacks** | **Tamper** with data / disrupt communication | DoS, MitM, session hijacking, SQL injection, malware, spoofing, replay, privilege escalation |
| **Close-in Attacks** | Attacker in **physical proximity** to gather/modify info | Shoulder surfing, dumpster diving, eavesdropping |
| **Insider Attacks** | **Trusted persons** using privileged access to violate rules | Keyloggers, backdoors, theft of physical devices, data theft |
| **Distribution Attacks** | **Tamper with hardware/software** prior to installation | Backdoors at source or in transit |

> 💡 **Passive attacks** = hard to detect (no active interaction). **Active attacks** = can be detected (traffic is sent).
> 

---

### 🛡️ Information Warfare (InfoWar)

> Use of **ICT (Information and Communication Technologies)** for competitive advantages over an opponent.
> 

**Weapons:** viruses, worms, Trojans, logic bombs, trap doors, nanomachines, electronic jamming, penetration tools.

**Martin Libicki's 7 Categories:**

| Category | Description |
| --- | --- |
| **Command & Control (C2) warfare** | Attacker's impact on a compromised system/network they control |
| **Intelligence-based warfare** | Sensor-based tech that directly corrupts systems |
| **Electronic warfare** | Radio-electronic & cryptographic techniques to degrade communication |
| **Psychological warfare** | Propaganda and terror to demoralize adversary |
| **Hacker warfare** | Shutdown systems, data errors, theft, service theft, false messaging |
| **Economic warfare** | Blocking flow of information to damage economy |
| **Cyberwarfare** | Use of info systems against virtual personas — broadest form |
- **Defensive InfoWar:** strategies to defend against attacks on ICT assets (Prevention, Deterrence, Alerts, Detection, Emergency Preparedness, Response)
- **Offensive InfoWar:** attacks against ICT assets of an opponent (Web App Attacks, Web Server Attacks, Malware, MITM, System Hacking)

---

## 2. Hacking Concepts & Hacker Classes

### 💻 What is Hacking?

> Exploiting **system vulnerabilities** and compromising security controls to gain **unauthorized or inappropriate access** to system resources.
> 
- Involves **modifying** system/application features outside their original purpose
- Can be used to steal/redistribute intellectual property → **business loss**
- Network hacking: scripts, viruses/worms, DoS, trojans, botnets, packet sniffing, phishing, password cracking

---

### 👤 Who is a Hacker?

A hacker is an intelligent individual with excellent computer skills who:

- Can create and explore software/hardware
- May hack as a **hobby** (to see how many systems they can compromise)
- May have intentions ranging from **gaining knowledge** to **doing illegal things**

---

### 🎭 Hacker Classes & Motivations

| Hacker Class | Background | Motivations | Cyber Activity | Potential Targets |
| --- | --- | --- | --- | --- |
| **Script Kiddies** | Inexperienced, uses pre-made scripts/tools without understanding | Thrill, recognition, fun | Simple attacks: DDoS, defacing | Small websites, online games, forums |
| **White Hat Hackers** | Cybersecurity professionals — **have permission** | Improving security, salary, reputation | Penetration tests, vulnerability assessments | Corporations, government agencies |
| **Black Hat Hackers** | Extraordinary computing skills — **malicious/illegal** | Financial gain, data theft, causing harm | Malware, phishing, ransomware, data breaches | Financial institutions, individuals, enterprises |
| **Gray Hat Hackers** | Between ethical and unethical | Recognition, curiosity, financial gain | Vulnerability discovery without permission, sometimes reported | Various, high-profile orgs |
| **Hacktivists** | Politically/socially motivated | Promoting a cause, social justice | DDoS, defacing websites, data leaks | Gov sites, corporations, political groups |
| **State-Sponsored Hackers** | Highly trained, employed by governments | National security, espionage, political objectives | Cyber espionage, infrastructure sabotage | Other nations' government agencies, corporations |
| **Cyber Terrorists** | Extremists, religious/political beliefs | Spreading fear | Attacks on critical infrastructure, propaganda | Critical infrastructure, public services |
| **Corporate Spies (Industrial Spies)** | Hired by companies | Financial gain, competitive advantage | Industrial espionage, data theft, spying | Competitor companies |
| **Blue Hat Hackers** | Contract-based security professionals | Improving product security | Security audits, penetration testing | Tech companies, software firms |
| **Red Hat Hackers** | Vigilantes targeting black hats | Cyber justice, disrupting malicious activity | Hacking black hat infrastructure | Cybercriminal groups |
| **Green Hat Hackers** | Newcomers eager to learn | Learning, curiosity, recognition | Learning techniques, simple attacks | Various, low-risk targets |
| **Suicide Hackers** | Willing to face consequences for a "cause" | Ideology | Attacks on critical infrastructure | Critical systems |
| **Hacker Teams** | Consortium of skilled hackers with funding | Research, advanced attacks | Developing advanced tools | Multiple targets |
| **Insiders** | Trusted employees with privileged access | Various | Violating rules, causing harm | Own organization |
| **Criminal Syndicates** | Organized criminal groups | Financial gain, money laundering | Sophisticated cyber-attacks | Victims across jurisdictions |
| **Organized Hackers** | Hierarchical criminal groups | Pilfering money, IP theft | Multiple attack types | Various |

> 💡 **Exam tip:** Know the Hat colors (White/Black/Gray/Blue/Red/Green) AND their distinctions very well — frequently tested!
> 

---

## 3. Ethical Hacking Concepts

### ✅ What is Ethical Hacking?

> Practice of employing computer and network skills to **test network security** for loopholes and vulnerabilities **WITH PERMISSION** of the owner.
> 
- Also known as **White Hat Hacking** or **Penetration Testing**
- Ethical hackers use the **same tools and techniques** as malicious hackers but **do not damage** the system
- They **report all vulnerabilities** to the system owner for remediation
- Key distinction: **CONSENT** — crackers have no permission; ethical hackers always do

**Key definitions:**

- **"Hacker"** = enjoys learning details & stretching capabilities of computer systems
- **"To hack"** = rapid development of new programs / reverse engineering of software
- **"Cracker" / "Attacker"** = employs hacking skills for **offensive** purposes
- **"Ethical hacker"** = employs hacking skills for **defensive** purposes

---

### ❓ Why is Ethical Hacking Necessary?

> Beat a hacker? **Think like one.**
> 

Ethical hacking is necessary to:

- **Counter attacks** from malicious hackers by anticipating their methods
- Predict vulnerabilities **in advance** and rectify them
- Implement **defense-in-depth** strategies

**Reasons organizations recruit ethical hackers:**

- Prevent hackers from accessing information systems
- Uncover vulnerabilities and explore their risk potential
- Analyze and strengthen security posture (policies, network protection, end-user practices)
- Provide adequate preventive measures to avoid security breaches
- Safeguard customer data
- Enhance security awareness at all levels

**3 Questions an ethical hacker must answer:**

1. **What can an attacker see on the target system?** (Recon & Scanning phases)
2. **What can an intruder do with that information?** (Gaining Access & Maintaining Access phases)
3. **Are the attacker's attempts being noticed?** (Clearing Tracks phase)

---

### 📏 Scope and Limitations of Ethical Hacking

### Scope

- Crucial component of **risk assessment, auditing, counter fraud**, and InfoSec best practices
- Used to **identify risks** and highlight remedial actions
- Reduces **ICT costs** by resolving vulnerabilities
- Involves **Tiger Teams** for full-scale testing

### Rules an Ethical Hacker Must Follow

1. **Gain signed authorization** from the client before testing
2. **Maintain confidentiality** — sign NDA; do not disclose test info to third parties
3. **Perform the test within agreed-upon limits** (e.g., only DoS if agreed)

### Security Audit Framework (Steps)

1. Talk to client → discuss needs
2. Prepare and sign NDA
3. Organize ethical hacking team and schedule
4. Conduct the test
5. Analyze results and prepare report
6. Present findings to client

### Limitations

- Requires the business to **know what they're looking for** before hiring a vendor
- Ethical hacker can only help **understand the security system** — the org must implement safeguards

---

### 🧠 Skills of an Ethical Hacker

| **Technical Skills** | **Non-Technical Skills** |
| --- | --- |
| In-depth knowledge of Windows, Unix, Linux, Macintosh | Ability to quickly learn and adopt new technologies |
| Knowledge of networking concepts, hardware, and software | Strong work ethic and problem-solving skills |
| Computer expert in technical domains | Commitment to organization's security policies |
| Knowledge of security areas and related issues | Awareness of local standards and laws |
| High technical knowledge to launch sophisticated attacks | Good communication skills |

---

### 🤖 AI-Driven Ethical Hacking (CEH v13 New Topic)

> AI technologies enhance the capabilities of ethical hackers — **not replace them**.
> 

**Benefits of AI in Ethical Hacking:**

1. **Efficiency** — automate routine tasks
2. **Accuracy** — reduce false positives
3. **Scalability** — handle large-scale assessments
4. **Cost-Effectiveness** — reduce manual effort

**Key AI Tools for Ethical Hackers:**

- **BurpGPT** — AI-enhanced Burp Suite for web vulnerability scanning
- **BugBountyGPT** — tailored for bug bounty hunters
- **PentestGPT** — automates penetration testing aspects
- **GPT White Hack** — AI-driven risk assessment and threat detection
- **CybGPT** — comprehensive AI tool for security operations
- **BugHunterGPT** — assists in finding and reporting bugs

**Why AI cannot replace ethical hackers:**

- Ethical hacking requires **creativity, critical thinking, domain knowledge**
- AI tools are imperfect — **humans must oversee** and interpret results
- Making **complex ethical decisions** where strict rules don't apply = human judgment only
- Hackers use contextual understanding to craft **tailored mitigation strategies**

---

## 4. Hacking Methodologies & Frameworks

### 🔄 CEH Ethical Hacking Framework (5 Phases)

```
Phase 1: Reconnaissance
    └── Footprinting & Reconnaissance
    └── Scanning & Enumeration
         │
         ▼
Phase 2: Vulnerability Scanning
    └── Vulnerability Analysis
         │
         ▼
Phase 3: Gaining Access
         │
         ▼
Phase 4: Maintaining Access
         │
         ▼
Phase 5: Clearing Tracks
```

### Phase 1: Reconnaissance

> Preparatory phase — gather as much info as possible about the target **before launching an attack**.
> 
- Creates a **profile of the target** (IP ranges, namespace, employees)
- Two types:
    - **Passive:** No direct interaction — public info, news releases, no-contact methods
    - **Active:** Direct interaction — tools to detect open ports, OS mapping, accessible hosts

**Footprinting collects:** IP addresses, employee info, websites, email formats, organizational structure

**Scanning:** Identifies active hosts, open ports, unnecessary services. Logical extension of active recon.

**Enumeration:** Active connections to target — intrusive probing to gather network user lists, routing tables, security flaws, shared users, groups, apps, banners.

### Phase 2: Vulnerability Scanning

> Examine the ability of a system/application to withstand assault.
> 
- Recognizes, measures, classifies security vulnerabilities in systems, networks, communication channels
- Identifies security loopholes for further exploitation

### Phase 3: Gaining Access

> **Where actual hacking occurs.**
> 
- Uses password cracking, exploitation of vulnerabilities including buffer overflows
- Gaining access = point where attacker obtains access to OS or applications
- After initial access → **escalate privileges** to admin/root level
- **Escalating Privileges:** Exploit known vulnerabilities to increase to administrator level

### Phase 4: Maintaining Access

> Attacker retains **ownership** of the system (admin/root privileges).
> 
- Can use system as a **launchpad** to scan/exploit other systems
- Can upload/download/manipulate data, applications, configurations
- Maintains control by closing vulnerabilities to prevent other hackers
- Deploys **Trojans, backdoors, rootkits**

### Phase 5: Clearing Tracks

> **Erase all evidence** of compromise to remain undetected.
> 
- Modify/delete logs using log-wiping utilities
- Remove evidence of their presence
- Modify/create backdoors for future access

---

### 💀 Cyber Kill Chain Methodology (Lockheed Martin)

> Intelligence-driven defense framework for **identification and prevention** of malicious intrusion activities — 7 phases.
> 

```
1. Reconnaissance → 2. Weaponization → 3. Delivery → 4. Exploitation → 5. Installation → 6. Command & Control → 7. Actions on Objectives
```

| Phase | Description | Attacker Activities |
| --- | --- | --- |
| **1. Reconnaissance** | Gather data on target to probe for weak points | Internet searches, social engineering, social media OSINT, Whois/DNS, scanning for open ports |
| **2. Weaponization** | Create deliverable malicious payload (exploit + backdoor) | Selecting/creating malware payload, phishing campaigns, exploit kits, botnets |
| **3. Delivery** | Transmit weaponized bundle to victim | Phishing emails, USB drives, watering hole attacks, malicious attachments |
| **4. Exploitation** | Trigger malicious code to exploit vulnerability | Exploiting software/hardware vulnerabilities, authentication/authorization attacks, code execution |
| **5. Installation** | Install malware on target system for persistent access | Backdoors, rootkits, encryption to hide activities |
| **6. Command & Control (C2)** | Establish two-way communication with victim system | C2 channel via web traffic, email, DNS; privilege escalation; hiding evidence via encryption |
| **7. Actions on Objectives** | Accomplish intended goals | Data exfiltration, service disruption, destroying operational capability, lateral movement |

> 💡 **Exam tip:** Know all 7 phases by name AND what happens in each!
> 

---

### 🗺️ MITRE ATT&CK Framework

> A **globally-accessible knowledge base** of adversary TTPs based on real-world observations.
> 
- Organized into **Tactics → Techniques → Sub-techniques → Procedures**
- Covers **pre-attack and enterprise** stages

**ATT&CK Navigator Phases (Enterprise):**

- Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, **Command and Control**, Exfiltration, **Impact**

**Data Staging:** After penetration, adversary collects and combines data (employee info, financial, network infrastructure) before exfiltration.

---

### 💎 Diamond Model of Intrusion Analysis

> Framework for identifying **clusters of events** correlated on any systems in an organization.
> 

**4 Core Features (Diamond Event):**

| Feature | Description |
| --- | --- |
| **Adversary** | The opponent "WHO" was behind the attack |
| **Victim** | The target "WHERE" the attack was performed |
| **Capability** | The attack strategies "HOW" the attack was performed |
| **Infrastructure** | "WHAT" the adversary used to reach the victim |

**Meta-Features:** Adversary uses Infrastructure (connects to) → deploys Capability → exploits Victim

- Helps develop **efficient mitigation approaches** and increase analytic efficiency
- Identifies missing data points and maps attack flow

---

### 🧭 Indicators of Compromise (IoCs)

> **Clues, artifacts, and forensic data** found on a network/OS that indicate a potential intrusion or malicious activity.
> 

IoCs are **not intelligence** themselves — they are **data points** in the intelligence process.

**Types of IoCs:**

- **Atomic Indicators:** Cannot be broken down further; meaning unchanged in intrusion context (e.g., IP addresses, email addresses)
- **Computed Indicators:** Derived from data extracted from a security incident (e.g., hash values, regex)
- **Behavioral Indicators:** Grouping of atomic and computed indicators combined by logic

**Standards for sharing IoCs:**

- **STIX** (Structured Threat Information Expression)
- **TAXII** (Trusted Automated eXchange of Intelligence Information)

---

## 5. Information Security Controls

### 🛡️ Information Assurance (IA)

> Assurance of **integrity, availability, confidentiality, and authenticity** of information and information systems during usage, processing, storage, and transmission.
> 

Accomplished through **physical, technical, and administrative controls**.

**Processes for achieving IA:**

1. Developing local policy, process, and guidance
2. Designing network and user authentication strategies
3. Identifying network vulnerabilities and threats
4. Identifying problems and resource requirements
5. Creating plans for identified resource requirements
6. Applying appropriate information assurance controls
7. Performing Certification and Accreditation (C&A) processes
8. Providing IA training to all personnel

---

### 🔒 Defense-in-Depth

> **Multi-layered security** approach — multiple defensive measures so if one layer fails, others still protect the asset.
> 

**Layers (from outer to inner):**

1. **Policies, Procedures & Awareness**
2. **Physical Security**
3. **Perimeter** (firewalls, DMZ)
4. **Internal Network** (IDS/IPS, network segmentation)
5. **Host** (OS hardening, endpoint protection)
6. **Application** (input validation, secure coding)
7. **Data** (encryption, access control, DLP)

---

### 🔁 Continual/Adaptive Security Strategy

> The adaptive security strategy = continuous **Predict → Protect → Detect → Respond** cycle.
> 

| Activity | Description |
| --- | --- |
| **Protection (01)** | Defense-in-depth: protect endpoints, network, data; security policies, physical security, firewall, IDS |
| **Detection (02)** | Monitor for abnormalities — network monitoring, packet sniffing tools |
| **Responding (03)** | Incident response, investigation, containment, impact mitigation, eradication |
| **Prediction (04)** | Risk & vulnerability assessment, attack surface analysis, threat intelligence |

**Assets are protected by:** Technical Technology, Administrative Operations, with People and Physical as core.

---

### ⚠️ Risk Management

> **Risk** = degree of uncertainty/expectation that an adverse event may cause damage.
> 

**Formulas:**

```
RISK = Threats × Vulnerabilities × Impact
RISK = Threat × Vulnerability × Asset Value
```

**Risk Levels:**

| Risk Level | Action |
| --- | --- |
| **Extreme/High** (81-100%) | Immediate measures; identify and impose controls to reduce risk |
| **Medium** (41-60%) | No urgent action; implement controls as soon as possible |
| **Low** (1-20%) | Take preventive steps to mitigate effects |

---

### 🔍 Cyber Threat Intelligence (CTI)

> Organized, analyzed, refined information about potential/current attacks that can be used to minimize and mitigate cybersecurity threats.
> 

**Types of Threat Intelligence:**

- **Strategic:** High-level business strategies (for senior management)
- **Tactical:** TTPs (for security operations)
- **Operational:** Specific threats (for incident handlers)
- **Technical:** IoCs (for technical teams)

### Threat Intelligence Lifecycle (5 Phases)

```
1. Planning & Direction → 2. Collection → 3. Processing & Exploitation → 4. Analysis & Production → 5. Dissemination & Integration
```

| Phase | Description |
| --- | --- |
| **1. Planning & Direction** | Define intelligence requirements, form team, make collection plan, send collection requests |
| **2. Collection** | Gather data using OSINT, HUMINT, IMINT, MASINT |
| **3. Processing & Exploitation** | Process raw data; convert to usable format |
| **4. Analysis & Production** | Combine info, include facts/findings/forecasts; be objective, timely, accurate, actionable |
| **5. Dissemination & Integration** | Deliver to stakeholders at strategic, tactical, operational, technical levels |

---

### 🚨 Incident Management Process (9 Steps)

> Structured approach to manage security incidents.
> 

| Step | Activity |
| --- | --- |
| **1. Preparation** | Establish policies, procedures, tools, team; define roles |
| **2. Incident Recording & Assignment** | Report, record, define communication plans |
| **3. Incident Triage** | Analyze, validate, categorize, prioritize incidents |
| **4. Notification** | Inform stakeholders, management, third-party vendors, clients |
| **5. Containment** | Prevent spread of infection to other assets |
| **6. Evidence Gathering & Forensic Analysis** | Accumulate evidence; forensic analysis reveals method, vulnerabilities, devices |
| **7. Eradication** | Remove/eliminate root cause; close all attack vectors |
| **8. Recovery** | Restore affected systems, services, resources, data |
| **9. Post-Incident Activities** | Documentation, impact assessment, review/revise policies, close investigation, incident disclosure |

---

### 🤖 AI/ML in Information Security

> AI/ML is transforming cybersecurity for both attackers and defenders.
> 

**Attacker use:** AI-driven automated attacks, adversarial ML, AI-generated phishing
**Defender use:** Threat detection, anomaly detection, UEBA, automated incident response

**AI Security Tools:** SIEM platforms with ML (Splunk, IBM QRadar), EDR solutions, NTA tools

---

## 6. Information Security Laws & Standards

### 📜 PCI DSS (Payment Card Industry Data Security Standard)

> Proprietary InfoSec standard for organizations handling **cardholder data** for major debit, credit, prepaid, e-purse, ATM, and POS cards.
> 

Applies to: merchants, processors, acquirers, issuers, service providers, and ALL entities that store/process/transmit cardholder data.

**PCI DSS High-Level Requirements:**

1. Build and Maintain a Secure Network
2. Protect Cardholder Data
3. Maintain a Vulnerability Management Program
4. Implement Strong Access Control Measures
5. Regularly Monitor and Test Networks
6. Maintain an Information Security Policy

> ⚠️ Failure to meet PCI DSS = **fines or termination of payment card processing privileges**
> 

---

### 🌍 ISO/IEC 27000 Series

| Standard | Coverage |
| --- | --- |
| **ISO/IEC 27001:2022** | ISMS (Information Security Management System) — requirements for establishing, implementing, maintaining, continually improving |
| **ISO/IEC 27002:2022** | Code of practice for InfoSec controls |
| **ISO/IEC 27032:2023** | Internet/cybersecurity guidelines — identifies stakeholders and roles |
| **ISO/IEC 27033-7:2023** | Network virtualization security guidelines |
| **ISO/IEC 27036-3:2023** | Securing hardware, software, and services supply chains |
| **ISO/IEC 27040:2024** | Data storage security — technical requirements |

---

### 🇺🇸 Key US Laws

| Law | Description |
| --- | --- |
| **HIPAA** (Health Insurance Portability and Accountability Act) | Protects health information privacy; defines standards for electronic health data exchange |
| **SOX** (Sarbanes-Oxley Act) | Financial reporting standards; prevents accounting fraud; affects IT security audit trails |
| **DMCA** (Digital Millennium Copyright Act) | Criminalizes production/distribution of tech that circumvents copyright protection |
| **FISMA** (Federal Information Security Modernization Act) | Framework for protecting federal government information and assets |
| **CFAA** (Computer Fraud and Abuse Act) | Criminalizes unauthorized access to computers |

---

### 🇪🇺 GDPR (General Data Protection Regulation)

> EU regulation protecting personal data of EU citizens — effective **May 25, 2018**.
> 

**Key provisions:**

- Personal data must be processed **lawfully, fairly, and transparently**
- Data subject has rights: access, erasure, portability, objection
- Mandatory breach notification **within 72 hours**
- Penalties: up to **€20 million or 4% of global annual turnover**

---

### 🇬🇧 DPA 2018 (Data Protection Act 2018)

> UK framework for data protection law — replaces DPA 1998, effective **25 May 2018**.
> 
- Protects individuals re: processing of personal data
- Extends data protection to national security and defense areas
- Sets out Information Commissioner's (ICO) functions and powers
- Post-Brexit: amended to reflect UK's status outside EU

---

### 🌏 Cyber Laws by Country (Selected)

| Country | Law |
| --- | --- |
| USA | Computer Fraud and Abuse Act (CFAA) |
| UK | Computer Misuse Act 1990 |
| Germany | Section 202a StGB — Data Espionage |
| Canada | Criminal Code of Canada (s. 342.1) |
| Australia | Criminal Code Act 1995 (Part 10.7) |
| India | Information Technology Act 2000 |
| Japan | Unauthorized Access Prohibition Law |
| Brazil | LGPD (General Data Protection Law) |
| Philippines | Republic Act No. 10175 |
| Hong Kong | Article 139 of the Basic Law |

---

## 🧪 Quick Exam Cheat Sheet

### Must-Know Definitions

| Term | One-liner |
| --- | --- |
| **Vulnerability** | Weakness in a system that can be exploited |
| **Threat** | Potential cause of an unwanted incident |
| **Risk** | Probability × Impact |
| **Exploit** | Technique that takes advantage of a vulnerability |
| **Attack** | Motive + Method (TTP) + Vulnerability |
| **IoC** | Clue/artifact indicating potential intrusion |
| **TTP** | Tactics (strategy) + Techniques (methods) + Procedures (systematic approach) |
| **Ethical Hacker** | Security professional who hacks with **authorization** to improve security |
| **Penetration Testing** | Simulated cyberattack to evaluate security |

---

### 🔑 Key Formulas

```
RISK = Threats × Vulnerabilities × Impact
ATTACK = Motive (Goal) + Method (TTP) + Vulnerability
```

---

### 🎯 5 Phases of Hacking (CEH Framework)

```
1. Reconnaissance (Footprinting + Scanning + Enumeration)
2. Vulnerability Scanning
3. Gaining Access (+ Privilege Escalation)
4. Maintaining Access (Backdoors, Trojans, Rootkits)
5. Clearing Tracks (Log manipulation, anti-forensics)
```

---

### 💀 7 Phases of Cyber Kill Chain

```
1. Reconnaissance → 2. Weaponization → 3. Delivery → 4. Exploitation
5. Installation → 6. C2 (Command & Control) → 7. Actions on Objectives
```

---

### 🎩 Hat Colors Quick Reference

| Hat | Legal? | Authorized? | Intent |
| --- | --- | --- | --- |
| White Hat | ✅ | ✅ | Defensive |
| Black Hat | ❌ | ❌ | Malicious |
| Gray Hat | ⚠️ | ❌ (no permission) | Neutral/discloses |
| Blue Hat | ✅ | ✅ | Contract-based testing |
| Red Hat | ⚠️ | Self-authorized | Aggressive vs. Black Hats |
| Green Hat | ⚠️ | ❌ | Learner |

---

### 📋 5 Elements of InfoSec — Quick Memory Aid

```
CIA + AN
C = Confidentiality (authorized access only)
I = Integrity (trustworthy, no unauthorized changes)
A = Availability (accessible when needed)
A = Authenticity (genuine / uncorrupted)
N = Non-Repudiation (sender can't deny sending)
```

---

### ⚖️ Key Laws to Remember

| What | Law |
| --- | --- |
| Healthcare data (USA) | HIPAA |
| Financial data (USA) | SOX |
| Payment cards (Global) | PCI DSS |
| EU personal data | GDPR |
| UK personal data | DPA 2018 |
| Federal IT security (USA) | FISMA |
| Copyright circumvention (USA) | DMCA |
| InfoSec management standard | ISO/IEC 27001 |

---
