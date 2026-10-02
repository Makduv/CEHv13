# Module 20: Cryptography

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
This is the **final module** of the CEH v13 curriculum.
> 

---

## 📋 Table of Contents

1. [Cryptography Concepts and Encryption Algorithms](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-cryptography-concepts-and-encryption-algorithms)
2. [Applications of Cryptography](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-applications-of-cryptography)
3. [Cryptanalysis Methods and Cryptography Attacks](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-cryptanalysis-methods-and-cryptography-attacks)
4. [Cryptography Attack Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-cryptography-attack-countermeasures)
5. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-quick-exam-cheat-sheet)

---

## 1. Cryptography Concepts and Encryption Algorithms

### 🎯 What is Cryptography?

> From Greek *kryptos* ("concealed/hidden/secret") + *graphia* ("writing") = "the art of secret writing." Cryptography converts **plaintext** (readable) into **ciphertext** (unreadable) using a key or encryption scheme.
> 

### 🎯 4 Objectives of Cryptography (CRITICAL)

```
Confidentiality  — information accessible only to authorized parties
Integrity        — trustworthiness of data; prevents improper/unauthorized changes
Authentication   — assurance that communication/document/data is genuine
Nonrepudiation   — sender cannot deny sending; recipient cannot deny receiving
```

---

### 🔑 Symmetric vs Asymmetric Encryption

| Type | Description |
| --- | --- |
| **Symmetric Encryption** (secret-key, shared-key, private-key) | Uses the SAME key for encryption and decryption |
| **Asymmetric Encryption** (public-key) | Uses DIFFERENT keys — public key (encryption) and private key (decryption) |

**Strengths/Weaknesses of crypto methods:** Provides no assurance about origin/authenticity if same key used by both parties; messages cannot be decrypted if private key is lost; vulnerable to dictionary/brute-force attacks; vulnerable to MITM and brute-force attacks.

---

### 🏛️ Government Access to Keys (GAK)

> Statutory obligation of individuals/organizations to disclose cryptographic keys to government agencies. Uses **Key Escrow** — a key exchange arrangement where essential cryptographic keys are stored with a third party (often a government agency) that may use the keys to decipher digital evidence under court authorization.
> 

---

### 📊 Symmetric Encryption Algorithms (CRITICAL TABLE)

| Algorithm | Cipher Type | Key Size (bits) | Block Size (bits) | Application Areas |
| --- | --- | --- | --- | --- |
| **DES** | Block | 56 | 64 | Legacy systems, early encryption standards |
| **3DES** | Block | 112, 168 | 64 | Financial services, payment systems |
| **AES** | Block | 128, 192, 256 | 128 | Secure comms, storage encryption, gov standards |
| **RC4** | Stream | 40 to 2048 (variable) | - | HTTPS, Wi-Fi (WEP/WPA), streaming encryption |
| **RC5** | Block | 0 to 2040 (variable) | 32, 64, 128 | Cryptographic libraries, secure communication |
| **RC6** | Block | 128, 192, 256 | 128 | Advanced encryption, AES competition finalist |
| **Blowfish** | Block | 32 to 448 (variable) | 64 | Replacement for DES, secure storage |
| **Twofish** | Block | 128, 192, 256 | 128 | File/disk encryption, open-source software |
| **IDEA** | Block | 128 | 64 | Secure email (PGP), data encryption |
| **Threefish** | Block | 256, 512, 1024 | 256, 512, 1024 | Disk encryption (Skein hash function) |
| **Serpent** | Block | 128, 192, 256 | 128 | High-security apps, AES competition finalist |
| **Camellia** | Block | 128, 192, 256 | 128 | Secure comms, Japanese encryption standard |
| **TEA** | Block | 128 | 64 | Lightweight encryption, embedded systems |
| **CAST-128** | Block | 40 to 128 | 64 | Various software apps, secure communications |
| **CAST-256** | Block | 128, 160, 192, 224, 256 | 128 | Advanced encryption, cryptographic libraries |
| **ChaCha20** | Stream | 256 | - | Secure comms, modern encryption protocols |
| **Salsa20** | Stream | 256 | - | Secure comms, cryptographic protocols |

**Key algorithm notes:**

