# Chapter 10 - Social Engineering

# Social Engineering

**Social engineering** = convincing or manipulating someone into doing something they **wouldn't normally do for someone they don't know**. The primary objective is to **get information** — credentials, access, internal details.

## 6 Principles of Influence (Cialdini)

| Principle | How it works |
| --- | --- |
| **Reciprocity** | People feel **obligated to return** a kindness or favour — do something for them first, then ask |
| **Commitment (& Consistency)** | Once someone **commits** to something (even small), they're more inclined to **follow through** — start with a small ask, escalate later |
| **Social Proof** | If others are doing it, it must be **acceptable** — "your colleagues already provided this information" |
| **Authority** | People **follow authority figures** and do what they say — impersonate a manager, IT, law enforcement |
| **Liking** | People are more likely to comply with someone they **like** — build rapport, find common ground, be friendly |
| **Scarcity** | Lack of availability **increases perceived value** and creates urgency — "this offer expires today", "act now or your account will be locked" |

These principles are **combined** in real attacks — e.g. authority + scarcity: "I'm the CFO and I need this wire transfer done in the next 30 minutes."

## Pretexting

A **pretext** = the excuse, the **story you've generated** to explain why you're making contact. It gives a believable reason for the interaction and a **hook** to get the person inclined to engage with you.

A good pretext is **researched** — it uses details gathered during OSINT to sound credible (names, departments, projects, recent events). The more specific it is, the harder it is to question.

## Social Engineering Vectors

| Vector | Method |
| --- | --- |
| **Phishing** | Acquiring information through **deception via electronic communication** — email, instant messaging, social networking platforms. Can be broad (mass phishing) or targeted (**spear phishing** for a specific person, **whaling** for executives) |
| **Vishing** | **Voice phishing** — using phone calls to phish for information. Also useful for **reconnaissance** — calling reception, help desk, or employees to gather details about the target |
| **SMiShing** | Phishing via **SMS** — messages from unknown numbers containing links to malicious sites or credential-harvesting pages |
| **Impersonation** | The **physical** vector — pretending to be someone else in person: a delivery driver, a technician, a new employee. Includes **tailgating** (following someone through a secure door) and **piggybacking** |

## Identity Theft

When someone **steals your information** with the intent of **committing fraud** — opening accounts, making purchases, filing taxes in your name.

**Protection:**

- **Strong, long passwords** (and unique per account)
- **Never give personal information** to unverified contacts
- Use **MFA** (multi-factor authentication)
- Monitor accounts and credit reports
- Be sceptical of unsolicited requests — verify through a separate channel

---

# Cheatsheet

**Cialdini's 6 principles:** Reciprocity · Commitment · Social Proof · Authority · Liking · Scarcity

**Vectors**

| Vector | Channel | Type |
| --- | --- | --- |
| **Phishing** | Email / messaging / social media | Electronic |
| **Vishing** | Phone calls | Voice |
| **SMiShing** | SMS | Text |
| **Impersonation** | In person | Physical |

**Phishing subtypes:** mass phishing (broad) · **spear phishing** (targeted individual) · **whaling** (targeted executive)

**Key terms**

- **Pretexting** = the backstory/excuse that makes the contact believable
- **Tailgating** = following someone through a secure door without credentials
- **Identity theft** = stealing personal info to commit fraud

**Exam reflexes**

- "Which principle?" → match the scenario to the Cialdini principle
- Phone call = **vishing** · SMS = **SMiShing** · email = **phishing** · in person = **impersonation**
- Best defence against social engineering = **security awareness training**

# Physical Social Engineering

Using the **physical vector** to do reconnaissance or gain access — showing up in person rather than hiding behind a screen.

## Badge Access

Restricts access to authorised people using **RFID** (Radio Frequency Identification) devices read by badge readers.

**Bypass methods:**

| Method | How it works |
| --- | --- |
| **Tailgating** | Wait for someone to unlock the door and walk in **behind them without their knowledge** |
| **Piggybacking** | Same, but **with the employee's consent** ("hold the door please") |
| **RFID cloning** | RFID badges operate on radio frequency waves between **125 kHz** (low-frequency, older, easier to clone) and **13.56 MHz** (high-frequency). An attacker can **listen** to the signal and **replicate** it onto a blank badge. Also possible using **NFC** technology on a phone (e.g. Flipper Zero, Proxmark) |
| **Theft** | Simply take someone's card — physical theft or finding a lost badge |

## Man Traps

A physical device that makes entry harder: **two doors separated by a short space**. You pass through the first, it closes behind you, and a second door only opens after identity is verified (badge, PIN, guard). Only **one person at a time** can pass through.

