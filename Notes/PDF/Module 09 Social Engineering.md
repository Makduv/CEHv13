# Module 09: Social Engineering

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [Social Engineering Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-social-engineering-concepts)
2. [Human-based Social Engineering Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-human-based-social-engineering-techniques)
3. [Computer-based Social Engineering Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-computer-based-social-engineering-techniques)
4. [Identity Theft](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-identity-theft)
5. [Mobile-based Social Engineering Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-mobile-based-social-engineering-techniques)
6. [Social Engineering Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-social-engineering-countermeasures)
7. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-quick-exam-cheat-sheet)

---

## 1. Social Engineering Concepts

### 🎯 What is Social Engineering?

> **Social Engineering** = the art of **convincing people** to reveal confidential information. Social engineers depend on the fact that people are **unaware** of the valuable information they have access to and are careless about protecting it.
> 
- Targets the **weakness of people**, not network security issues
- No specific software or hardware can defend against it
- "Bottom line": there is no ready defense — only **constant vigilance**

**Pre-attack info gathering sources:** organization's official websites (employee IDs/names/emails), advertisements, blogs/forums

**Post-info-gathering approaches:** impersonation, piggybacking, tailgating, reverse social engineering

---

### 🧠 Behaviors Vulnerable to Attacks (7 Factors — CRITICAL)

```
Authority | Intimidation | Consensus | Scarcity | Urgency | Familiarity | Trust | Greed
```

### 🏢 Factors That Make Companies Vulnerable to Attacks

- Insufficient security training
- Unregulated access to information
- Several organizational units (siloed security)
- Lack of security policies

### ⚡ Why is Social Engineering Effective?

- Security policies are only as strong as their **weakest link** — human behavior
- Difficult to detect attempts
- No method guarantees complete security
- No specific hardware/software defends against it
- Relatively cheap (or free) and easy to implement

---

### 🎯 Common Targets of Social Engineering

| Target | Why |
| --- | --- |
| **Receptionists/Help-Desk Personnel** | Tricked into divulging confidential info; want to be helpful |
| **Technical Support Executives** | Contacted while attacker poses as senior management, customer, vendor |
| **System Administrators** | Hold critical info (OS type/version, admin passwords) |
| **Users and Clients** | Approached by attacker posing as tech support |
| **Vendors of the Target Organization** | Targeted for critical info to plan attacks |
| **Senior Executives** | Approached across Finance, HR, CxO departments |

---

### 💥 Impact of Social Engineering Attack on an Organization

- **Economic Losses** — competitors steal development plans/marketing strategies
- **Damage to Goodwill** — leaking sensitive data damages customer trust
- **Loss of Privacy** — organization loses stakeholder/customer trust
- **Dangers of Terrorism** — terrorists build target blueprints via social engineering
- **Lawsuits and Arbitration** — negative publicity, affects business performance
- **Temporary or Permanent Closure** — severe cases force business shutdown

---

### 🔄 4 Phases of a Social Engineering Attack (CRITICAL)

```
1. Research the Target Company
   - Gather basic info: nature of business, location, employee count
   - Dumpster diving, browsing company website, finding employee details
        ↓
2. Select a Target
   - Attacker selects target for extracting sensitive info
   - Disgruntled employees are preferred — easier to manipulate
        ↓
3. Develop a Relationship
   - Attacker builds relationship with the selected employee
        ↓
4. Exploit the Relationship
   - Extracts sensitive info about accounts, finance, technologies, upcoming plans
```

---

## 2. Human-based Social Engineering Techniques

### 🎭 Impersonation (Most Common Human-based Technique)

> Attacker **pretends to be someone legitimate or authorized**, personally or via communication medium (phone, email). Helps trick target into revealing sensitive information.
> 

**Types of Impersonation:**

- Posing as a legitimate end-user
- Posing as an important user (VIP)
- Posing as a technical support agent
- Posing as an internal employee, client, or vendor
- Posing as a repairman
- Abusing the over-helpfulness of the help desk
- Posing as someone with third-party authorization
- Posing as a tech support agent through **vishing**
- Posing as a trusted authority

**Examples:**