- **RC4:** Variable key-size stream cipher, byte-oriented, based on random permutation; enables SSL secure traffic
- **RC5:** Fast symmetric block cipher (Ronald Rivest); parameterized — variable block size (32/64/128), variable key size (0-2040 bits), variable rounds (0-255); routines: key expansion, encryption, decryption; operations: integer addition, bitwise XOR, variable rotation
- **Twofish:** One of 5 AES finalists (not chosen); designed by Bruce Schneier et al.; 128-bit block, Feistel cipher, single key for enc/dec up to 256 bits
- **Threefish:** Part of Skein algorithm (SHA-3 contest); uses ARX (Addition-Rotation-XOR) operations only; no S-boxes (prevents cache timing attacks)
- **Serpent:** AES finalist; 32 rounds, substitution+permutation on four 32-bit word blocks, 8 variable S-boxes; Rijndael chosen over Serpent for better speed/complexity balance
- **TEA:** Created by David Wheeler & Roger Needham (1994); Feistel cipher, 64 rounds (implemented in pairs called cycles); 128-bit key, 64-bit block; uses constant delta = 2^32/golden ratio

---

### 🔢 DES, RSA, Diffie-Hellman, DSA — Key Algorithms

**RSA Key Generation:**

```
1. Generate two large distinct primes p and q (same bit length)
2. Compute n = pq and φ = (p-1)(q-1)
3. Choose random integer e, 1 < e < φ, such that gcd(e, φ) = 1
4. Use extended Euclidean algorithm to compute unique integer d, 1 < d < φ,
   such that ed ≡ 1 (mod φ)
5. Public key = (n, e); Private key = d
6. Destroy p and q
```

**RSA Signature:** Generation: `s = m̃^d mod n`. Verification: `m̃ = s^e mod n`, recover `m = R^-1(m̃)`.

**Diffie-Hellman Algorithm:**

```
Parameters: p (prime number), g (generator, integer < p)
1. Alice generates private value a; Bob generates private value b
2. Alice's public value = g^a mod p; Bob's public value = g^b mod p
3. Exchange public values
4. Alice computes g^ab = (g^b)^a mod p; Bob computes g^ba = (g^a)^b mod p
5. Since g^ab = g^ba = k → shared secret key k
```

> Does NOT provide authentication for key exchange; vulnerable to attacks, but is the basis for many authentication mechanisms (e.g., forward secrecy in TLS ephemeral modes).
> 

**DSA (Digital Signature Algorithm):** Public-key cryptosystem. Uses private key for **signature generation** and public key for **signature verification**. Benefits: less forgery chance vs written signature, quick business transactions, mitigates fake currency.

**Elliptic Curve Cryptography (ECC):** Modern public-key cryptography avoiding large key usage; depends on number theory and elliptic curves (algebraic structure) to generate short, quick, robust keys — proposed as RSA replacement to minimize key size.

---

### 📊 Message Digest Functions / Hash Functions (CRITICAL TABLE)

| Algorithm | Output Size (bits) | Block Size (bits) | Rounds | Operations | Security (bits) | Application |
| --- | --- | --- | --- | --- | --- | --- |
| **MD2** | 128 | 128 | 18 | Permutation, Substitution | 128 | Legacy, checksum validation |
| **MD4** | 128 | 512 | 48 | Logical ops (AND/OR/XOR) | 64 | Obsolete, early hash functions |
| **MD5** | 128 | 512 | 64 | Logical ops (AND/OR/XOR) | 64 | File verification, checksum, digital signatures |
| **MD6** | 224-512 | 512 | Variable | Logical ops | 128-256 | Cryptographic apps, data integrity |
| **SHA-0** | 160 | 512 | 80 | Bitwise logical ops | 0 | Obsolete, replaced by SHA-1 |
| **SHA-1** | 160 | 512 | 80 | Bitwise logical ops | 80 | Legacy systems, software updates, TLS |
| **SHA-2** | 224-512 | 512, 1024 | 64, 80 | Logical ops (AND/OR/XOR) | 112-256 | Secure apps, digital signatures, SSL |
| **SHA-3** | 224-512 | 1088, 576 | Variable | Sponge construction | 112-256 | Secure apps, next-gen crypto functions |
| **RIPEMD-160** | 160 | 512 | 160 | Logical ops | 80 | Cryptographic apps, data integrity |
| **WHIRLPOOL** | 512 | 512 | 10 | Matrix ops, substitution | 256 | Secure hashing, cryptographic apps |
| **Tiger** | 192 | 512 | 24 | Logical ops | 192 | High-speed apps, checksum validation |
| **BLAKE2** | 256, 512 | 512, 1024 | 10-14 | Logical ops | 128-256 | High-speed hashing, secure apps |
| **BLAKE3** | 256 | 512 | Variable | Logical ops | 128 | High-performance cryptographic apps |