**Bypass:** some buildings have **handicap-accessible doors** that open with just a badge — these may not enforce the one-person-at-a-time rule.

## Biometrics

Using a **physical characteristic unique to you** as a form of authentication — usually for physical access control.

| Type | How it works | Weakness |
| --- | --- | --- |
| **Fingerprint** | Scans the ridges of a finger | Can be fooled by a **high-resolution replica** unless the reader checks **body temperature** — but temperature isn't consistent, so not guaranteed |
| **Iris scanning** | Matches the **colour pattern** of the iris — unique per person, even between your two eyes | More recent, more accurate. Advantage: works **in the dark** |
| **Retinal scanning** | Scans the **back of the eye** — the retina's blood vessel pattern | Very accurate but intrusive (you have to put your eye close to the scanner) |
| **Voiceprint** | Matches vocal characteristics | Unreliable — your voice **changes day to day** (illness, fatigue, emotion). Also vulnerable to **AI voice cloning** |
| **Palm vein scanning** | Maps the **vein pattern** inside the palm using infrared light | Hard to replicate — veins are internal and unique |
| **Gait recognition** | Video analysis of **how someone walks** | Still emerging, can be affected by injury, shoes, or deliberate change |
| **Facial recognition** | Matches facial features | Can be fooled by a **static image** — that's why Apple added a **liveness** component (depth sensing, attention detection) to confirm it's not a photo |

### Measuring biometric effectiveness

Biometric systems are judged on their **error rates**:

| Measure | Meaning |
| --- | --- |
| **FRR** (False Rejection Rate) | Someone who **should** have access is **denied** (false negative) — frustrates legitimate users |
| **FAR** (False Acceptance Rate) | Someone who **shouldn't** have access **gets in** (false positive) — security failure |
| **CER / EER** (Crossover/Equal Error Rate) | The point where FRR = FAR — **lower CER = better system**. This is the standard comparison metric |

The trade-off: tightening the system **lowers FAR** but **raises FRR** (and vice versa). The CER is where the balance sits.

## Phone Calls (Vishing)

Do **reconnaissance before calling** — know names, departments, projects so the pretext sounds believable.

Common pretexts: impersonate the **help desk** or **IT support** ("we need to verify your credentials for a system migration").

## Baiting

**Baiting** = leaving something tempting where the target will find it — a USB drive in a parking lot labelled "Salary Info Q4", a CD marked "Confidential". The victim picks it up, plugs it in, and malware executes.

- You can **train people** to recognise baiting
- But training fails when the bait is **tantalising enough** — curiosity often wins
- Defence: disable USB autorun, block unknown USB devices via endpoint policy

## Tailgating

**Following someone into a locked area** without using your own credentials.

**Defences:**

- **Train people** to challenge unknown followers and report to security
- **Man traps** — enforce one-person-at-a-time
- **Door close timers** — doors that close quickly so a second person can't slip through
- **Security guards** — human verification at entry points

---

# Cheatsheet

**Physical access bypass**

| Method | Description |
| --- | --- |
| **Tailgating** | Follow someone through a door (without their knowledge) |
| **Piggybacking** | Follow with their consent |
| **RFID cloning** | Copy badge signal (125 kHz / 13.56 MHz) to a blank card or phone |
| **Man trap bypass** | Use handicap doors that don't enforce single-person entry |
| **Baiting** | Drop a tempting USB/media for the victim to plug in |

**Biometric types:** fingerprint · iris · retinal · voiceprint · palm vein · gait · facial recognition

**Error rates:** FRR (false rejection) · FAR (false acceptance) · **CER/EER** (where FRR = FAR, lower = better)

**Exam reflexes**

- "Best comparison metric for biometrics?" → **CER / EER**
- "Following someone through a door without their knowledge?" → **tailgating**
- "With their knowledge?" → **piggybacking**
- "USB drive in a parking lot?" → **baiting**
- "Phone call pretending to be IT?" → **vishing**
- "Two doors, one person at a time?" → **man trap**

# Phishing Attacks

## What to look for (red flags)

- **Email address** — does the sender's address match who they claim to be? Look for typos, extra characters, wrong domains
- **URL** — hover before clicking. Does it go where it claims? Look for misspelled domains, extra subdomains, HTTP instead of HTTPS
- **Grammar** — poor spelling and grammar are classic indicators (though AI-generated phishing is making this less reliable)

**What the link does:** it can lead to a page that asks for **authentication information** (credential harvesting) or it can **deliver malware** onto the system.

## Spear phishing

**Targeted** phishing — the attacker has a **specific person** in mind and crafts the email using details gathered during OSINT (name, role, projects, colleagues). Much harder to detect than mass phishing because it looks personalised and relevant.