- *"Hi! This is John from the Finance Department. I have forgotten my password. Can I get it?"* (legitimate end-user)
- *"Hi! This is Kevin, CFO Secretary. I'm working on an urgent project and lost my system's password. Can you help me out?"* (important user)
- *"Sir, this is Matthew, Technical Support, X company. Last night we had a system crash here, and we are checking for the lost data. Can you give me your ID and password?"* (technical support agent)

---

### 📊 7 Other Human-based Techniques (CRITICAL TABLE)

| Technique | Description |
| --- | --- |
| **Eavesdropping** | Unauthorized listening of conversations or reading of messages; interception of audio/video/written communication via communication channels (phone lines, email, IM) |
| **Shoulder Surfing** | Direct observation — looking over someone's shoulder to get passwords, PINs, account numbers. Can be done from a distance using vision-enhancing devices (binoculars) |
| **Dumpster Diving** | Looking for treasure in someone else's trash — collecting phone bills, contact info, financial info, operational info from trash bins, printer bins, or sticky notes |
| **Reverse Social Engineering** | Attacker presents self as an authority; target seeks his/her advice before/after offering the info the attacker needs |
| **Piggybacking** | An authorized person **intentionally or unintentionally** allows an unauthorized person to pass through a secure door (e.g., "I forgot my ID badge at home. Please help me") |
| **Tailgating** | Attacker, wearing a **fake ID badge**, enters a secured area by closely following an authorized person through a door requiring key access — WITHOUT consent |
| **Diversion Theft** | Attacker tricks a person responsible for a genuine delivery into delivering the consignment to a location other than the intended one |

> 💡 **Piggybacking vs Tailgating:** Piggybacking = authorized person KNOWINGLY/willingly allows entry; Tailgating = attacker sneaks in WITHOUT the authorized person's consent/knowledge.
> 

---

### 🗑️ Information Obtainable via Dumpster Diving

- **Phone lists** — employee names and contact numbers
- **Organizational charts** — company structure, server rooms, restricted areas
- **Email printouts, notes, faxes, memos** — personal details, passwords, contacts
- **Policy manuals** — employment, system use, operations info
- **Event notes, calendars, computer use logs** — reveals log on/off timings for attack timing

---

### 🔄 Reverse Social Engineering — 3 Techniques