> MD2/MD4/MD5/MD6 are used in digital signature applications to compress a document securely before signing with a private key. MD2 supports 8-bit machines; MD4/MD5 support 32-bit machines.
> 

---

### 🧊 Cipher Modes of Operation (4 Modes — CRITICAL)

| Mode | Description |
| --- | --- |
| **Electronic Code Book (ECB)** | Simplest mode; plaintext divided into fixed blocks (= key size), each independently encrypted with the secret key. **Flaw:** identical plaintext blocks → identical ciphertext blocks (pattern leakage) |
| **Cipher Block Chaining (CBC)** | Improves ECB; first block XORed with Initialization Vector (IV) then encrypted; each subsequent block XORed with the PREVIOUS ciphertext block before encryption. **Flaw:** error in one block propagates to subsequent blocks |
| **Cipher Feedback (CFB)** | IV stored in shift register, encrypted, first S bits XORed with plaintext block of size S to form ciphertext; previous cipher block feeds the shift register for next block. Makes cryptanalysis difficult (some data loss via shift registers) |
| **Counter Mode (CTR)** | Uses a counter value (not previous ciphertext) as input to encryption algorithm with secret key, XORed with plaintext. **Eliminates error propagation** since it doesn't use previously generated ciphertext. Requires synchronized counter values on both sides |

---

### 🌟 Modern & Advanced Encryption Techniques

- **Homomorphic Encryption:** Allows math operations on encrypted data WITHOUT decrypting first. Unlike private-key (only keyholder generates/decrypts) or public-key (public keyholder generates, secret keyholder decrypts) encryption — in homomorphic encryption, the **keyholder can generate ciphertext AND anyone can alter the ciphertext**, but only the keyholder can decrypt. Enables secure outsourced cloud processing.
- **Post-Quantum Cryptography:** Also called quantum-resistant/quantum-proof cryptography; advanced (mostly public-key based) algorithms designed to protect against both conventional AND quantum computer attacks.
- **Lightweight Cryptography:** Compact, quantum-safe algorithms for low-powered devices (RFID tags, sensor-based/IoT applications) — less power/resources without compromising security.

---

## 2. Applications of Cryptography

### 🏗️ Public Key Infrastructure (PKI) — 6-Step Process (CRITICAL)

> PKI binds public keys with respective user identities via a Certificate Authority (CA).
> 

```
1. Subject (user/company/system) applies for a certificate to the
   Registration Authority (RA)
2. RA verifies subject's identity, requests CA to issue a public key certificate
3. CA issues the public key certificate (binds subject's identity with
   subject's public key); updated info sent to Validation Authority (VA)
4. User signs message digitally using PRIVATE key; sends message +
   public key certificate to client
5. Client verifies authenticity by inquiring with VA about validity of
   user's public key certificate
6. VA compares the public key certificate with CA's updated info →
   determines certificate validity (valid/invalid)
```

---

### ✍️ Digital Signature for Email Security (Sign/Seal/Deliver/Accept/Open/Verify)

```
SIGN:    Confidential info → Hash value → Sender signs hash with PRIVATE key
         → Append signed hash to message
SEAL:    Encrypt message using one-time symmetric key → Encrypt the
         symmetric key using recipient's PUBLIC key
DELIVER: Mail electronic envelopes to recipient
ACCEPT:  Recipient's laptop accepts the sealed envelope
OPEN:    Recipient decrypts one-time symmetric key using PRIVATE key →
         Decrypt message using one-time symmetric key
VERIFY:  Unlock hash value using sender's PUBLIC key → Rehash message
         and compare with attached hash value
```

---