## Attachments

Phishing emails can also carry **malicious attachments** — commonly disguised as an **invoice**, a shipping notification, or a document requiring review.

- **PDFs** can execute an **embedded executable**
- Office documents can contain **malicious macros**
- Archives (.zip, .rar) can hide executables

## Tools

**FiercePhish** — a platform for running **phishing campaigns** (similar to GoPhish). Used by red teams and awareness programs to simulate phishing, track who clicks, and measure the organisation's resilience.

## Contact Spamming

Social engineering is built on **trust**. If an attacker **compromises someone's email**, they can use the victim's **contact list** — every message appears to come from someone the target already trusts.

But a compromised mailbox isn't the only way: the attacker can do **reconnaissance** to identify trusted people and then **spoof the email address** (forge the From header) without ever accessing the real account.

## Quid Pro Quo

Latin for **"something for something"** — the attacker **offers something in exchange** for what the target gives up.

Example: the attacker calls pretending to be **IT support**, offers to fix a problem, and asks for the user's **credentials** to "resolve the issue." The victim gets help, the attacker gets a password.

The difference from baiting: baiting leaves something for the victim to find; quid pro quo involves a **direct exchange** during an interaction.

---

# Cheatsheet

**Phishing types**

| Type | Target | Channel |
| --- | --- | --- |
| **Phishing** | Broad / mass | Email |
| **Spear phishing** | Specific individual | Email |
| **Whaling** | Executive / high-value target | Email |
| **Vishing** | Anyone | Phone |
| **SMiShing** | Anyone | SMS |

**Red flags:** sender address · URL mismatch · grammar · urgency · unexpected attachment

**Social engineering techniques**

| Technique | How it works |
| --- | --- |
| **Contact spamming** | Compromise or spoof a trusted person's email to leverage their contact list |
| **Quid pro quo** | Offer help or a service in exchange for information (e.g. fake IT support) |
| **Baiting** | Leave something tempting (USB, link) for the victim to interact with |

**Tools:** **FiercePhish** / **GoPhish** — phishing campaign platforms for red teaming and awareness.

**Key points**

- Spear phishing uses **OSINT** to personalise — much harder to detect
- PDFs can carry **embedded executables**; Office docs carry **macros**
- Contact spamming works because the message comes from a **trusted sender**
- Quid pro quo = **exchange** (something for something) — differs from baiting (no interaction, just a lure)

# Social Engineering for Social Networking

Social networks are built on **trust and connections** — which makes them ideal for social engineering.

## Cloning

**Create a duplicate account** that impersonates someone else — same name, same profile picture, same details. The attacker then sends friend/connection requests to the real person's contacts. Once accepted, the clone has a **trusted identity** and can use it to send phishing links, ask for information, or request favours ("I'm locked out, can you send me the code?").

## Account Takeover

A different approach: instead of cloning, **compromise the actual account** — using credentials that may have been **harvested** from a breach, phished, or cracked.

The attacker **takes over the real account** and **retains the existing contact/friend list**. This is more powerful than cloning because:

- The contacts are **already connected** — no need to send new requests
- The message history exists — the attacker can reference past conversations
- The account is **verified and established** — harder for contacts to suspect something is wrong

The attacker then uses the contacts to **acquire something** — credentials, money, access, information.

---

# Cheatsheet

| Technique | Method | Why it works |
| --- | --- | --- |
| **Cloning** | Create a **fake duplicate** account impersonating someone | Contacts accept the clone thinking it's a real person |
| **Account takeover** | **Compromise the real** account and keep the contact list | Messages come from the actual trusted account — no suspicion |

**Key difference:** cloning = fake copy (contacts may notice two accounts); takeover = the real account is hijacked (much harder to detect).

**Defences:** MFA on all social accounts · verify unusual requests through a separate channel · report duplicate profiles · monitor for breach exposure.

# Website Attacks

It's **easier to get people to click links than to open attachments** — that's why attackers set up fake websites. Phishing often leads to a **cloned site** that looks legitimate, where the user enters their credentials thinking they're on the real thing.

## Cloning

You don't need to clone everything — just enough **HTML** to make the page **render convincingly**. Usually the login page is all that matters.

