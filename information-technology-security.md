# Information Technology Security — Unit Notes

*Part of the OTHM Level 5 Extended Diploma in Cyber Security*

These are my notes as I work through this unit, summarizing key concepts in my own words.

---

## Unit 1 — Fundamentals of Information & Information Privacy

**Introduction to Information**
Information is data that has been processed or organized so it has meaning — a phone number on its own is data, but "this is my doctor's phone number" is information. Understanding this distinction matters in security because protecting raw data isn't the same as protecting what that data reveals when combined or interpreted.

**Usage of Information**
Organizations collect and use information to make decisions, run operations, deliver services, and understand customers. How information is used shapes what protections it needs — information used for marketing has different risk than information used for authentication.

**Types of Information**
Information can be classified in different ways: personal vs corporate, structured vs unstructured, public vs confidential. Classification is the first step toward deciding how something should be secured.

**Information Privacy**
Privacy is about control — who gets to decide what happens to information about a person, and who it's shared with. It's distinct from security: security is about protecting information from unauthorized access; privacy is about respecting rights over that information even among those who could technically access it.

**Ethical Considerations for Privacy & Trade-Off**
There's often a tension between convenience/functionality and privacy — e.g. apps that want location data to work better vs. the risk of tracking. Ethical practice means being transparent about this trade-off rather than hiding it.

**Personal and Corporate Impact of Privacy Breaches**
For individuals: identity theft, financial loss, reputational harm, emotional distress. For organizations: regulatory fines, loss of customer trust, legal liability, brand damage.

**Societal and National Impact**
At scale, privacy breaches can undermine public trust in digital services, be used for large-scale manipulation (e.g. targeted disinformation), or even threaten national security when government or infrastructure data is exposed.

---
## Unit 2 — Information Security, Threats & Risks

**Cyber Security Threats & System-Based Attacks**
Threats that target systems directly — malware, denial-of-service attacks, exploitation of software vulnerabilities — rather than targeting people.

**Human-Based Threats & Vulnerability Exploitation**
Attacks that exploit people rather than technology — phishing, social engineering, pretexting. Often more effective than technical attacks because humans are harder to "patch."

**Internal vs External Risks**
Internal risks come from within an organization (employees, contractors — whether malicious or careless). External risks come from outside actors. Internal risks are often underestimated but can be just as damaging.