### 🔒 SSL/TLS

**TLS Record Protocol:** Fragments outgoing data into blocks, reassembles incoming data; optionally compresses/decompresses; applies MAC to outgoing data (verifies incoming); encrypts outgoing/decrypts incoming data; sends to TCP layer.

**TLS Handshake Protocol — Steps (CRITICAL):**

```
1. Client sends "Client hello" (client random value + supported cipher suites)
2. Server responds "Server hello" (server random value)
3. Server sends its certificate; may request client's certificate;
   sends "Server hello done"
4. Client sends its certificate, if requested
5. Client generates random pre-master secret, encrypts with server's
   public key, sends to server
6. Server receives pre-master secret; both sides derive master secret
   and session keys
7. Client sends "Change cipher spec" (start using new session keys) +
   "Client finished"
```

> Provides connection security with 3 properties: peer identity authenticated via asymmetric crypto; shared secret negotiation is secure; negotiation is reliable.
> 

---

### 📧 PGP (Pretty Good Privacy)

> Each step of PGP encryption (hashing, data compression, symmetric-key crypto, public-key crypto) uses one of various supported algorithms.
> 

**PGP Decryption:** Encrypted key decrypted using receiver's private key (RSA) → random (session) key recovered → data decrypted using random key.

**Web of Trust (WOT):** PGP's trust model — Direct Trust (between two known parties) and Indirect Trust (trust propagated through mutual connections) form a trust network without needing a central CA.

**Email encryption tools:** FlowCrypt (Gmail PGP), Outlook Security Settings (Encrypt contents and attachments for all outgoing messages).

---

### 💾 Disk Encryption

**Tools:** BitLocker Drive Encryption (Windows), Symantec Encryption, SafeGuard Enterprise Encryption, GiliSoft Full Disk Encryption, Check Point Full Disk Encryption, DiskCryptor.

**Linux tool — Cryptsetup:** Convenient utility to set up disk encryption based on DMCrypt kernel module. Includes plain dm-crypt volumes, LUKS volumes, loop-AES, TrueCrypt (incl. VeraCrypt extension), BitLocker formats.

---

### ⛓️ Blockchain

> A **distributed ledger technology (DLT)** used to record/store transaction history in blocks; multiple blocks cryptographically linked = "blockchain." Data is resistant to unwanted modifications; account transparency maintained cryptographically.
> 

**Process of Creating a Blockchain (5 steps):**

```
1. Participant requests a transaction
2. A block is created and broadcast to network members
3. Members validate the transaction
4. The block is added to the blockchain
5. A copy of the shared ledger is generated and made available to all members
```

**Blockchain mechanism:** Uses hash functions (mostly SHA-256) and asymmetric key algorithms. Validating blocks = "**proof of work**" (miners compensated). Adding validated blocks = "**crypto mining**." Each block = data (transaction details) + hash + hash of previous block. First block = "**genesis**" block (represented by 0s).

### 📊 4 Types of Blockchain

| Type | Description | Examples |
| --- | --- | --- |
| **Public Blockchain** | Everyone can participate in validation; blocks immutable once created; suitable for B2C | Bitcoin, Ethereum |
| **Private Blockchain** | Central authority/supervisor decides who can join; only involved members see ledgers; suitable for B2B | Hyperledger, Ripple (XRP) |
| **Federated (Consortium) Blockchain** | Partially decentralized; group of trusted/predetermined nodes manages network; fast and scalable | EWF (Energy), R3 (banks) |
| **Hybrid Blockchain** | Combination of private + public; select data publicly accessible, rest kept private | IBM Food Trust |

---

## 3. Cryptanalysis Methods and Cryptography Attacks

### 🎯 What is Cryptanalysis?

> The study of ciphers, ciphertext, or cryptosystems to identify vulnerabilities and extract plaintext from ciphertext even without knowledge of the key/algorithm.
> 

---

### 📊 4 Cryptanalysis Methods (CRITICAL)

