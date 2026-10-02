# Chapter 13 - Cryptography

# Basic Encryption

**Plaintext** = data that is consumable without anything else being done to it — readable as-is.

**Ciphertext** = plaintext that has gone through an **encryption algorithm** — unreadable without the key to reverse it.

## Substitution Ciphers

### Rotation cipher (Caesar cipher)

Takes an alphabet and **rotates the letters** by a fixed number of positions. One letter is simply **substituted** for another. Used by **Julius Caesar**.

**Example (rotation of 4):**

```
Plain:  ABCDEFGHIJKLMNOPQRSTUVWXYZ
Cipher: EFGHIJKLMNOPQRSTUVWXYZABCD

HELLO → LIPPS
```

The **key** = the number of positions the alphabet is rotated.

**ROT13** = rotation by **13 positions** (exactly half the alphabet). Applying it twice gives you back the original — ROT13 is its own inverse.

**Weakness:** only **25 possible keys** → trivially brute-forced. Can also be broken with **frequency analysis** — comparing the statistical distribution of letters in the ciphertext against the known distribution in the language (e.g. in English, E is the most common letter).

### Vigenère cipher

A **polyalphabetic** cipher — uses **multiple substitution alphabets**, making frequency analysis much harder.

Takes plaintext and a **keyword** as the key, using a grid (the Vigenère table):

1. Write the keyword **repeating** over the plaintext until it matches in length
2. For each letter: the **key letter** selects the column, the **plaintext letter** selects the row → the intersection is the ciphertext letter

**Example:**

```
Key:        HELLOHELLOHEL
Plaintext:  DEFORESTATION
Ciphertext: KIQZFLWELHPSY
```

**Stronger than Caesar** but still breakable — once you determine the **key length** (using techniques like Kasiski examination or index of coincidence), each position becomes a simple substitution cipher.

**Both are unusable today** — modern computing can crack them easily.

## Diffie-Hellman Key Exchange

**The problem:** two parties need a shared encryption key, but they're communicating over an **insecure channel**. A **pre-shared key** (shared in advance) works but requires a secure way to distribute it first.

**Diffie-Hellman** solves this: it allows two endpoints to **generate the same key without ever transmitting** data that would let an eavesdropper know the key.

### How it works (simplified)

Alice and Bob want to agree on a shared secret:

1. They publicly agree on the **same initial values** (a large prime number and a generator — these are public, anyone can see them)
2. Each **privately** picks a **random secret value** (Alice picks hers, Bob picks his — never shared)
3. Each combines their secret with the public values and **sends the result** to the other (this can be intercepted — it doesn't matter)
4. Each takes what they **received** and combines it with their **own secret value**
5. **Result:** both arrive at the **same final value** — this becomes the shared key

An eavesdropper sees the public values and the exchanged results, but can't derive the shared secret without one of the private values — this is the **discrete logarithm problem**, which is computationally hard.

**Key point:** Diffie-Hellman is a **key exchange** protocol, not an encryption algorithm. It generates a shared secret that is then used as a key for a symmetric cipher (like AES).

**Vulnerability:** Diffie-Hellman alone doesn't authenticate either party → vulnerable to a **man-in-the-middle** attack (the attacker performs separate key exchanges with each side). That's why it's combined with **authentication** (certificates, digital signatures) in practice.

---

# Cheatsheet

**Cipher types**

| Cipher | Type | Key | Weakness |
| --- | --- | --- | --- |
| **Caesar / rotation** | Monoalphabetic substitution | Number of positions rotated | Only 25 keys → brute force; frequency analysis |
| **ROT13** | Caesar with rotation = 13 | Fixed (13) | Same — trivial |
| **Vigenère** | Polyalphabetic substitution | A keyword | Once key length is found, each position is a simple substitution |

**Diffie-Hellman**

| Aspect | Detail |
| --- | --- |
| **Purpose** | Key exchange — agree on a shared secret over an insecure channel |
| **How** | Both sides combine public values + private secret → same result |
| **Security basis** | Discrete logarithm problem |
| **What it is NOT** | Not an encryption algorithm — it generates a key for one |
| **Weakness** | No authentication → vulnerable to **MITM** without certificates |

**Key terms**

- **Plaintext** = readable data · **Ciphertext** = encrypted data
- **Frequency analysis** = break substitution ciphers by letter distribution
- **Pre-shared key (PSK)** = key distributed in advance
- **Discrete logarithm problem** = the mathematical hardness that makes DH secure
- ROT13 is its own inverse (apply twice = original)

**Exam reflexes**

- "Rotate letters by a fixed number" → **Caesar / rotation cipher**
- "Multiple alphabets, uses a keyword" → **Vigenère**
- "Generate a shared key without transmitting it" → **Diffie-Hellman**
- "DH without authentication" → vulnerable to **MITM**
- "Only 25 possible keys" → Caesar → **brute force**
- "Statistical letter distribution" → **frequency analysis**

# Symmetric Key Cryptography

The key that comes out of **Diffie-Hellman** is a **symmetric key** — the **same key** is used to both encrypt and decrypt messages. Both parties must have it; if it's compromised, everything encrypted with it is exposed.

## Block vs Stream ciphers

Any symmetric key algorithm is either a **block** or a **stream** cipher:

| Type | How it works |
| --- | --- |
| **Block cipher** | Takes the data and divides it into **fixed-length blocks**, encrypting one block at a time. If the data isn't a multiple of the block size, the last block is **padded**. Common block size: 64 bits (8 single-byte characters) or 128 bits |
| **Stream cipher** | Encrypts the data **byte by byte** (or bit by bit) in a continuous stream. Faster for real-time data (voice, video) but harder to implement securely |

## DES (Data Encryption Standard)

A **block cipher** using symmetric encryption. **Long deprecated**.

| Property | Value |
| --- | --- |
| Block size | **64 bits** |
| Key length | **56 bits** (+ 8 parity bits = 64 bits total, but only 56 are key) |
| Status | **Broken** — 56-bit key is vulnerable to brute force |

### Triple DES (3DES)

An attempt to extend DES's life by running it **three times** with **three different keys**:

1. **Encrypt** with key 1
2. **Decrypt** with key 2 (this step actually further scrambles it — it's called **EDE**: Encrypt-Decrypt-Encrypt)
3. **Encrypt** with key 3

**Total key length:** 168 bits (3 × 56), but it's not a single 168-bit key — it's three separate 56-bit keys. Effective security is closer to **112 bits** due to certain attacks.

**Also deprecated** — slow (three passes) and the underlying DES algorithm is weak regardless of how many times you run it. Key length doesn't matter if the **algorithm itself is weak**.

**Still seen** on legacy servers and less capable devices that can't run AES.

## AES (Advanced Encryption Standard)

The **replacement for DES**. A block cipher that supports multiple key lengths.

| Property | Value |
| --- | --- |
| Block size | **128 bits** |
| Key lengths | **128, 192 or 256 bits** |
| Status | Current standard — **no practical attacks** exist |

AES-128 has shown **theoretical** vulnerabilities, so the industry has moved toward **longer key lengths** (AES-256 is now the recommendation for sensitive data).

## Cipher suite

A complete encryption setup uses **multiple components** working together — this combination is called a **cipher suite**:

| Component | Role |
| --- | --- |
| **Key exchange** | How both parties agree on a shared key (e.g. Diffie-Hellman, ECDHE) |
| **Encryption cipher** | The symmetric algorithm used to encrypt the data (e.g. AES-256) |
| **Message Authentication Code (MAC)** | Verifies integrity and authenticity of the message (e.g. SHA-256, SHA-384) |

Example cipher suite string: `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`

## sslscan

A tool that identifies **supported cryptographic ciphers** on servers using SSL/TLS:

```
sslscan target.com
```

Shows which cipher suites, protocols and key lengths a server accepts — useful for identifying **weak or deprecated configurations**.

## Attacks against symmetric encryption

| Attack | Target |
| --- | --- |
| **Side-channel attack** | The **implementation**, not the algorithm — analyses power consumption, CPU utilisation, timing, electromagnetic emissions to leak key information |
| **Related-key attack** | The attacker observes ciphertext encrypted with **several different but mathematically related keys** and uses the relationships to derive the key |
| **Key recovery attack** | The key is **obtained** (through a flaw, a leak, or computational attack) and used to decrypt ciphertext |
| **Brute force** | Try every possible key — only practical when the key space is small (DES 56-bit) |

**No practical attack exists against AES** — all known attacks are theoretical and don't work at scale. AES is considered secure.

---

# Cheatsheet

**Algorithms**

| Algorithm | Type | Block size | Key length | Status |
| --- | --- | --- | --- | --- |
| **DES** | Block | 64 bits | 56 bits | Broken — brute force |
| **3DES** | Block (3× DES) | 64 bits | 168 bits (3 × 56) | Deprecated — slow, weak base |
| **AES** | Block | 128 bits | 128 / 192 / 256 bits | **Current standard** |
| **RC4** | Stream | N/A | Variable | Broken — used by WEP/WPA |

**Block vs stream:** block = fixed-length chunks + padding · stream = byte by byte

**Cipher suite components:** key exchange + encryption cipher + MAC

**Attacks**

| Attack | What it targets |
| --- | --- |
| Side-channel | Implementation (power, timing, emissions) |
| Related-key | Multiple related keys |
| Key recovery | Obtain the key itself |
| Brute force | Small key space (DES) |

**Tool:** `sslscan target.com` — identify supported ciphers on SSL/TLS servers

**Key terms**

- **Symmetric** = same key encrypts and decrypts
- **Padding** = filling the last block to match the block size
- **EDE** = Encrypt-Decrypt-Encrypt (how 3DES works)
- **Cipher suite** = key exchange + cipher + MAC combined

**Exam reflexes**

- "56-bit key, 64-bit block" → **DES**
- "Three passes, three keys" → **3DES (EDE)**
- "128-bit block, 128/192/256-bit key" → **AES**
- "Byte-by-byte encryption" → **stream cipher**
- "Fixed-length blocks with padding" → **block cipher**
- "Analyse power consumption to find the key" → **side-channel attack**
- "No practical attack" → **AES**
- "Key length doesn't help if the algorithm is weak" → why 3DES is still deprecated despite 168-bit key

# Asymmetric Key Cryptography

Asymmetric cryptography uses **two keys** — a **public key** and a **private key**. Also called **public key cryptography**.

What's encrypted by one key can only be decrypted by the **other**. This solves the key transmission problem: only the **private key** needs protection. The public key can be shared openly — anyone can have it.

**Flow:** someone encrypts a message with your **public key** → only your **private key** can decrypt it. Even the sender can't decrypt it after encryption.

## RSA

The most widely known asymmetric algorithm. Keys are based on a pair of **large prime numbers** — the security relies on the fact that **factoring very large numbers** is computationally hard.

Key sizes: **1024, 2048 and 4096 bits**. 1024 is considered weak now; **2048** is the current minimum, **4096** for high-security use.

## Hybrid Cryptosystem

**The problem with asymmetric encryption:** it's **computationally expensive** — far slower than symmetric encryption. Encrypting large amounts of data with RSA is impractical.

**The problem with symmetric encryption:** the **key exchange** — how do you safely get the same key to both parties?

**Solution — combine both:**

1. The **web server** has a public/private key pair (the public key is in its certificate)
2. The **client** generates a **symmetric session key** (random, one-time use)
3. The client encrypts the session key with the server's **public key** and sends it
4. The server decrypts it with its **private key** — now both sides have the session key
5. All further communication uses the **fast symmetric cipher** with that session key

No Diffie-Hellman needed in this model — the public key handles the key exchange. This is the **hybrid cryptosystem**, and it's how most HTTPS communication works.

## Non-repudiation

Public and private keys belong **exclusively** to a person or system. Beyond encryption, asymmetric keys can be used to **sign messages**.

**How signing works:**

- The sender signs a message with their **private key**
- Anyone can verify the signature with the sender's **public key**
- If the signature verifies, it proves the message was generated by the **owner of that private key**

**Non-repudiation** = once a message has been signed with a private key, the owner **cannot deny** that the message originated with them. "I didn't send that" doesn't work when the signature verifies against your public key.

**This requires that the private key is protected:**

- The file containing the key should have **appropriate permissions** so only the owner can access it
- Using the key should require a **password/passphrase** — requested before the key can sign. Without the correct password, the signing fails
- If the private key is compromised, non-repudiation is broken — someone else could have signed

## Elliptic Curve Cryptography (ECC)

ECC is an alternative to RSA that achieves the **same security with much smaller keys** — reducing the computing power needed for encryption and decryption.

**How it works (simplified):** ECC is based on the mathematics of **elliptic curves** over finite fields. The security relies on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**: given two points on a curve, it's easy to compute the result of multiplying one point by a number, but **computationally infeasible to reverse** the process (figure out which number was used). There's no known shortcut — the process is effectively **one-way**.

The math involves **multivariable polynomial equations** with no standard approach to computing the discrete logarithm — which is what makes ECC hard to break.

**Key size comparison (equivalent security):**

| RSA key | ECC key | Security level |
| --- | --- | --- |
| 1024 bits | 160 bits | Low (deprecated) |
| 2048 bits | 224 bits | Standard |
| 3072 bits | 256 bits | Strong |
| 4096 bits | 384 bits | Very strong |

ECC achieves the **same security as RSA** with keys roughly **one-sixth the size** — faster, less bandwidth, less storage, better for constrained devices (mobile, IoT).

**ECDH (Elliptic Curve Diffie-Hellman)** = a key agreement protocol that uses a **Diffie-Hellman variant built on elliptic curve math**. Combines DH's key exchange with ECC's smaller keys and better performance. Common in modern TLS cipher suites.

**ECDHE** = the **ephemeral** version — a new key pair is generated for **every session**, providing **forward secrecy** (compromising one session key doesn't expose past or future sessions).

---

# Cheatsheet

**Asymmetric vs symmetric**

|  | Symmetric | Asymmetric |
| --- | --- | --- |
| Keys | **1** (same for encrypt/decrypt) | **2** (public + private) |
| Speed | Fast | Slow (computationally expensive) |
| Key exchange | Problem (how to share safely?) | Solved (public key is open) |
| Use case | Bulk data encryption | Key exchange, signatures, small data |
| Examples | AES, DES, 3DES | RSA, ECC |

**Algorithms**

| Algorithm | Type | Key sizes | Security basis |
| --- | --- | --- | --- |
| **RSA** | Asymmetric | 1024 / 2048 / 4096 bits | Factoring large primes |
| **ECC** | Asymmetric | 160–384 bits (equivalent to RSA) | Elliptic curve discrete logarithm |
| **ECDH** | Key exchange | ECC-based | DH + elliptic curves |
| **ECDHE** | Key exchange (ephemeral) | ECC-based | Same + **forward secrecy** |

**Concepts**

| Term | Meaning |
| --- | --- |
| **Hybrid cryptosystem** | Asymmetric encrypts the session key → symmetric encrypts the data |
| **Session key** | Temporary symmetric key for one session |
| **Non-repudiation** | Signer can't deny the message — private key proves origin |
| **Digital signature** | Sign with private key → verify with public key |
| **Forward secrecy** | New key per session → past sessions stay safe if key leaks (ECDHE) |
| **ECDLP** | Elliptic Curve Discrete Logarithm Problem — the hard math behind ECC |

**Hybrid flow:** client generates session key → encrypts it with server's **public key** → server decrypts with **private key** → both use session key (symmetric) for the rest.

**Exam reflexes**

- "Two keys, public and private" → **asymmetric / public key cryptography**
- "Keys based on large prime numbers" → **RSA**
- "Same security, smaller keys" → **ECC**
- "Can't deny sending the message" → **non-repudiation** (signed with private key)
- "Encrypt with public key, decrypt with private key" → **confidentiality**
- "Sign with private key, verify with public key" → **authentication / non-repudiation**
- "Symmetric is fast but key exchange is hard" → solved by **hybrid cryptosystem**
- "Ephemeral key exchange" → **ECDHE** → provides **forward secrecy**
- RSA 2048 ≈ ECC 224 in security · ECC keys are ~6× smaller

# Certificate Authorities and Key Management

## Why certificates exist

**Session keys** are temporary — the web server discards them after minutes. But keys tied to a **person or system** need to be **persistent** — they stick around.

Those persistent keys are stored inside a data structure called a **certificate**. The structure is defined by the **X.509** standard (part of the X.500 directory services standard). The **public key** lives inside the certificate, along with identity information (name, organisation, expiry date).

Think of a certificate as an **ID card for a public key** — it binds a key to an identity.

## Certificate Authority (CA)

A **CA** is the trusted entity that **issues, stores and manages certificates**. The whole system is called a **Public Key Infrastructure (PKI)**.

**How it works (simplified):**

1. A user or server **requests a certificate** from the CA, providing identity information
2. The CA **verifies the identity** (how thoroughly depends on the CA and certificate type)
3. The CA **generates the key pair** and creates the certificate
4. The CA **signs the certificate** with its own private key (this is the stamp of trust)
5. The certificate is issued — the user gets their **private key** (kept secret) and their **certificate** (containing the public key, shared freely)

When you look at a certificate, you don't see the raw key — you see a **fingerprint** (a fixed-length hexadecimal hash of the key).

**Software CA example:** **Simple Authority** — uses the **OpenSSL** library to generate certificates and handle storage/management through a GUI.

## Root certificate

The **root certificate** is the CA's own certificate — the one that **all other certificates are signed by**. It's the foundation of trust.

Root certificate generated → it can now **sign** (issue) other certificates. Every certificate in the CA chain traces back to this root.

## Certificate revocation

A CA can **revoke** a certificate — reasons include:

- The user **left the organisation**
- The email address is **no longer valid**
- The private key was **compromised**

Once revoked, **nobody should accept that certificate** — it's no longer trustworthy for encryption or identity verification.

**Two ways to check if a certificate is revoked:**

| Method | How it works | Problem |
| --- | --- | --- |
| **CRL** (Certificate Revocation List) | A list of revoked certificates published by the CA | Not always checked — clients may skip the request |
| **OCSP** (Online Certificate Status Protocol) | Real-time query to the CA: "is this certificate still valid?" | Better — gives a live answer, but adds a network request |

## Trusted Third Party

The advantage of a CA: it's a **central, trusted authority** that stores certificates, manages them, and **verifies identity**. Non-repudiation only works if the certificate **actually belongs to who it claims** — just providing a name and email isn't enough.

**Commercial CAs** (like DigiCert, formerly VeriSign) may require **photo identification** before issuing a certificate — real identity verification.

**Transitive trust:** if you trust a CA, and the CA verified someone's identity and signed their certificate, then you **also trust that person**. The trust flows through the CA.

**How it works in practice:**

- Every certificate issued by a CA is **signed by the CA's root certificate** (using the CA's private key → digital signature)
- To verify that signature, your system needs the **CA's root certificate** in its **certificate store/cache**
- Your browser and OS ship with dozens of **pre-installed root certificates** from trusted CAs — that's why HTTPS "just works" for major sites
- If a certificate is signed by a CA whose root you **don't** have, you get a certificate warning

## Self-signed Certificates

Sometimes you just need encryption between two systems you control (a lab, a dev environment) — you don't need a CA to verify your identity to yourself.

A **self-signed certificate** is one you **generate and sign yourself** — no CA involved. It provides **encryption** but **no third-party trust**, so browsers show a **certificate error** (the signature can't be verified against any trusted root).

**Generate a self-signed certificate:**

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
```

| Flag | Meaning |
| --- | --- |
| `req` | Certificate request utility |
| `-x509` | Output a self-signed certificate (not just a request) |
| `-newkey rsa:4096` | Generate a new RSA key pair, 4096 bits |
| `-keyout key.pem` | Save the **private key** to this file |
| `-out cert.pem` | Save the **certificate** (with public key) to this file |
| `-days 365` | Certificate valid for 1 year |

**View certificate details:**

```
openssl x509 -in cert.pem -text
```

---

# Cheatsheet

**PKI components**

| Component | Role |
| --- | --- |
| **Certificate** | Binds a public key to an identity (X.509 format) |
| **CA** (Certificate Authority) | Issues, manages and revokes certificates |
| **Root certificate** | The CA's own cert — signs all others, foundation of trust |
| **PKI** (Public Key Infrastructure) | The whole system: CA + certificates + policies |
| **CRL** | List of revoked certificates (not always checked) |
| **OCSP** | Real-time "is this cert still valid?" query |

**Trust model**

| Concept | Meaning |
| --- | --- |
| **Transitive trust** | Trust the CA → trust the certificates it signed |
| **Root store** | Pre-installed CA roots in your browser/OS |
| **Self-signed** | You sign your own cert — encryption works, but no third-party trust → browser warning |
| **Fingerprint** | Fixed-length hex hash of the key (what you see in the certificate) |

**Revocation:** CRL = published list (may be stale) · OCSP = live check (better)

**Commands**

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365   # generate self-signed
openssl x509 -in cert.pem -text                                                # view certificate details
```

**Exam reflexes**

- "Binds a public key to an identity" → **X.509 certificate**
- "Issues and manages certificates" → **CA**
- "Signs all other certificates" → **root certificate**
- "Real-time certificate validation" → **OCSP**
- "Published list of revoked certificates" → **CRL**
- "Certificate error in browser" → likely **self-signed** or signed by an untrusted CA
- "I trust the CA, the CA trusts Bob" → **transitive trust** → I trust Bob
- "Can't deny sending the message" → **non-repudiation** (requires valid certificate + private key)

# Cryptographic Hashing

A **Message Authentication Code (MAC)** verifies that a message received is the **same as what was sent** — it's a **fixed-length value** generated by running the entire message through a cryptographic algorithm.

The output = a **hash** (also called a digest or fingerprint).

## Key properties of a hash

- Takes **arbitrary-length input** → produces **fixed-length output**
- **Not linear** — changing a single bit generates a **completely different** hash value
- **One-way** — you can't reverse the hash to get the original input
- **Deterministic** — the same input always produces the same hash

## Collision

Every unique input **should** yield a different hash. When **two different inputs produce the same output** → that's a **collision**.

Collisions can be exploited — an attacker could substitute one input for another and the hash wouldn't catch it. This is related to the **birthday paradox**: the probability of a collision becomes likely much sooner than you'd expect as the number of inputs grows (like the surprisingly high chance that two people in a room share a birthday).

**Defence against collisions:** use a **larger output size** — more possible hash values = collisions are harder to find.

## Hash algorithms

| Algorithm | Output size | Hex characters | Status |
| --- | --- | --- | --- |
| **MD5** | 128 bits | 32 | **Deprecated** — collisions demonstrated |
| **SHA-1** | 160 bits | 40 | **Deprecated** — collisions demonstrated (2017) |
| **SHA-2** | 224 / 256 / 384 / 512 bits | Varies | **Current standard** |
| **SHA-3** | 224 / 256 / 384 / 512 bits | Same sizes as SHA-2 | **Current standard** — different internal design from SHA-2 |

**MD5 → SHA-1 → SHA-2 → SHA-3**: each generation provides **better protection against collisions** through larger output sizes and improved algorithms.

SHA-2 and SHA-3 support the **same output sizes** but differ internally — SHA-3 uses a completely different construction (sponge function), so a flaw in SHA-2's design wouldn't affect SHA-3.

**Tool:** `md5sum` (Linux) to compute an MD5 hash of a file.

## What hashing is used for

### Message authentication

The hash is **transmitted alongside the message**. The receiver computes their own hash of the received message and **compares** it to the one that was sent:

- **Match** → message is authentic, hasn't been altered
- **Mismatch** → the message has been **tampered with** and should be **discarded**

### File integrity

Hash a file and compare it to what it **should** be to detect any changes. Used by **file integrity monitoring** systems like **Tripwire**:

1. Essential system files have their hashes **computed and stored** in a baseline
2. The software **periodically rechecks** those files
3. If any hash doesn't match the stored value → the file has been **modified** → alert

Also used to verify downloaded software — compare the hash on the download page to the hash of the file you received.

---

# Cheatsheet

**Algorithms**

| Algorithm | Output | Status |
| --- | --- | --- |
| **MD5** | 128 bits / 32 hex | Deprecated (collisions) |
| **SHA-1** | 160 bits / 40 hex | Deprecated (collisions) |
| **SHA-2** | 224/256/384/512 bits | Current standard |
| **SHA-3** | 224/256/384/512 bits | Current standard (different design from SHA-2) |

**Key terms**

| Term | Meaning |
| --- | --- |
| **Hash / digest** | Fixed-length output of a cryptographic algorithm |
| **MAC** | Hash transmitted with the message for authentication |
| **Collision** | Two different inputs produce the same hash |
| **Birthday paradox** | Collisions become likely sooner than expected |
| **Tripwire** | File integrity monitoring — stores hashes, checks periodically |

**Uses of hashing:** message authentication (MAC) · file integrity (Tripwire) · password storage · digital signatures · verifying downloads

**Tool:** `md5sum <file>` — compute MD5 hash

**Exam reflexes**

- "Fixed-length output from any input" → **hash**
- "Two inputs, same output" → **collision**
- "128 bits, 32 hex characters" → **MD5**
- "160 bits, 40 hex characters" → **SHA-1**
- "Periodically checks system file hashes" → **Tripwire** (file integrity)
- "Hash sent with message to verify integrity" → **MAC**
- "Same output sizes but different internal design" → **SHA-2 vs SHA-3**
- MD5 and SHA-1 are **deprecated** — use SHA-2 or SHA-3

# PGP and S/MIME

## PGP (Pretty Good Privacy)

Another way to manage certificates — **without a CA** for centralised verification. Instead, PGP uses a **web of trust**.

### How the web of trust works

There's no central authority deciding who to trust. Instead, **users vouch for each other**:

1. Lily creates a key pair (based on X.509, with a public key) and **uploads her public key** to a PGP key server
2. You know Lily personally — you go to the key server, verify it's really her key, and **sign it with your private key**
3. Now anyone who **trusts you** (but doesn't know Lily) can see your signature on her key and be **assured it's legitimate**
4. The more people who sign a key, the more trusted it becomes

Trust is **distributed and cumulative** — no single point of authority, just a chain of people vouching for each other.

### Key management

Keys are stored in a **keyring** — signed with your own key and protected with a **MAC** (message authentication code) to ensure it hasn't been tampered with.

### Downside

Keys are **managed entirely by the users** — you need to keep track of your keyring, keep your private key with you, and manage revocations yourself. There's no IT department doing it for you. PGP is designed for **individual users**, not for servers.

## S/MIME (Secure/Multipurpose Internet Mail Extensions)

A protocol for sending **encrypted email messages**. The standard is generally **implemented directly in email clients** (Outlook, Thunderbird, Apple Mail).

|  | PGP | S/MIME |
| --- | --- | --- |
| **Trust model** | Web of trust (users sign each other's keys) | **Certificate Authority** (centralised) |
| **Certificates** | User-managed, uploaded to key servers | X.509 from a CA — can be installed in **Active Directory** |
| **Key management** | User's responsibility | Managed by the organisation / CA |
| **Best for** | Individual users, personal email | **Enterprise** environments |

---

# Cheatsheet

| Term | Meaning |
| --- | --- |
| **PGP** | Encryption using a **web of trust** — users vouch for each other, no CA |
| **Web of trust** | Distributed trust: I sign your key, people who trust me now trust you |
| **Keyring** | Local store of your keys, signed + MAC-protected |
| **S/MIME** | Encrypted email using **X.509 certificates from a CA** — enterprise standard |
| **Key server** | Public server where PGP keys are uploaded and retrieved |

**Exam reflexes**

- "No central authority, users sign each other's keys" → **PGP / web of trust**
- "Encrypted email using certificates from a CA" → **S/MIME**
- "Keys managed by users, uploaded to a server" → **PGP**
- "Certificates installed in Active Directory" → **S/MIME**
- "Enterprise email encryption" → **S/MIME**
- "Individual, decentralised key management" → **PGP**

# Disk and File Encryption

## Three states of data

| State | Where it is | Encryption approach |
| --- | --- | --- |
| **In motion** | Being transmitted from one location to another (over a network) | TLS, IPSec, VPN — encrypt the transit |
| **In use** | Being acted on by an application, typically **in memory** | **Cannot be encrypted** while being processed — developers use techniques like **data masking** to limit exposure |
| **At rest** | Stored on a disk or tape | Must be encrypted — **file-level** or **full-disk** encryption |

## Encryption at rest

### File-level encryption

Encrypts **individual files or folders** — handled by many utilities (7-Zip, VeraCrypt containers, EFS on Windows, GPG). You choose what to encrypt; the rest stays unencrypted.

### Full-disk encryption (FDE)

Encrypts the **entire disk** — everything on it is ciphertext until unlocked. Requires **authentication before boot** to unlock the decryption key. Without the key, the disk is unreadable — even if it's physically removed.

## TPM (Trusted Platform Module)

A **cryptographic processor on a chip** — dedicated hardware built into the motherboard that can **store encryption keys** securely. The keys never leave the chip in plaintext.

TPM handles the decryption key for FDE: at boot, the TPM checks the system's integrity (boot components haven't been tampered with) and releases the key automatically. If the disk is moved to another machine (different TPM), it won't unlock.

## OS-specific implementations

| OS | Tool | Details |
| --- | --- | --- |
| **Windows** | **BitLocker** | Full-disk encryption. Decryption key stored in the **TPM** if available; a recovery key can also be stored in **Active Directory**. Uses AES |
| **macOS** | **FileVault** | Built into the OS. Uses **AES** to encrypt volumes. Recovery key can be stored in iCloud or with the organisation |
| **Linux** | **dm-crypt + LUKS** | **dm-crypt** resides in the Linux kernel (the encryption engine). **LUKS** (Linux Unified Key Setup) is the standard format on top of it — manages the key slots and header |

---

# Cheatsheet

**Data states**

| State | Protection |
| --- | --- |
| In motion | TLS, IPSec, VPN |
| In use | Data masking (can't encrypt in memory) |
| At rest | File-level or full-disk encryption |

**FDE tools**

| OS | Tool | Key storage |
| --- | --- | --- |
| Windows | **BitLocker** | TPM + AD recovery key |
| macOS | **FileVault** | AES, recovery via iCloud/org |
| Linux | **dm-crypt / LUKS** | Kernel-level, LUKS key slots |

**Key terms**

| Term | Meaning |
| --- | --- |
| **FDE** | Full-disk encryption — entire disk is ciphertext until unlocked |
| **TPM** | Hardware crypto chip — stores keys securely, checks boot integrity |
| **LUKS** | Linux Unified Key Setup — standard key management for dm-crypt |
| **Data masking** | Obscure data in use (since it can't be encrypted in memory) |

**Exam reflexes**

- "Crypto chip on the motherboard that stores keys" → **TPM**
- "Windows full-disk encryption" → **BitLocker**
- "macOS full-disk encryption" → **FileVault**
- "Linux full-disk encryption" → **dm-crypt + LUKS**
- "Data in use can't be encrypted" → correct — use **data masking** instead
- "Recovery key stored in Active Directory" → **BitLocker**
- "Disk moved to another machine won't unlock" → **TPM** ties the key to the hardware