**The CIA Triad Basics**
The three core goals of security: **Confidentiality** (only authorized people can access information), **Integrity** (information isn't altered without authorization), **Availability** (information/systems are accessible when needed).

**Applying the CIA Triad**
In practice, security decisions often involve balancing the three — e.g. strong encryption boosts confidentiality but can add friction that hurts availability if not implemented well.

**Access Control Models**
Frameworks for deciding who can access what — e.g. role-based access control (permissions tied to job role), discretionary access control (owner decides), mandatory access control (system-enforced policy).

**Risk Management Models**
Structured approaches to identifying, assessing, and responding to risk — typically involving identifying assets, assessing likelihood/impact of threats, and choosing to accept, mitigate, transfer, or avoid the risk.

**The Zero-Trust Model**
A security approach based on "never trust, always verify" — no user or device is trusted by default, even if inside the network perimeter. Every request is authenticated and authorized.

**Identifying Risks and Vulnerabilities**
The process of finding weaknesses in systems, processes, or people that could be exploited — through audits, scanning, and review.

**Threat Modelling**
A structured way of thinking through what could go wrong — e.g. the STRIDE model (Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege) — to anticipate attacks before they happen.

**Security Controls and Measures**
The actual safeguards put in place — technical (firewalls, encryption), administrative (policies, training), and physical (locked server rooms) — to reduce risk.

---

## Unit 3 — Understanding Security in System Design

**Human Vulnerabilities**
People are often the weakest link — through lack of awareness, poor password habits, or susceptibility to manipulation.

**Fundamental Software Vulnerabilities**
Common weaknesses like poor input validation, buffer overflows, or insecure default configurations that attackers can exploit.

**Advanced Software Vulnerabilities and Exploits**
More sophisticated issues, such as chained exploits (combining multiple small flaws) or zero-day vulnerabilities (unknown to the vendor at time of exploitation).

**Hardware Vulnerabilities**
Weaknesses at the physical/firmware level — e.g. side-channel attacks, insecure IoT devices, or flaws baked into chip design.

**Principles of Secure System Design**
Concepts like least privilege (give only the access needed), defense in depth (multiple layers of protection), and fail-safe defaults (deny by default) that guide how secure systems should be built.

**Secure Software Development**
Building security into the software development lifecycle from the start rather than adding it afterward — including secure coding practices and following guides like the OWASP developer guide.

**Security Testing and Review**
Verifying security through methods like vulnerability assessments, penetration testing, and code review before and after deployment.

**Practical Implementation of Secure Design Frameworks**
Applying established frameworks — like the UK Government's Secure by Design principles — to real projects rather than treating security as an afterthought.

---

## Unit 4 — Cryptography & Encryption Techniques

**History of Cryptography**
From ancient substitution ciphers (like the Caesar cipher) through mechanical systems like Enigma, to modern digital cryptography.

**Evolution of Modern Cryptography**
The shift from manual/mechanical methods to mathematically-based digital encryption, driven by the need to secure computer and network communication.

**Mathematical Foundations of Encryption**
Encryption relies on mathematical problems that are easy to compute one way but very hard to reverse without the key (e.g. factoring large prime numbers).

**Symmetric and Asymmetric Encryption**
Symmetric encryption uses the same key to encrypt and decrypt (e.g. AES) — fast, but the key must be shared securely. Asymmetric encryption uses a public/private key pair (e.g. RSA) — solves the key-sharing problem but is slower.

**Encryption at Rest and in Transit**
"At rest" means encrypting stored data (e.g. on a hard drive); "in transit" means encrypting data while it moves across a network (e.g. via TLS/SSL).

**Introduction to Hashing**
Hashing converts data into a fixed-length string that can't be reversed back to the original — used to verify integrity rather than to hide content.

**Hashing Algorithms and their Applications**
Algorithms like SHA-256 are used for password storage, verifying file integrity, and digital signatures.

**Public Key Infrastructure and Digital Signatures**
PKI is the system of certificates and authorities that lets people trust that a public key really belongs to who it claims to. Digital signatures use this to prove authenticity and integrity of a message.

**Key Management and Distribution**
The processes for generating, storing, rotating, and safely sharing encryption keys — often the weakest point in a cryptographic system if handled poorly.

**Evaluating Encryption Techniques**
Choosing the right method depends on the use case — balancing security strength, performance, and practicality.

---

## Unit 5 — Legal Frameworks & Ethical Considerations

**Need for Legal Frameworks in Cyber Security**
Laws create accountability and set minimum standards for how organizations must protect data and respond to breaches.

**Data Protection Laws and Regulations**
Regulations like GDPR (EU) or POPIA (South Africa) set rules for how personal data must be collected, stored, and processed.

**Ethical Hacking and Vulnerability Disclosure**
Ethical hackers test systems with permission to find weaknesses before malicious actors do, and follow responsible disclosure practices when reporting findings.

**Surveillance, Data Rights and Privacy**
The balance between state/organizational surveillance (for security purposes) and individual rights to privacy.

**Privacy vs Security**
These two goals can conflict — stronger surveillance may improve security but reduce privacy, and vice versa. Good policy tries to find a defensible balance.

**Case Studies in Cyber Law and Ethics**
Real-world examples (data breaches, ethical hacking incidents, legal cases) that illustrate how these principles play out in practice.

---

*Note: these are my own summaries written while studying — I'll expand them with examples and practical exercises as I go deeper into each topic.*