| Method | Description |
| --- | --- |
| **Linear Cryptanalysis** | Invented by Mitsuru Matsui; known-plaintext attack using linear approximation to describe block cipher behavior; e.g., `P1 ⊕ P3 ⊕ C1 = K2`. Requires ~2^43 known plaintexts (vs 2^56 brute-force for DES) |
| **Differential Cryptanalysis** | Invented by Eli Biham & Adi Shamir; applicable to symmetric-key algorithms; examines differences in input and resultant output differences. Originally chosen-plaintext only; now also works with known-plaintext and ciphertext-only |
| **Integral Cryptanalysis** | First described by Lars Knudsen; useful against block ciphers based on substitution-permutation networks (extension of differential cryptanalysis). For block size b, holds b-k bits constant, runs other k bits through all 2^k possibilities |
| **Quantum Cryptanalysis** | Cracking cryptographic algorithms using a quantum computer. Uses Shor's algorithm (factor large numbers, breaks RSA/ECDH) and Grover's algorithm (faster brute-force key search for AES/SHA). Resources needed: Circuit Width, Circuit Depth, Number of Gates, Number of T-Gates, T-Depth, MAXDEPTH |

---

### 📊 Code Breaking Methodologies

```
Brute Force        — try every possible key combination
Frequency Analysis  — study frequency of letters/groups in ciphertext (e.g., 'e' common in English)
Trickery and Deceit — social engineering techniques to extract keys
One-Time Pad        — many non-repeating groups of letters/numbers, chosen randomly
```

**Brute-force time estimate table (illustrative):**

| Power/Cost | 40 bits | 56 bits | 64 bits | 128 bits |
| --- | --- | --- | --- | --- |
| $2K (1 PC) | 1.4 min | 73 days | 50 years | 10^20 years |
| $100K (company) | 2 sec | 35 hours | 1 year | 10^19 years |
| $1M (state/org) | 0.2 sec | 3.5 hours | 37 days | 10^18 years |

---

### 📊 12 Cryptography Attacks (CRITICAL TABLE — MEMORIZE)

| Attack | Description |
| --- | --- |
| **Ciphertext-only Attack** | Attacker has access only to the ciphertext; goal is to recover the encryption key from it |
| **Adaptive Chosen-plaintext Attack** | Attacker makes a series of interactive queries, choosing subsequent plaintexts based on info from previous encryptions |
| **Chosen-plaintext Attack** | Attacker defines their own plaintext, feeds it into the cipher, and analyzes the resulting ciphertext |
| **Related-Key Attack** | Attacker obtains ciphertexts encrypted under two different (but related) keys; useful with matching plaintext/ciphertext |
| **Dictionary Attack** | Attacker constructs a dictionary of plaintext along with corresponding ciphertext learned over time |
| **Known-plaintext Attack** | Attacker has knowledge of some part of the plaintext; deduces the key to decipher other messages |
| **Chosen-ciphertext Attack** | Attacker obtains plaintexts corresponding to an arbitrary set of ciphertexts of their own choosing |
| **Rubber Hose Attack** | Extraction of cryptographic secrets (e.g., passwords) from a person via **coercion or torture** |
| **Chosen-key Attack** | Attacker breaks an n-bit key cipher into 2^(n/2) operations |
| **Timing Attack** | Based on repeatedly measuring the exact execution times of modular exponentiation operations |
| **Man-in-the-Middle Attack** | Performed on public key cryptosystems where key exchange is required before communication |

---

### 🎯 Other Specific Cryptography Attacks

| Attack | Description |
| --- | --- |
| **Side-Channel Attack** | Physical attack on a cryptographic device/system exploiting environmental factors: Power Consumption (SPA/DPA), Electromagnetic Field, Light Emission, Timing, Sound |
| **Hash Collision Attack** | Finds two different input messages that result in the SAME hash output (`hash(a1) = hash(a2)`); exploits digital signatures to forge a signature on a different message |
| **DROWN Attack** | (Decrypting RSA with Obsolete and Weakened eNcryption) Cross-protocol weakness; attacker decrypts latest TLS connection by launching malicious SSLv2 probes using same private key; server vulnerable if it permits SSLv2 or shares private key cert with an SSLv2-permitting server |
| **Rainbow Table Attack** | Uses a precomputed table (rainbow table) containing word lists (dictionary/brute-force) and their hash values; cryptanalytic time-memory trade-off technique; attacker computes hash of possible passwords and compares to table |
| **Birthday Attack** | Class of brute-force attacks against cryptographic hashes; based on the birthday paradox — probability of 2+ people sharing a birthday in a group of 23 is >0.5 |
| **Brute-Forcing VeraCrypt Encryption** | Uses `dd.exe` to extract hash value from encrypted container, then `hashcat`/John the Ripper to brute-force: `dd.exe if=<container> of=<hashfile.tc> bs=512 count=1` then `hashcat.exe -a 3 -w 1 -m 13721 <hashfile.tc> ?d?d?d?d` |
| **Race Attack** (blockchain) | Attacker creates two transactions using the same coins; broadcasts Transaction A to victim, then immediately broadcasts Transaction B to network hoping it confirms first — if B confirms first, A is invalidated, attacker keeps goods AND cryptocurrency |
| **DeFi Sandwich Attack** | Targets DEXs/AMMs; attacker finds a large pending transaction in the mempool, places a buy order before it (inflating price), victim's transaction executes at inflated price |