**WinHTTrack** — a **GUI tool** for cloning websites. It can mirror an entire site for offline use, or be used to **test sites** (e.g. checking a list of bookmarks to see if they're still valid).

**wget** — command-line tool for downloading files, packages, tarballs of source code, and doing **recursive GET requests** to a web server. To mirror a site (grab everything):

```
wget -m https://google.com
```

- `m` = mirror mode — grabs the entire site recursively
- Add `-convert-links` and `-page-requisites` to make links **relative** so the site works **offline**

**cURL** — command-line tool for transferring data with URLs. More granular than wget — good for grabbing **individual pages or API responses**, testing headers, and scripting HTTP requests. Less suited for full-site mirroring.

## Rogue Attacks

A **rogue** site is one the user **expects to be legitimate** but that actually has a **malicious purpose** — credential harvesting, malware delivery, or information collection.

**Methods to get users there:**

| Technique | How it works |
| --- | --- |
| **Typosquatting** | Register a domain that looks like a legitimate one but has a **typo** — e.g. `gooogle.com`, `googel.com`. Users who mistype the URL land on the attacker's page |
| **URL hijacking** | A broader term — any **misleading URL** that looks like the real one. Typosquatting is one technique; others include homograph attacks (replacing letters with similar-looking Unicode characters, e.g. `gοogle.com` with a Greek omicron) |
| **Watering hole attack** | Instead of targeting the victim directly, the attacker compromises a site the target **commonly visits** — the "watering hole" they return to. The compromised site then delivers malware or redirects to a credential-harvesting page. Targeted: the attacker **knows** which sites the victim frequents (from OSINT) |

---

# Cheatsheet

**Cloning tools**

| Tool | Type | Command |
| --- | --- | --- |
| **WinHTTrack** | GUI website cloner | GUI |
| **wget** | CLI — mirror entire sites | `wget -m https://target.com` |
| **cURL** | CLI — grab individual pages/responses | `curl https://target.com` |

**Rogue techniques**

| Technique | Description |
| --- | --- |
| **Typosquatting** | Domain with a deliberate typo (user mistypes the URL) |
| **URL hijacking** | Misleading URL that looks real (includes typosquatting + homograph attacks) |
| **Watering hole** | Compromise a site the target **regularly visits** |

**Key points**

- Cloning only needs enough HTML for a **convincing login page** — not the whole site
- `wget -m` mirrors recursively · add `-convert-links` for offline use
- Watering hole is **targeted** — the attacker researches which sites the victim uses
- Typosquatting relies on **user mistakes**; homograph attacks rely on **visual similarity**

# Wireless Social Engineering

Wireless network names (SSIDs) have **no restrictions** — there's no central registry, no verification, and names can **overlap**. Anyone can create a network with any name, including one that matches a legitimate network.

There is also **no way to restrict who receives the wireless signal** once it's been transmitted — radio waves go everywhere within range.

## Rogue WiFi Networks

A **rogue access point** = a fake wireless network set up by the attacker, typically with the **same SSID** as a trusted network (a coffee shop, an office, an airport). Users connect thinking it's the real one, and all their traffic flows through the attacker.

## Wireless security protocols

| Protocol | Security level |
| --- | --- |
| **WEP** (Wired Equivalent Privacy) | Broken — transmissions are **easily decrypted**. Uses a **Pre-Shared Key (PSK)**. Should never be used |
| **WPA** (WiFi Protected Access) | Introduced **enterprise authentication** (RADIUS, username/password). Better than WEP but still has weaknesses |
| **WPA2** | Strong — uses AES encryption. Still PSK or Enterprise modes |
| **WPA3** | Current standard — improved key exchange (SAE), resistant to offline dictionary attacks |

## Captive Portals

Sometimes when you connect to a network, it brings up a **captive portal** — a limited-functionality web page with input boxes for **authentication credentials**. Common in hotels, airports, cafes.

An attacker can **create a fake captive portal** on their rogue AP — users enter their credentials thinking they're logging into the real network, and the attacker captures them.

## Tools

**hostapd** — turns a **wireless interface into an access point**. You configure the SSID, channel, and encryption, and your laptop becomes an AP. Combined with a DHCP server, it's a fully functional rogue network.

**iptables** — Linux firewall/routing tool used to **route traffic from your wireless interface to another interface** connected to the internet. This makes the rogue AP actually work — victims get internet access through you (and you see all their traffic).

**wifiphisher** — an automated tool that combines everything into an attack workflow:

1. **Launch** → it shows all SSIDs in range of your system
2. **Select** the SSID you want to spoof
3. It sends **deauthentication frames** to clients connected to the **real** access point → forces them to **disconnect and reauthenticate** — ideally, they reconnect to your rogue AP instead
4. **Select the attack type**:
    - Some attacks harvest **credentials** (via a fake captive portal or login page)
    - Others attempt to get **code onto the target system** (e.g. fake firmware update page)

**Why deauth works:** 802.11 deauthentication frames are **unauthenticated management frames** — any device can send them. The client's device disconnects and auto-reconnects to the strongest signal with the same SSID, which is now the attacker's AP. *(WPA3 and 802.11w management frame protection mitigate this.)*

---

# Cheatsheet

**Wireless security**

| Protocol | Key type | Status |
| --- | --- | --- |
| **WEP** | PSK | Broken — trivially cracked |
| **WPA** | PSK or Enterprise | Weak — deprecated |
| **WPA2** | PSK or Enterprise (AES) | Strong — still widely used |
| **WPA3** | SAE | Current standard |

**Tools**

| Tool | Purpose |
| --- | --- |
| **hostapd** | Turn a wireless interface into an access point |
| **iptables** | Route traffic from the rogue AP to the internet |
| **wifiphisher** | Automated rogue AP + deauth + credential harvesting / payload delivery |

**Attack flow (wifiphisher):**

1. Scan SSIDs in range
2. Select target SSID to spoof
3. Deauth clients from real AP
4. Clients reconnect to rogue AP
5. Serve fake portal or payload page

**Key points**

- SSIDs have **no registration or uniqueness** — anyone can copy one
- Wireless signals can't be restricted to authorised receivers
- **Deauth frames** are unauthenticated → any device can force disconnections
- Rogue AP + captive portal = **credential harvesting**
- Defence: WPA3 · 802.11w (management frame protection) · VPN on untrusted networks · verify the network before connecting · disable auto-connect

# Automating Social Engineering

## Social-Engineer Toolkit (SET)

An overlay on top of **Metasploit** — a **menu-based program** that uses modules and functionality from Metasploit but pulls everything together **automatically** to accomplish social engineering tasks.

Instead of manually configuring exploits, listeners, payloads and cloned sites separately, SET **walks you through it step by step** — select the attack type, provide the target details, and it handles the rest.

**What SET can do:**

- **Spear phishing** — craft and send targeted emails with malicious attachments or links
- **Website cloning** — clone a site and serve it from your machine with credential harvesting built in
- **Mass mailer** — send phishing emails at scale
- **Infectious media generator** — create payloads for USB/CD
- **PowerShell attacks** — generate PowerShell-based payloads
- **QR code attacks** — generate QR codes that point to malicious URLs

All of this backed by Metasploit's **handlers and payloads** — once a victim interacts, you get a Meterpreter session automatically.

## Social Engineering and AI

AI makes social engineering attacks **more convincing, scalable and personalised** — the three things that used to require human effort and skill.

| Method | How AI helps |
| --- | --- |
| **AI-generated phishing messages** | Perfect grammar, natural tone, no more "red flag" spelling mistakes |
| **Targeted (spear) phishing** | AI processes OSINT data and **automatically crafts personalised** emails referencing real projects, colleagues, events |
| **Impersonation and deepfake** | AI clones voices and generates fake video — convincing enough to impersonate executives on calls |
| **Automated phishing campaigns** | AI manages the entire campaign — generates variants, sends at optimal times, adapts based on responses |
| **Chatbots for real-time manipulation** | AI-powered chatbots that **interact live** with the victim, answering questions and maintaining the pretext dynamically |
| **Emotion and urgency exploitation** | AI analyses the target's communication style and crafts messages that **trigger the right emotional response** — fear, urgency, curiosity |
| **Personalisation at scale** | What used to require a human researching each target now happens **automatically for thousands** of targets simultaneously |
| **More realistic communication** | AI matches tone, vocabulary and context — messages are **indistinguishable** from legitimate ones |
| **Psychological manipulation** | AI applies Cialdini's principles **systematically** — authority, scarcity, reciprocity — tailored to each target's profile |

**The core shift:** traditional social engineering required a **skilled human** for every convincing interaction. AI removes that bottleneck — now a single attacker can run **thousands of personalised, adaptive attacks** simultaneously.

---

# Cheatsheet

**Tools**

| Tool | Purpose |
| --- | --- |
| **SET (Social-Engineer Toolkit)** | Menu-driven automation of social engineering attacks, built on Metasploit |

**SET capabilities:** spear phishing · website cloning + credential harvesting · mass mailer · infectious media (USB/CD) · PowerShell payloads · QR code attacks

**AI-enhanced social engineering:** removes the skill/time bottleneck · personalisation at scale · real-time chatbot manipulation · deepfake/voice cloning · automated adaptive campaigns

**Key points**

- SET = **Metasploit for social engineering** — automates the whole attack chain
- AI's biggest impact: **scale + personalisation** — thousands of unique, convincing attacks at once
- Defence: awareness training alone is no longer enough — need **technical controls** (email filtering, DMARC, link sandboxing, MFA) alongside human vigilance