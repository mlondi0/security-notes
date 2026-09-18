# Using Cryptography to Secure Company Information

Information Technology Security: Cryptography Assignment*

## Part 1: Principles of Cryptography for Information Security

### What Cryptography Achieves

Cryptography is the practice of transforming information so that only authorized parties can access or verify it. In an information security context, it supports several of the core goals covered by the CIA Triad and beyond:

- **Confidentiality** encryption ensures that even if data is intercepted or stolen, it can't be read without the correct key.
- **Integrity** hashing and digital signatures let a recipient verify that data hasn't been altered.
- **Authentication** cryptographic keys and certificates let systems and people prove who they are.
- **Non-repudiation** digital signatures make it possible to prove that a specific party created or approved a piece of data, so they can't later deny it.

For a company managing a large user base, this matters at every layer: protecting customer data at rest, securing communication between servers, and proving that software or transactions haven't been tampered with.

### Why Modern Cryptography Is Much Harder to Break

Historical cryptography like the Caesar cipher (shifting letters by a fixed amount) or the Vigenère cipher (a repeating-key substitution) relied on **obscuring patterns in language**. These schemes can be broken through frequency analysis: since certain letters (like "e" in English) appear more often than others, patterns in the ciphertext reveal patterns in the plaintext. Once the method itself is known, breaking it is often just a matter of manual effort or simple computation.

Modern cryptography instead relies on **mathematical problems that are computationally infeasible to reverse without a key**, even when the algorithm itself is fully public. Key reasons it's so much harder to break:

1. **Security through mathematics, not secrecy of method.** Modern algorithms (AES, RSA, SHA-256, etc.) are publicly documented and peer-reviewed this is intentional (see Kerckhoffs's Principle: a system should be secure even if everything about it except the key is public). Security comes entirely from the secrecy of the key, not from hiding how the algorithm works.
2. **Enormous key spaces.** A modern AES-256 key has 2 possible combinations a number so large that even with all the computing power on Earth, brute-forcing it would take longer than the age of the universe.
3. **Hard mathematical problems.** Asymmetric schemes like RSA rely on problems such as factoring the product of two very large prime numbers easy to compute one way, but there's no known efficient method to reverse it with current classical computers.
4. **No exploitable statistical patterns.** Unlike historical ciphers, well-designed modern algorithms produce output that is statistically indistinguishable from random noise, so frequency analysis and similar techniques don't work.
5. **Continuous scrutiny.** Modern algorithms are tested for years by cryptographers and security researchers worldwide before being trusted for real-world use, and are retired if any weakness is found (e.g. MD5 and SHA-1 were phased out once collision weaknesses were discovered).

### Categories of Cryptographic Schemes

| Type | How it works | Examples | Common Uses |
|---|---|---|---|
| **Symmetric encryption** | Same key encrypts and decrypts | AES, ChaCha20 | Encrypting files, disk encryption, VPN traffic |
| **Asymmetric encryption** | Public key encrypts, private key decrypts (key pair) | RSA, ECC | Secure key exchange, SSH, TLS handshakes |
| **Hashing** | One-way function producing a fixed-length "fingerprint" | SHA-256, SHA-3 | Password storage, integrity checks, digital signatures |
| **Digital signatures** | Combines hashing + asymmetric keys to prove authorship and integrity | RSA/DSA/ECDSA signatures | Verifying software updates, signed documents, code commits |
| **Key exchange protocols** | Lets two parties agree on a shared secret over an insecure channel | Diffie-Hellman, ECDH | Establishing session keys for TLS/SSH |

Symmetric encryption is fast and efficient for large amounts of data but requires securely sharing the key beforehand. Asymmetric encryption solves that key-sharing problem but is slower, so in practice systems like TLS and SSH combine both: asymmetric cryptography to securely exchange a symmetric session key, then symmetric encryption for the actual data transfer.

---

## Part 2: Generating SSH Keys and Establishing a Secure Connection

### Generating a Key Pair

Using an Ubuntu (WSL2) terminal, I generated an SSH key pair with the Ed25519 algorithm, currently recommended over older RSA keys for most use cases due to its strong security-to-key-size ratio and faster performance:

```
ssh-keygen -t ed25519 -C "mlondi-assignment"
```

This created two files in `~/.ssh/`:
- **`id_ed25519`** the private key, kept secret and never shared.
- **`id_ed25519.pub`** the public key, safe to share with any server I want to access.

*(See screenshot: key generation and randomart)*

### Setting Up a Server and Connecting

I installed `openssh-server` on the same machine to act as a local test server, started the SSH service, and connected to it as a client:

```
sudo service ssh start
ssh dee_4x@localhost
```

On first connection, SSH displayed the server's host key fingerprint and asked me to confirm it before proceeding. This is a critical security step: it protects against **man-in-the-middle attacks**, where an attacker could impersonate the server. Once confirmed, the fingerprint is stored locally so future connections are automatically checked against it, and I'd be warned if it ever changed unexpectedly.

*(See screenshot: password-based login)*
<img width="868" height="1033" alt="part2-ssh-connection-password" src="https://github.com/user-attachments/assets/36e450d7-7a38-42b5-b671-4382c1ca82d5" />


### Switching to Key-Based Authentication

I then copied my public key to the server's list of trusted keys:

```
ssh-copy-id dee_4x@localhost
```

This appends the public key to `~/.ssh/authorized_keys` on the server. On the next connection attempt, SSH used the key pair instead of a password proven by the prompt changing from "Enter password" to "Enter passphrase for key", confirming the cryptographic key was doing the authentication work rather than a stored password.

*(See screenshot: key-based login success)*

<img width="1081" height="615" alt="part2-ssh-key-login-success" src="https://github.com/user-attachments/assets/7134e99a-119b-4e8d-ad41-d1ec2c33d077" />


### How the Secure Connection Is Established

```
Client Server
 | |
 |------ 1. TCP connection request ------------->|
 |<----- 2. Server sends host public key ---------|
 | (client checks fingerprint against known_hosts)
 | |
 |<===== 3. Key exchange (Diffie-Hellman) =======>|
 | (both sides derive a shared session key |
 | without ever transmitting it directly) |
 | |
 |------ 4. All further traffic encrypted ------->|
 |<----- with the shared symmetric session key ---|
 | |
 |------ 5. Client authenticates with private ---->|
 | key (signs a challenge) |
 |<----- 6. Server verifies signature against ----|
 | stored public key -> access granted |
```

### Why This Is Secure

1. **Host verification** prevents connecting to an imposter server (protects against man-in-the-middle attacks).
2. **Key exchange (Diffie-Hellman)** allows client and server to agree on a shared symmetric encryption key over an insecure network, without ever sending that key itself across the connection even someone monitoring all traffic can't derive it.
3. **Symmetric encryption of the session** (once the key exchange is done) is fast and secures all data sent afterward.
4. **Public-key authentication** means the private key never leaves my machine. The server only ever needs to know the public key, so even if the server were fully compromised, an attacker couldn't extract anything usable to impersonate me elsewhere.
5. **No password sent over the network at all** when using key-based auth removing an entire category of risk (password interception, brute-forcing, credential reuse from breaches elsewhere).

---

## Part 3: At-Rest Encryption of a File

"At rest" encryption protects data while it's stored on a disk, in a database, or in a backup as opposed to "in transit" encryption, which protects data while it's moving across a network (covered in Part 2's SSH connection). Both matter: a file could be perfectly safe while being transferred but completely exposed if the storage device is stolen or accessed without authorization.

### Encrypting the File

I created a sample file representing valuable company data, then encrypted it using GPG's symmetric encryption (AES256):

```
echo "This is confidential company information: Q3 revenue figures and client contract terms." > confidential.txt
gpg -c confidential.txt
```

This produced `confidential.txt.gpg`, protected by a passphrase. Viewing the encrypted file directly with `cat` shows unreadable binary data rather than any trace of the original content confirming the file is genuinely encrypted, not just relabeled or hidden.

*(See screenshot: encrypted file content, unreadable)*
<img width="1205" height="513" alt="part3-gpg-encrypt-decrypt" src="https://github.com/user-attachments/assets/230afa0c-fe4c-4fbe-90f7-4acf0efde81d" />


### Decrypting the File

To prove the process is fully reversible with the correct passphrase, I renamed the original file out of the way, then decrypted the `.gpg` file back into plain text:

```
gpg -d confidential.txt.gpg > confidential_decrypted.txt
```

Comparing the decrypted file to the original with `diff confidential_original.txt confidential_decrypted.txt` produced **no output at all** meaning the two files are byte-for-byte identical. This confirms the encryption/decryption round-trip preserved the data with zero loss or corruption.

*(See screenshot: decrypted content matches original, empty diff)*

### Why This Matters for the Company

If a laptop, backup drive, or cloud storage bucket containing company data were lost, stolen, or accessed by an unauthorized party, at-rest encryption ensures the data itself remains unreadable without the passphrase (or key, in more advanced setups using asymmetric encryption). This is especially relevant for the company given it manages a large user base customer records, financial data, and internal documents should never be stored in plain, readable form.

---

## Part 4: Hashing for Document Integrity

### Fingerprinting a Document

I created a sample document and generated its SHA-256 hash a fixed-length "fingerprint" that uniquely represents its exact content:

```
echo "Company Policy Document v1.0 - All employees must complete security training annually." > policy.txt
sha256sum policy.txt
```

**Result (v1.0):** `385ac1da3b80bc7b2eaf1b8f0ff1c8c59741c7eeb11d6f79949d297238b82b0d`

I then made a tiny, single-character edit changing "v1.0" to "v1.1" and re-hashed the file:

**Result (v1.1):** `63fe31a425fa0956be86ff3c8a9b2647e8b9893a1bb16b16bb3c8b6f5a3e2afd`

*(See screenshot: both hashes shown together)*
<img width="1222" height="251" alt="part4-hashing-comparison" src="https://github.com/user-attachments/assets/c0bfacc0-5f77-4a41-acf7-8222071a60b6" />


### The Avalanche Effect

Despite only a single character changing in a fairly long sentence, the resulting hash is **completely different** there is no visible similarity between the two outputs at all. This property is called the **avalanche effect**: a well-designed hash function is built so that even the smallest change to the input causes roughly half of the output bits to flip, unpredictably. This makes it impossible to guess what a small edit will do to the hash, which is exactly what makes hashing useful for detecting tampering even a single altered character, added space, or changed byte is immediately obvious once you compare hashes.

It's also worth noting hashing is a **one-way function**: there is no way to reverse a hash back into the original document. It only ever answers the question "does this content match what I expect?", not "what does the content say?".

### Ways Hashing Can Improve Security for the Company

1. **File/document integrity verification** storing the hash of an important document (contracts, policies, financial records) separately, so it can be re-checked later to confirm the file hasn't been altered, accidentally or maliciously.
2. **Password storage** rather than storing user passwords in plain text, the company should store only the hash of each password. When a user logs in, their entered password is hashed and compared to the stored hash meaning even if the database is breached, actual passwords are never exposed. (In practice this should use a slow, purpose-built password hash like bcrypt or Argon2 rather than a fast general-purpose hash like SHA-256, specifically to resist brute-force attacks.)
3. **Software/update verification** publishing the hash of official software downloads or updates alongside the file lets users confirm they received the genuine, untampered version.
4. **Digital signatures** signatures work by hashing a document first, then encrypting that hash with a private key; verifying the signature involves re-hashing and comparing, combining integrity and authentication in one mechanism.
5. **Detecting duplicate or known-malicious files** security tools can maintain databases of hashes of known malware; if a file's hash matches, it can be flagged without needing to fully re-scan the content.
6. **Blockchain/audit trails** chaining hashes together (each record's hash depends on the previous one) creates tamper-evident logs, useful for financial or compliance audit trails where the company needs to prove records weren't altered after the fact.

---

## Part 5: Industry Practices and Recommendations for the Company

### SSH Key Authentication

**Industry practice:** Enterprise security guidance increasingly favours disabling password-based SSH access entirely in favour of key-only authentication, since removing passwords eliminates the most automatable attack path (brute-force and credential-stuffing attacks). Larger organizations go further, layering short-lived certificates and centralized identity providers on top of raw key pairs, and enforcing policies such as maximum key lifetimes GitLab, for example, has built settings letting organizations enforce an expiry period on user SSH keys, closing a gap where old keys could otherwise linger indefinitely. Real incidents illustrate why this discipline matters: after a 2023 breach involving a stolen session on an engineer's laptop, CircleCI advised its entire customer base to rotate SSH keys and other credentials as a precaution showing that even organizations built around infrastructure automation can be caught out by weak key hygiene.

**Recommendation for the company:** Require key-based SSH authentication for all administrative and server access, disable password login entirely on production systems, and maintain an inventory of issued keys with expiry/rotation policies rather than leaving keys to accumulate indefinitely. For a company of this scale, routing all administrative SSH access through a central bastion host would also let the security team log and audit every session in one place.

**Benefit:** Removes an entire class of common attacks (password guessing, credential reuse from breaches elsewhere) and creates an auditable trail of exactly who accessed which system and when useful both for security monitoring and for demonstrating compliance to regulators or auditors.

### At-Rest Encryption

**Industry practice:** Data protection regulation increasingly treats encryption as a baseline expectation rather than an optional extra. Under the GDPR, encryption is explicitly listed as an "appropriate technical measure" for protecting personal data, and one of the earliest GDPR enforcement fines in Germany was issued specifically because a company had stored user passwords unencrypted. South Africa's own regulation, POPIA, similarly requires "reasonable technical and organisational measures" to protect personal information, and locally, both TransUnion and Experian have suffered major, high-profile breaches involving South African personal data in recent years underlining that this isn't a hypothetical risk for a company operating in this market.

**Recommendation for the company:** Encrypt sensitive data at rest by default customer records, financial data, and internal documents using strong, modern standards such as AES-256, whether stored on employee devices, internal servers, or third-party cloud storage. Backups should be encrypted to the same standard as production data, since backups are often overlooked and can become the weakest link. Key management should be handled separately from the encrypted data itself (e.g. using a dedicated key management service), so that access to storage alone isn't enough to expose the data.

**Benefit:** Beyond directly protecting customer trust and company data, demonstrable encryption practices can reduce regulatory exposure POPIA's breach-notification obligations, for instance, are less onerous when compromised data was properly encrypted, since the practical harm to affected individuals is far lower.

### Hashing

**Industry practice:** Password-related breaches repeatedly show the gap between companies that hash correctly and those that don't. LinkedIn's 2012 breach exposed 165 million passwords that had been hashed with unsalted SHA-1 a fast, general-purpose hash never intended for passwords and the vast majority were cracked within days. Adobe's 2013 breach is often cited as a cautionary example of confusing encryption with hashing: passwords were reversibly encrypted rather than hashed, meaning the underlying passwords could, in principle, be recovered. By contrast, in the 2015 Ashley Madison breach, accounts protected with bcrypt (a slow, purpose-built password-hashing algorithm) remained largely uncracked, while a smaller set still using older MD5 hashes were compromised quickly a direct, real-world comparison of weak versus strong hashing choices. Facebook, separately, was found in 2019 to have stored hundreds of millions of passwords in plain text internally, with no hashing at all.

**Recommendation for the company:** Passwords should never be stored in plain text or with fast general-purpose hashes like plain SHA-256. Instead, adopt a modern, purpose-built password hashing algorithm Argon2id or bcrypt with a unique salt per password, specifically because these are designed to be slow and computationally expensive to resist large-scale guessing attacks. Separately, use fast hashes like SHA-256 where they are appropriate: verifying file/document integrity, confirming software downloads haven't been tampered with, and underpinning digital signatures.

**Benefit:** Correct hashing practice means that even in the event of a full database breach, user passwords remain effectively unusable to attackers turning a potentially catastrophic breach into a much more contained incident, and directly limiting reputational and regulatory fallout.


All three techniques work together as layers of a broader security posture rather than standalone fixes: SSH keys protect *access* to systems, at-rest encryption protects *stored* data, and hashing protects both the *integrity* of data and the *secrecy* of credentials. Adopting all three, backed by clear policy (key rotation, encryption standards, and hashing algorithm choice) rather than ad-hoc implementation, would bring the company's practices in line with what regulators, auditors, and industry frameworks increasingly treat as baseline expectations.

---

## References

- Encryption Consulting, "Securing SSH against Attacks" https://www.encryptionconsulting.com/securing-ssh-against-attacks/
- Encryption Consulting, "Why SSH Key Management Matters in Modern Security" https://www.encryptionconsulting.com/why-ssh-key-management-matters-in-modern-security/
- GitLab, SSH key expiration policy proposal https://gitlab.com/gitlab-org/gitlab/-/issues/1007
- OWASP, "Password Storage Cheat Sheet" https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html
- Gupta Deepak, "Argon2 vs Bcrypt vs Scrypt vs PBKDF2" https://guptadeepak.com/the-complete-guide-to-password-hashing-argon2-vs-bcrypt-vs-scrypt-vs-pbkdf2-2026/
- USENIX, "Bcrypt at 25: A Retrospective on Password Security" https://www.usenix.org/publications/loginonline/bcrypt-25-retrospective-password-security
- Mayer Brown, "Privacy and Data Protection Post-GDPR Enforcement in Germany" (PDF)
- Fasken / Mondaq, "Data Protection in Terms of POPIA and the GDPR" https://www.mondaq.com/southafrica/data-protection/713936/data-protection-in-terms-of-popia-and-the-gdpr
- KPMG, "POPIA Data Breach" briefing (PDF)
- Notion / MZIZI Africa, POPIA enforcement case summary TransUnion

*Note: all sources paraphrased in my own words above see the linked pages directly for full detail and direct quotations if needed for citation purposes.*