---

## 4. Cryptography Attack Countermeasures

### 🛡️ How to Defend Against Cryptographic Attacks (12-Point Checklist — CRITICAL)

1. Access to cryptographic keys should be given directly to the application or user
2. IDS should be deployed to monitor exchange and access of keys
3. Passphrases/passwords must be used to encrypt the key, if stored on disk
4. Keys should NOT be present inside the source code or binaries
5. For certificate signing, transfer of private keys should NOT be allowed
6. For symmetric algorithms, key size of **256 bits** should be preferred (esp. large transactions)
7. Message authentication must be implemented for encryption of symmetric-key protocols
8. For asymmetric algorithms, key sizes of at least **2048 bits** should be considered
9. For hash algorithms, hash length of **256 bits or higher** should be considered
10. Recommended tools/products should be preferred over self-engineered crypto algorithms
11. Avoid simple encryption key relationships — each key should be created from a KDF
12. Output of the hash function should have a higher bit length, making it difficult to decrypt

---

### 🔑 Key Stretching

> Process of strengthening a key that might be slightly too weak, usually by making it longer — defends against brute-force attacks.
> 

| Function | Description |
| --- | --- |
| **PBKDF2** (Password-Based Key Derivation Function 2) | Part of PKCS #5 v.2.01; applies a function (hash or HMAC) to the password/passphrase along with **Salt** to produce a derived key |
| **Bcrypt** | Used with passwords; uses a derivation of the **Blowfish algorithm**, converted to a hashing algorithm, to hash a password and add Salt |

---

### 🛡️ Blockchain Security Countermeasures

```
Boost mining pool surveillance | Avoid storing blockchain keys in unsecured files |
Use trusted encryption programs to store keys | Implement randomized peer selection |
Implement timeouts for peer connections | Maintain secondary trusted communication channels |
Use out-of-band verification methods | Implement reputation systems for peers |
Use trusted bootstrapping nodes | Wait for multiple confirmations before accepting transactions |
Increase transaction propagation speed | Hide pending transaction details (prevent front-running) |
Use batch processing/fair sequencing | Develop secure consensus/order-matching algorithms |
Randomize transaction submission times
```

### 🛡️ Post-Quantum / Quantum-Resistant Countermeasures

```
Use quantum-resistant digital signatures in blockchain protocols |
Encrypt stored data with quantum-resistant algorithms |
Fragment/distribute data across locations | Isolate critical systems, multiple security layers |
Use cloud-based key management with quantum-resistant algorithms |
Employ secure multi-party computation (MPC) | Develop quantum-specific firewalls |
Use quantum-resistant zero-knowledge proofs | Implement quantum-resistant DLT |
Apply quantum-resistant threshold cryptography | Secure random number generation |
Use TPMs supporting quantum-resistant algorithms | RBAC/ABAC with quantum-safe protection |
Include quantum-resistance checks in SDLC/code review | Use HSMs for quantum-resistant key storage
```

---

### 🛠️ Cryptanalysis Tools

```
CrypTool | RsaCtfTool | Msieve | Cryptol | CryptoSMT | MTP
```

**Online MD5 Decryption Tools:** MD5 Decrypter (dcode.fr), MD5 Decrypt, Md5 Encrypt & Decrypt, MD5Hashing.net, Online Hash Crack, Md5.My-Addr.com