| Technique | Description |
| --- | --- |
| **Sabotage** | Attacker corrupts (or makes it appear corrupted) the workstation — user seeks help |
| **Marketing** | Attacker advertises (business card in target's office, contact number on error message) to ensure the user calls them |
| **Support** | Attacker continues to assist users even after acquiring desired info, remaining unidentified |

---

### 🤝 Other Human-based Techniques

| Technique | Description |
| --- | --- |
| **Third-party Authorization** | Attacker claims an absent authority figure (on vacation/traveling) authorized them to receive information — makes verification impossible |
| **Tech Support** | Attacker uses vishing, poses as tech support staff of a vendor/contractor to obtain credentials under pretext of troubleshooting |
| **Quid Pro Quo** | Latin "something for something" — attacker calls random numbers claiming to be tech support, offers to fix a genuine issue in exchange for credentials/data |
| **Elicitation** | Extracting specific info via normal, disarming conversation — requires good social skills |
| **Bait and Switching** | Attacker presents an exciting offer (link/file download) to prompt action; primarily targets e-commerce customers |

---

## 3. Computer-based Social Engineering Techniques

### 🎣 Phishing

> Practice of sending an **illegitimate email** claiming to be from a legitimate site to acquire a user's personal/account info. Redirects users to fake webpages mimicking trustworthy sites.
> 

**Reasons for phishing success:** users' lack of knowledge, visual deception, not paying attention to security indicators

---

### 📊 Types of Phishing (COMPREHENSIVE TABLE — CRITICAL)

| Type | Description |
| --- | --- |
| **Spear Phishing** | Targeted attack aimed at specific individuals/small group within an organization, using specialized social engineering content |
| **Whaling** | Targets **high-profile executives** (CEOs, CFOs, politicians, celebrities) with complete access to valuable info; via email/website spoofing |
| **Pharming** | Redirects web traffic to a fraudulent website by installing malicious program on PC/server. Also called "phishing without a lure" — via **DNS Cache Poisoning** or **Host File Modification** |
| **Spimming** | Variant of spam exploiting Instant Messaging platforms; uses bots to harvest IM IDs and spread spam |
| **Clone Phishing** | Attacker creates a **nearly identical copy** of a legitimate communication, modifying the link/attachment to point to a malicious destination |
| **E-wallet Phishing** | Targets electronic wallet users by posing as a legitimate e-wallet provider |
| **Tabnabbing** | Malicious webpage tricks users by changing its content to resemble a familiar site (e.g., bank login) when the tab is switched away and back |
| **Reverse Tabnabbing** | Legitimate-looking website deceives users into opening a malicious link, which then alters the ORIGINAL tab's content to a phishing site |
| **Consent Phishing** | Exploits the **OAuth authentication protocol** (Google/Facebook/Microsoft); fake app tricks victim into granting permissions to their account |
| **Search Engine Phishing** | Manipulates search engine results (SEO manipulation, keyword stuffing) to lead users to fraudulent websites |

---

### 🤖 AI-Assisted Phishing

Attackers use tools like ChatGPT to craft convincing phishing emails (e.g., posing as Microsoft support, urgent password reset requests) — increases speed and realism of social engineering content generation.

---

### 💬 Other Computer-based Techniques

| Technique | Description |
| --- | --- |
| **Pop-up Windows** | Fake alert windows (e.g., "VIRUS ALERT FROM MICROSOFT — This computer is BLOCKED") trick users into calling fraudulent support numbers or entering info |
| **Hoax Letters** | Messages warning of non-existent computer virus threats; cause productivity loss, not physical damage |
| **Chain Letters** | Offer free gifts (money/software) if forwarded to a set number of recipients; use "get-rich-quick" schemes, superstition |
| **Instant Chat Messenger** | Attacker chats with users to gather personal info (DOB, maiden name) to crack accounts |
| **Spam Email** | Irrelevant/unsolicited emails to collect financial/network info; may carry hidden malware with long filenames to hide extensions |
| **Scareware** | Malware tricking users into visiting malware-infested sites or downloading malicious software via fake urgent pop-ups (e.g., "STOLEN IDENTITY — DATA LEAK WARNING! ACT NOW!") |

---

### 🎬 Deepfake

> AI-generated fake video/audio content that convincingly impersonates a real person.
> 

**Deepfake creation process:**

- Requires proficiency in video editing (Adobe Premiere Pro, Final Cut Pro, DaVinci Resolve)
- Requires knowledge of compositing, color grading, motion tracking, rotoscoping
- **Source video** = contains the face to deepfake; **Destination video** = target video receiving the deepfake face

**Deepfake Tools:** DeepFaceLab, Vidnoz, Deepfakesweb, Synthesia, DeepBrain AI, Hoodem

---

## 4. Identity Theft

### 🆔 What is Identity Theft?

> A crime in which an **imposter steals personally identifiable information** (PII) — name, credit card number, SSN, driver's license number, etc. — to commit fraud or other crimes. Attackers use identity theft to **impersonate employees** and physically access facilities.
> 

**PII types commonly stolen:** Name, home/office address, SSN, phone number, DOB, medical history/health insurance info, biometric data, bank account number, credit card info, credit report, driving license number, passport number

---

### 📊 Types of Identity Theft

| Type | Description |
| --- | --- |
| **Criminal Identity Theft** | Criminal uses someone's identity to escape criminal charges; provides assumed identity when caught |
| **Financial Identity Theft** | Victim's bank/credit card info stolen and illegally used — maxing cards, withdrawing money, opening new accounts |
| **Driver's License Identity Theft** | Easiest type — stolen license used to commit traffic violations under victim's name |
| **Insurance Identity Theft** | Perpetrator uses victim's medical info to access insurance for medical treatment |
| **Medical Identity Theft** | Most dangerous type — uses victim's info for medical products/healthcare services; can cause false diagnoses |
| **Tax Identity Theft** | Perpetrator steals victim's SSN to file fraudulent tax returns/refunds |

---

### 🎯 Common Techniques to Obtain PII

- **Mail Theft and Rerouting** — stealing mailbox contents (bank documents, admin forms) or rerouting mail to new address
- **Social Media Mining** — gathering PII from social profiles to create fake identities
- **Data Trading on Dark Web** — purchasing PII (SSN, credit card, bank info, credentials) from dark web marketplaces

---

### ⚠️ Indications of Identity Theft

- Unfamiliar credit card charges
- No longer receiving credit card/bank/utility statements
- Creditors call about unknown accounts in your name
- Numerous unauthorized traffic violations under your name
- Charges for medical treatment never received
- More than one tax return filed under your name
- Denied access to own account/loans
- Not receiving service bills (electricity, gas, water) due to stolen mail
- Sudden changes in medical records
- Notification of data breach involving your info
- Inexplicable cash withdrawal
- Calls from fraud control departments about suspicious activity
- Refusal of government benefits (already claimed under your child's SSN by another account)

---

## 5. Mobile-based Social Engineering Techniques

### 📱 Publishing Malicious Apps and Repackaging Legitimate Apps

**Publishing Malicious Apps:**

```
1. Attacker creates malicious mobile application (e.g., gaming app)
2. Attacker publishes malicious app on app store
3. User downloads and installs the malicious mobile application
4. App sends user credentials to the attacker
```

**Repackaging Legitimate Apps:**

```
1. Legitimate developer creates a gaming app, uploads to app store
2. Malicious developer downloads the legitimate game, repackages it with malware
3. Malicious developer uploads repackaged game to third-party app store
4. End user downloads the malicious gaming app
5. App sends user credentials to the malicious developer
```

---

### 📲 Fake Security Applications

Attacker infects victim's PC with malware → uploads malicious app to an app store → when victim logs into their bank account, malware displays a pop-up telling them to download a security app on their phone → victim downloads from attacker's app store → attacker obtains bank credentials + intercepts the SMS-based second authentication factor.

---

### 💬 SMiShing (SMS Phishing)

> The act of using the **SMS text messaging system** of mobile devices to lure users into instant action — downloading malware, visiting a malicious webpage, or calling a fraudulent phone number.
> 

**Example flow:** Attacker sends SMS ("XIM BANK Emergency! Please call 08-7999-433") → victim thinks it's a real message from their bank → victim calls the number → an automated recording asks for credit/debit card number → victim reveals sensitive information.

---

### 🔲 QRLJacking

> Social engineering attack that exploits the **QR Code Login** method in web applications to hijack login sessions and gain unauthorized access to victims' accounts.
> 

**Attack flow:**

```
1. Attacker initiates a QR code login session with the web service
2. Web service returns QR code to attacker
3. Attacker clones the retrieved QR code
4. Attacker creates a phishing page using the cloned QR code
5. Attacker sends the phishing webpage URL to the victim
6. Victim scans the malicious QR code
7. Victim's device ID and login credentials sent to the attacker
8. Attacker logs into victim's account using the stolen session
```

- Can also obtain: GPS location, device ID, IMEI, SIM card details
- **Tool: QR TIGER** — QR code generator that allows creating duplicate copies of legitimate static/dynamic QR codes

---

## 6. Social Engineering Countermeasures

### 🎯 Main Objectives of Defense Strategies

Create **user awareness**, robust **internal network controls**, and **security policies, plans, and processes**.

### 📋 Official Security Policies

- Disseminate policies among employees; provide proper education/training (specialized for higher-risk positions)
- Obtain employee signatures acknowledging policy understanding
- Define consequences of policy violations

---

### 🔑 Password Policies

- Change passwords regularly
- Avoid easily guessable passwords (avoid answers to common SE questions — birthplace, favorite movie, pet's name)
- Block accounts after certain number of failed attempts
- Choose long (min 6–8 characters) and complex passwords
- Do not disclose passwords to anyone
- Set up a password expiration policy
- Avoid sharing computer accounts
- Avoid reusing the same password across accounts
- Avoid storing passwords on media/sticky notes
- Avoid communicating passwords via phone/email/SMS
- Lock/shut down the computer before stepping away

---

### 🏢 Physical Security Policies

- Issue ID cards, uniforms, and other access control measures to employees

---

### 🔒 Deepfake Attack Countermeasures

- Implement **digital watermarking** techniques for authentic videos
- Use **blockchain technology** to record/verify original digital content authenticity
- Improve **facial recognition** to distinguish real vs. artificially generated faces
- Implement strong privacy measures to protect biometric data
- Enhance user reporting mechanisms on social media to flag deepfakes
- Train public/media professionals to critically evaluate digital content
- Develop **AI/ML detection tools** — flag unnatural eye movements, facial expressions, inconsistent lighting, flawed lip-syncing
- Establish ethical guidelines for AI developers/users
- Use advanced forensic techniques (compression artifacts, pixel-level analysis, audio/video inconsistencies)

---

### 🛠️ Anti-Phishing Tools

| Tool | Description |
| --- | --- |
| **Netcraft** | Browser extension blocking suspected phishing sites |
| **PhishTank** | Collaborative clearinghouse for phishing data; open API for anti-phishing integration |
| **OhPhish** (EC-Council Aware) | Phishing simulation/awareness platform — Entice to Click, Credential Harvesting, Send Attachment, Assign New Training, Vishing, Smishing campaigns |

---

## 7. Quick Exam Cheat Sheet

### 🔑 7 Behaviors Vulnerable to Attacks

```
Authority | Intimidation | Consensus | Scarcity | Urgency | Familiarity | Trust | Greed
```

---

### 🔄 4 Phases of Social Engineering Attack

```
1. Research the Target Company → 2. Select a Target →
3. Develop a Relationship → 4. Exploit the Relationship
```

---

### 📊 Human-based Techniques Summary

```
Impersonation | Eavesdropping | Shoulder Surfing | Dumpster Diving |
Reverse Social Engineering | Piggybacking | Tailgating | Diversion Theft |
Third-party Authorization | Tech Support | Quid Pro Quo | Elicitation | Bait & Switching
```

**Key distinction:**

- **Piggybacking** = WITH consent (unwitting or willing)
- **Tailgating** = WITHOUT consent (fake badge, sneaking through)

---

### 📊 10 Types of Phishing

```
Spear Phishing | Whaling | Pharming | Spimming | Clone Phishing |
E-wallet Phishing | Tabnabbing/Reverse Tabnabbing | Consent Phishing | Search Engine Phishing
```

---

### 📊 6 Types of Identity Theft

```
Criminal | Financial | Driver's License | Insurance | Medical | Tax
```

---

### 📱 Mobile-based Techniques

```
Publishing Malicious Apps | Repackaging Legitimate Apps |
Fake Security Applications | SMiShing | QRLJacking
```

---

### 🔥 Common Exam Scenarios

**Q: What are the 4 phases of a social engineering attack?**
→ **Research the Target → Select a Target → Develop a Relationship → Exploit the Relationship**

**Q: What's the difference between piggybacking and tailgating?**
→ Piggybacking = authorized person KNOWINGLY allows entry; Tailgating = attacker sneaks in WITHOUT consent (often with a fake badge)

**Q: What phishing type targets high-profile executives like CEOs?**
→ **Whaling**

**Q: What phishing type is also known as "phishing without a lure"?**
→ **Pharming** (via DNS Cache Poisoning or Host File Modification)

**Q: What phishing type exploits the OAuth authentication protocol?**
→ **Consent Phishing**

**Q: What phishing type changes tab content while the user is away, then reverts to trick them?**
→ **Tabnabbing**

**Q: What attack exploits QR Code Login to hijack sessions?**
→ **QRLJacking**

**Q: What is SMiShing?**
→ SMS-based phishing — uses text messages to lure victims into instant action

**Q: What are the 3 techniques of Reverse Social Engineering?**
→ **Sabotage, Marketing, Support**

**Q: What Latin phrase describes an attacker offering "something for something" (fixing an issue for credentials)?**
→ **Quid Pro Quo**

**Q: What identity theft type is considered the most dangerous?**
→ **Medical Identity Theft** (can cause false diagnoses/life-threatening decisions)

**Q: What identity theft type is considered the easiest to commit?**
→ **Driver's License Identity Theft**

**Q: What are the 7 behaviors that make people vulnerable to social engineering?**
→ **Authority, Intimidation, Consensus, Scarcity, Urgency, Familiarity, Trust, Greed**

**Q: What tool is a collaborative phishing data clearinghouse with an open API?**
→ **PhishTank**

**Q: What video/audio impersonation technique is countered with digital watermarking and blockchain verification?**
→ **Deepfake**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 09*