**Hashing tools:** MD5 Calculator, HashMyFiles (nirsoft.net — calculates MD5/SHA1 of files), CyberChef (multi-layer hashing — e.g., MD5 → SHA1 → HMAC recipe chains)

---

## 5. Quick Exam Cheat Sheet

### 🎯 4 Objectives of Cryptography

```
Confidentiality | Integrity | Authentication | Nonrepudiation
```

---

### 📊 4 Cipher Modes of Operation

```
ECB (Electronic Code Book)   → simplest, pattern leakage flaw
CBC (Cipher Block Chaining)  → XOR with previous ciphertext/IV, error propagates
CFB (Cipher Feedback)        → shift register feedback, harder cryptanalysis
CTR (Counter Mode)           → counter value, NO error propagation
```

---

### 📊 12 Cryptography Attacks

```
Ciphertext-only | Adaptive Chosen-plaintext | Chosen-plaintext | Related-Key |
Dictionary | Known-plaintext | Chosen-ciphertext | Rubber Hose |
Chosen-key | Timing | Man-in-the-Middle
```

---

### 📊 4 Cryptanalysis Methods

```
Linear | Differential | Integral | Quantum
```

---

### 🔥 Common Exam Scenarios

**Q: What are the 4 objectives of cryptography?**
→ **Confidentiality, Integrity, Authentication, Nonrepudiation**

**Q: What cipher mode has NO error propagation because it doesn't use previous ciphertext?**
→ **Counter Mode (CTR)**

**Q: What cipher mode has the flaw that identical plaintext blocks produce identical ciphertext blocks?**
→ **Electronic Code Book (ECB)**

**Q: What attack extracts secrets via coercion or torture rather than technical means?**
→ **Rubber Hose Attack**

**Q: What attack exploits the mathematical relationship between two different (but related) keys?**
→ **Related-Key Attack**

**Q: What attack is based on repeatedly measuring exact execution times of modular exponentiation?**
→ **Timing Attack**

**Q: What cross-protocol attack lets an attacker decrypt TLS traffic by exploiting SSLv2 support?**
→ **DROWN Attack**

**Q: What attack finds two different messages producing the same hash output?**
→ **Hash Collision Attack**

**Q: What attack uses a precomputed table of hash values to crack passwords faster?**
→ **Rainbow Table Attack**

**Q: What paradox underlies the Birthday Attack (23 people, >50% chance of shared birthday)?**
→ **Birthday Paradox**

**Q: What are the 4 cryptanalysis methods?**
→ **Linear, Differential, Integral, Quantum Cryptanalysis**

**Q: Who invented Linear Cryptanalysis? Differential Cryptanalysis?**
→ Linear: **Mitsuru Matsui**; Differential: **Eli Biham and Adi Shamir**

**Q: What quantum algorithm breaks RSA/ECDH by factoring large numbers?**
→ **Shor's algorithm**

**Q: What quantum algorithm speeds up brute-force key search against AES/SHA?**
→ **Grover's algorithm**

**Q: What are the 6 steps of the PKI process?**
→ **Apply to RA → RA verifies & requests CA → CA issues certificate (sends to VA) → User signs message with private key → Client verifies with VA → VA compares & determines validity**

**Q: What are the minimum recommended key sizes for symmetric and asymmetric algorithms per CEH countermeasures?**
→ Symmetric: **256 bits**; Asymmetric: **at least 2048 bits**; Hash: **256 bits or higher**

**Q: What 2 functions are commonly used for key stretching?**
→ **PBKDF2 and Bcrypt**

**Q: What are the 4 types of blockchain?**
→ **Public, Private, Federated (Consortium), Hybrid**

**Q: What process validates blocks in a blockchain, and what is the process of adding validated blocks called?**
→ Validating = **"proof of work"**; Adding = **"crypto mining"**

**Q: What is the first block in a blockchain called?**
→ **Genesis block** (represented by 0s)

**Q: What's the difference between Simple Power Analysis (SPA) and Differential Power Analysis (DPA)?**
→ SPA reveals info about instruction/values at a certain time; DPA doesn't require algorithm implementation knowledge — exploits statistical methods

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 20 (Final Module)*