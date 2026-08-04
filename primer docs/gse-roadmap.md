# GSE Roadmap — Connor

> **Status:** Hand-owned document. The `/update-primer` skill does **not** touch this file.
> Built 2026-08-04 from GIAC's published requirements and exam objectives.
>
> **What this is:** the GIAC Security Expert (GSE) broken down from the credential at the
> top all the way to individual teachable concepts. Tick atoms off as they're covered.
> The primer (`learning-context.md`) stays the record of *demonstrated* mastery; this file
> is the *map*.

---

## 0. What the GSE actually is (corrected 2026-08-04)

The primer previously recorded the **legacy** model — GSEC + GCIH + GCIA, a Gold paper, and
a two-day in-person hands-on lab. **GIAC retired that.** The current model is a *portfolio*:

| Portfolio cert | Practitioner certs | Applied Knowledge certs |
|---|---|---|
| **GSP** — GIAC Security Professional | 3 | 2 |
| **GSE** — GIAC Security Expert | 6 | 4 |

Key properties of the new model:

- **No entrance exam.** No Gold/research-paper requirement. No travel, no proctored lab week.
- **Any** 6 of GIAC's 40+ Practitioner certs. **Any** 4 of the 6 Applied Knowledge certs.
- **Failures are not catastrophic.** Under the legacy lab, failing a section sank the attempt.
  Now each exam stands alone.
- **GSP is a real credential on the way**, not just a milestone. 3+2 gets you GSP; another
  3+2 converts it to GSE. Everything counts twice.
- **Certs must stay active.** They co-terminate to the GSE expiration date. This is the real
  long-term cost — renewals, not just first attempts.
- Applied Knowledge exams are **100% CyberLive**: 25 hands-on lab challenges, 4 hours,
  open book / open notes. They are explicitly *not* tied to a single training course.

**The six Applied Knowledge certs:**

| Code | Name | Primary-fit course | Natural Practitioner pair |
|---|---|---|---|
| GX-CS | Experienced Cybersecurity Specialist | SEC401 | GSEC |
| GX-IA | Experienced Intrusion Analyst | SEC503 | GCIA |
| GX-IH | Experienced Incident Handler | SEC504 | GCIH |
| GX-PT | Experienced Penetration Tester | SEC560 | GPEN |
| GX-FA | Experienced Forensics Analyst | FOR508 | GCFA |
| GX-FE | Experienced Forensics Examiner | FOR500 | GCFE |

**Cost note (verify before committing money):** reporting around the transition put Applied
Knowledge exams at ~$499 *if you hold the paired Practitioner cert*, ~$1,299 without. That
discount is structural — it makes the pairing below the cheap path, not just the logical one.
Practitioner exam attempts without SANS training run roughly $1,000–1,300 each. SANS courses
are ~$8–9k each and are **optional**. Confirm current pricing at giac.org before planning spend.

---

## 1. The portfolio I'm proposing for you

Six Practitioner + four Applied Knowledge, chosen so that **every Applied Knowledge exam is
paired with the Practitioner cert that discounts it**, and so the set actually matches what
you're already drawn to (low-level/memory, CTF, Linux, and the maritime domain).

| # | Practitioner | Why this one | Unlocks |
|---|---|---|---|
| 1 | **GSEC** | The breadth foundation. Everything else assumes it. | GX-CS |
| 2 | **GCIA** | Packets and intrusion analysis. **Your weakest area and the deepest prerequisite for everything defensive.** | GX-IA |
| 3 | **GCIH** | Attack lifecycle + response. The bridge between offense and defense. | GX-IH |
| 4 | **GPEN** | Offensive. Direct continuation of your Bandit/CTF work. | GX-PT |
| 5 | **GREM** | Reverse engineering malware. **The direct payoff of your stack-direction, buffer-overflow, and SAT/angr work.** Assembly, debuggers, packers. | — |
| 6 | **GICSP** | Industrial control systems. **You work maritime on the lower Mississippi** — vessel systems, port infrastructure, and ICS/OT is a domain where your day job is an actual advantage. | — |

**Applied Knowledge (4):** GX-CS, GX-IA, GX-IH, GX-PT.

**Swap candidates**, if interests move: GCFA + GX-FA, or GCFE + GX-FE (forensics), GCDA
(detection/SIEM), GWAPT (web app), GNFA (network forensics). Slots 5 and 6 are the flexible
ones — GREM and GICSP are picks, not requirements.

### GSP checkpoint

You hit **GSP** after: GSEC + GCIA + GCIH (3 Practitioner) and GX-CS + GX-IA (2 Applied
Knowledge). That is a genuine, listable credential — plan around reaching it, not around the GSE.

---

## 2. Honest sequencing and timeline

You are currently at CompTIA Tech+ diagnostic level with a **confirmed zero on networking**
(couldn't define a switch). That is not a criticism — it's the actual starting coordinate, and
the plan has to start there or it's fiction.

| Phase | Content | Realistic window |
|---|---|---|
| **0** | Foundations — networking, Linux, Windows/AD, Python, Tech+ exam | Now → ~12 months |
| **1** | GSEC → GX-CS | +9–15 months |
| **2** | GCIA → GX-IA | +12–18 months |
| **3** | GCIH → GX-IH | +9–12 months → **GSP earned here** |
| **4** | GPEN → GX-PT | +12 months |
| **5** | GREM | +12 months |
| **6** | GICSP | +6–9 months → **GSE** |

**Total: realistically 5–8 years.** Anyone selling a shorter path to GSE from a standing start
is selling something. The GSP at roughly the halfway point is what makes this survivable.

**Ordering rationale — read this, it's the part that matters:** GCIA sits at position 2 rather
than later precisely *because* networking is your weak point. Packet analysis is the substrate
under intrusion detection, incident response, network forensics, and half of pentesting. Deferring
it because it's hard means every later cert gets harder. Attack the weak link early.

---

## 3. Phase 0 — Foundations (before any GIAC exam)

None of this is GIAC material. All of it is assumed by GIAC material.

### 0.1 Networking — the confirmed gap, highest priority

- [ ] What a **collision domain** and a **broadcast domain** are, and why they motivated switches
- [ ] **Hub** vs **switch**: repeat-to-all-ports vs. forward-by-learned-MAC
- [ ] MAC address table / CAM table — how a switch *learns* which port a MAC is on
- [ ] What a switch does with an unknown destination MAC (flooding)
- [ ] **Router** vs switch: layer 3 vs layer 2, routes between networks vs within one
- [ ] **Access point** — bridging wireless clients into a wired L2 segment
- [ ] Why a home "router" is really router + switch + AP + firewall + DHCP in one box
- [ ] OSI 7-layer model — each layer's job, and one real protocol at each
- [ ] TCP/IP 4-layer model, and how it maps onto OSI
- [ ] Encapsulation: how a payload gains headers going down the stack, sheds them going up
- [ ] IPv4 address structure — network portion vs host portion
- [ ] Subnet masks and CIDR notation (`/24`), by hand, in binary
- [ ] Calculate network address, broadcast address, usable host range for a given CIDR
- [ ] Private (RFC1918) vs public address space
- [ ] **NAT** — why it exists, what it breaks
- [ ] Default gateway — what it means for a host to "not know" a route
- [ ] **ARP** — resolving IP → MAC, and why it's trust-on-first-use (sets up ARP spoofing later)
- [ ] **DNS** resolution walkthrough: recursive resolver → root → TLD → authoritative
- [ ] DNS record types: A, AAAA, CNAME, MX, TXT, PTR, NS
- [ ] **DHCP** DORA sequence
- [ ] **TCP three-way handshake** — SYN, SYN-ACK, ACK, and what each proves
- [ ] TCP teardown: FIN/ACK exchange vs RST
- [ ] Sequence and acknowledgment numbers — how TCP achieves reliability
- [ ] TCP windowing and flow control
- [ ] **UDP** — what it drops and why anyone would want that
- [ ] Ports: well-known / registered / ephemeral; socket = IP + port + protocol
- [ ] Common ports on sight: 22, 25, 53, 80, 88, 123, 389, 443, 445, 3389
- [ ] **ICMP** — echo, unreachable, time exceeded; how traceroute exploits TTL
- [ ] VLANs — segmentation on shared physical hardware
- [ ] Routing basics: static vs dynamic, routing table lookup, longest-prefix match

### 0.2 Linux (extends your existing Applying-level work)

- [ ] Filesystem hierarchy: `/etc`, `/var`, `/proc`, `/sys`, `/dev`, `/usr`, `/tmp`
- [ ] Permission triads, octal notation, `umask`
- [ ] setuid / setgid / sticky bit — and why setuid is a privilege-escalation surface
- [ ] Users, groups, `/etc/passwd`, `/etc/shadow`, hash formats in shadow
- [ ] Processes: PID, PPID, fork/exec, process states, `/proc/<pid>/`
- [ ] `systemd` units, services, enabling vs starting, `journalctl`
- [ ] Log locations and formats: `/var/log/auth.log`, `syslog`, journald
- [ ] Package management: apt/dpkg, rpm/yum — and what a repo trust chain is
- [ ] SSH: keypairs, `authorized_keys`, config hardening, agent forwarding risk
- [ ] `iptables` / `nftables` basics — chains, tables, default-deny
- [ ] Cron and systemd timers (persistence mechanism later in GCIH)
- [ ] Text processing fluency: `grep -E`, `awk`, `sed`, `cut`, `sort`, `uniq -c`, `tr`, `jq`
- [ ] Pipes, redirection, file descriptors 0/1/2, here-docs
- [ ] Finish **Bandit** past L15 → L34
- [ ] Roppers Linux Certificate → done (already in progress)

### 0.3 Windows & Active Directory (a real gap — GIAC is Windows-heavy)

- [ ] Windows filesystem layout and NTFS permissions vs share permissions
- [ ] The Registry: hives, keys, values; where persistence hides
- [ ] Local accounts vs domain accounts; SIDs
- [ ] **Active Directory**: domain, forest, OU, domain controller
- [ ] **Kerberos** authentication flow: AS-REQ → TGT → TGS → service ticket
- [ ] NTLM authentication and why it survives
- [ ] Where credentials live in memory (LSASS) — sets up credential theft later
- [ ] Group Policy: GPO application order, security templates
- [ ] Windows Event Log: channels, key Event IDs (4624, 4625, 4688, 4672)
- [ ] PowerShell basics: cmdlets, objects-not-text, pipeline, execution policy
- [ ] SMB: shares, named pipes, why 445 matters so much

### 0.4 Programming & data

- [ ] Python: comfortable *writing* from scratch, not just reading (current stated gap)
- [ ] Python: file I/O, `argparse`, exceptions, virtualenvs
- [ ] Python: `socket`, `struct`, `requests` — the security-tooling trio
- [ ] `scapy` — craft and dissect a packet in Python (direct GCIA prerequisite)
- [ ] Regular expressions to fluency (IDS rules, log parsing)
- [ ] Bash scripting: loops, conditionals, functions, `set -euo pipefail`
- [ ] Git (already Applying — maintain)
- [ ] Finish the **CompTIA Tech+** exam already in flight

### 0.5 Cryptography groundwork

- [ ] Symmetric vs asymmetric — what each is actually *for*
- [ ] Hash functions: preimage / second-preimage / collision resistance
- [ ] Why hashing ≠ encryption ≠ encoding (three different jobs)
- [ ] HMAC and message authentication
- [ ] Digital signatures — signing vs encrypting, and the direction of the key
- [ ] Certificates, CAs, chain of trust, revocation
- [ ] TLS handshake end to end (connects to your existing OpenSSL `s_client` work)
- [ ] Salting, key stretching, why fast hashes are wrong for passwords

---

## 4. Practitioner tracks

> Each section lists GIAC's published exam objectives verbatim as headers, decomposed into
> atoms. **The atoms are my decomposition, not GIAC's** — GIAC publishes the objectives, not
> a sub-concept list. Treat atoms as the study unit and the objectives as authoritative.

---

### 4.1 GSEC — GIAC Security Essentials

**Exam:** 106 questions · 4 hours · **72%** to pass · CyberLive · pairs with **GX-CS**
**Primary-fit course:** SEC401

Broadest of the six. 26 objectives. The point of GSEC is coverage, not depth — resist going
down rabbit holes.

**Access Control & Password Management**
- [ ] Identification vs authentication vs authorization vs accounting
- [ ] Access control models: DAC, MAC, RBAC, ABAC
- [ ] Principle of least privilege; separation of duties
- [ ] Password storage: hash + salt + work factor
- [ ] Password attacks as a defender sees them: guessing, cracking, spraying, stuffing
- [ ] MFA factor categories and their respective failure modes

**Cryptography**
- [ ] Symmetric ciphers: AES, block vs stream, key sizes
- [ ] Block cipher modes — ECB's visible failure, CBC, GCM (authenticated)
- [ ] Asymmetric: RSA, ECC; the math intuition (factoring / discrete log)
- [ ] Hash families: MD5, SHA-1 (both broken), SHA-2, SHA-3
- [ ] Key exchange: Diffie-Hellman, forward secrecy
- [ ] Crypto failure modes: key reuse, weak RNG, rolling your own

**Cryptography Application**
- [ ] VPN types: site-to-site vs remote access; IPsec vs TLS VPN
- [ ] IPsec: AH vs ESP, transport vs tunnel mode, IKE
- [ ] GPG: keyring, web of trust, sign vs encrypt
- [ ] PKI: CA hierarchy, CSR, issuance, revocation (CRL/OCSP)

**Defense in Depth**
- [ ] Layered controls; no single point of failure
- [ ] Preventive / detective / corrective / compensating controls
- [ ] Administrative vs technical vs physical
- [ ] Risk = threat × vulnerability × impact; risk treatment options

**Defensible Network Architecture**
- [ ] Segmentation and zoning; DMZ design
- [ ] Choke points and where to place sensors
- [ ] Egress filtering — and why it catches more than ingress filtering
- [ ] Network visibility: TAP vs SPAN port, and what each misses

**Network Security Devices**
- [ ] Packet filter vs stateful firewall vs next-gen firewall
- [ ] Proxies: forward vs reverse; TLS inspection tradeoffs
- [ ] NIDS vs NIPS — inline vs out-of-band, and the failure consequence of each
- [ ] Signature-based vs anomaly-based detection
- [ ] False positive vs false negative, and which one you tune toward

**Endpoint Security**
- [ ] Host firewalls
- [ ] HIDS vs HIPS
- [ ] EDR — what telemetry it collects
- [ ] Application allowlisting

**Networking & Protocols**
- [ ] (Covered in Phase 0.1 — treat as review, then verify against objectives)
- [ ] Protocol stack behavior under attack conditions

**Linux Fundamentals / Linux Security and Hardening**
- [ ] (Phase 0.2 covers most — extend below)
- [ ] Auditing with `auditd`
- [ ] SELinux / AppArmor mandatory access control
- [ ] Baseline hardening: minimize services, patch, restrict, log
- [ ] Detecting a compromised Linux host: unexpected listeners, SUID files, cron entries

**Windows Security Infrastructure / Access Controls / Services**
- [ ] (Phase 0.3 covers most — extend below)
- [ ] NTFS permission inheritance and effective permissions
- [ ] Registry key ACLs
- [ ] Windows privileges vs permissions (the distinction GIAC tests)
- [ ] IIS, Remote Desktop, IPsec hardening
- [ ] Azure security features at a high level

**Enforcing Windows Security Policy**
- [ ] Group Policy structure; computer vs user configuration
- [ ] GPO precedence: LSDOU, block inheritance, enforced
- [ ] INF security templates

**Windows as a Service**
- [ ] Servicing channels and update rings
- [ ] WSUS / patch management at scale

**Windows Automation, Auditing, and Forensics**
- [ ] PowerShell scripting fundamentals
- [ ] PowerShell logging: module, script block, transcription
- [ ] Auditing policy configuration
- [ ] Basic host forensic artifacts

**Container and MacOS Security**
- [ ] (You have namespaces/cgroups at Learning — extend)
- [ ] Container escape surfaces; privileged containers
- [ ] Image provenance and scanning
- [ ] macOS: SIP, Gatekeeper, XProtect, TCC

**Virtualization, Cloud Security, and AI Essentials**
- [ ] Type 1 vs Type 2 hypervisors
- [ ] Shared responsibility model
- [ ] IaaS / PaaS / SaaS boundaries
- [ ] Cloud identity and misconfiguration as the dominant cloud risk
- [ ] AI fundamentals as GIAC frames them; prompt injection basics

**Log Management & SIEM**
- [ ] What to log and why most orgs log the wrong things
- [ ] Log centralization; syslog, Windows Event Forwarding
- [ ] Correlation rules
- [ ] Time synchronization — why NTP drift destroys investigations

**Malicious Code & Exploit Mitigation**
- [ ] Malware taxonomy: virus, worm, trojan, ransomware, rootkit
- [ ] Buffer overflow mechanics (**you already have stack direction at Understood — this is your anchor**)
- [ ] Exploit mitigations: ASLR, DEP/NX, stack canaries, CFG
- [ ] How each mitigation is bypassed conceptually (ROP, info leak)

**Vulnerability Scanning and Penetration Testing**
- [ ] Threat vs vulnerability vs risk vs exploit
- [ ] Recon: passive vs active
- [ ] Network mapping; nmap fundamentals
- [ ] Vulnerability scanning; CVE, CVSS scoring
- [ ] Pentest phases and rules of engagement

**Web Communication Security**
- [ ] HTTP request/response anatomy, methods, status codes
- [ ] Cookies: attributes `HttpOnly`, `Secure`, `SameSite`
- [ ] Session management and session fixation
- [ ] Same-origin policy; CORS
- [ ] OWASP Top 10 at survey level

**Wireless Network Security**
- [ ] 802.11 frame types; association process
- [ ] WEP → WPA → WPA2 → WPA3 and why each fell
- [ ] Evil twin, deauth, rogue AP
- [ ] Enterprise wireless: 802.1X / EAP / RADIUS

**Data Loss Prevention and Mobile Device Security**
- [ ] Data classification; data at rest / in transit / in use
- [ ] DLP detection approaches and their evasions
- [ ] MDM, containerization, BYOD risk
- [ ] Full-disk encryption on mobile

**Incident Handling & Response**
- [ ] PICERL phases (deepened heavily in GCIH)
- [ ] Evidence handling and chain of custody
- [ ] When to contain vs when to observe

**Security Frameworks and CIS Controls**
- [ ] CIS Critical Controls — implementation groups
- [ ] NIST CSF functions: Identify, Protect, Detect, Respond, Recover
- [ ] **MITRE ATT&CK**: tactics vs techniques vs procedures
- [ ] Mapping a real incident onto ATT&CK

---

### 4.2 GCIA — GIAC Certified Intrusion Analyst

**Exam:** 106 questions · 4 hours · **67%** to pass · CyberLive · pairs with **GX-IA**
**Primary-fit course:** SEC503

**This is your hardest and most important cert.** It is also the one that turns your confirmed
networking gap into a strength. Budget the most time here.

**Concepts of TCP/IP and the Link Layer**
- [ ] Ethernet frame structure field by field
- [ ] MAC addressing; OUI; broadcast and multicast addresses
- [ ] ARP request/reply on the wire; gratuitous ARP
- [ ] ARP spoofing as seen in a capture
- [ ] VLAN tagging (802.1Q) in a frame
- [ ] The full encapsulation stack in one real packet

**IP Headers**
- [ ] Every IPv4 header field and its purpose
- [ ] IHL, options, and why they matter for evasion
- [ ] TTL — fingerprinting OS by initial TTL; traceroute mechanics
- [ ] TOS/DSCP
- [ ] Header checksum; what a bad checksum means
- [ ] Identifying spoofed or crafted headers by internal inconsistency

**Fragmentation**
- [ ] Why fragmentation exists; MTU and path MTU discovery
- [ ] Fragment offset, More Fragments flag, Don't Fragment flag
- [ ] Reassembly and where the state lives
- [ ] **Overlapping fragment attacks** and reassembly ambiguity between IDS and host
- [ ] Tiny fragment attack
- [ ] Identifying fragmentation in a pcap by hand

**TCP**
- [ ] TCP header field by field
- [ ] All flags: SYN ACK FIN RST PSH URG ECE CWR NS
- [ ] Handshake, established data transfer, four-way teardown
- [ ] Sequence/ack number arithmetic across a real session
- [ ] Window scaling, selective ACK, options
- [ ] Retransmission and duplicate ACK patterns
- [ ] Anomalous flag combinations: NULL, FIN, XMAS scans
- [ ] RST injection / session hijack indicators

**UDP and ICMP**
- [ ] UDP header; statelessness and its detection consequences
- [ ] ICMP types and codes — the ones that matter
- [ ] ICMP tunneling and covert channels
- [ ] ICMP as a scan and mapping tool
- [ ] ICMP error messages that quote the original packet

**IPv6**
- [ ] IPv6 header vs IPv4 — what was removed and why
- [ ] Address notation, types (link-local, global unicast, multicast)
- [ ] Extension headers and header-chain evasion
- [ ] **NDP** replacing ARP; NDP spoofing
- [ ] SLAAC
- [ ] Tunneling: 6to4, Teredo — and IPv6 as a bypass of v4-only controls

**Application Protocols**
- [ ] HTTP/HTTPS dissection; HTTP/2 differences
- [ ] DNS on the wire: query/response, record types, EDNS0
- [ ] **DNS tunneling and DGA detection**
- [ ] SMTP transaction; header analysis for phishing
- [ ] SMB/CIFS on the wire
- [ ] TLS handshake as seen in a capture; **JA3/JA4 fingerprinting**
- [ ] What you can still learn from encrypted traffic (SNI, cert, size, timing)

**Wireshark Fundamentals**
- [ ] Capture filters (BPF) vs display filters — different syntaxes, different times
- [ ] Following streams; exporting objects
- [ ] Statistics: conversations, endpoints, protocol hierarchy, IO graph
- [ ] Expert information
- [ ] Custom columns and profiles for analysis speed
- [ ] Decryption of TLS with keylog file

**Tcpdump Filters**
- [ ] BPF syntax: primitives, and/or/not
- [ ] Byte-offset filtering — `tcp[13] & 2 != 0` style expressions
- [ ] Filtering on flags, header fields, payload bytes
- [ ] Writing and reading pcap files; ring buffers
- [ ] Building a filter that isolates a specific attack pattern

**Packet Engineering**
- [ ] Crafting packets with **Scapy**
- [ ] Spoofing source addresses
- [ ] Constructing malformed / evasive packets deliberately
- [ ] Replaying traffic (`tcpreplay`)
- [ ] Using crafted packets to test whether a detection actually fires

**IDS Fundamentals and Network Architecture**
- [ ] Sensor placement: perimeter, internal, egress
- [ ] Inline (IPS) vs passive (IDS) tradeoffs
- [ ] TAP vs SPAN and what SPAN drops under load
- [ ] Encrypted-traffic blind spots
- [ ] Signature vs anomaly vs behavioral detection

**Intrusion Detection System Rules**
- [ ] Snort/Suricata rule anatomy: header + options
- [ ] `content`, `pcre`, `offset`, `depth`, `distance`, `within`
- [ ] Flow and stream options
- [ ] Thresholds and suppression
- [ ] **Write a rule that detects a given pcap and nothing else**
- [ ] Zeek: logs as the alternative model to signatures
- [ ] Zeek scripting basics

**Advanced IDS Concepts**
- [ ] Tuning: baselining normal before alerting on abnormal
- [ ] False positive reduction without creating blind spots
- [ ] Alert correlation across sensors
- [ ] Evasion: insertion vs evasion attacks, TTL manipulation, fragmentation
- [ ] Detection-rule lifecycle and decay

**SiLK and Other Traffic Analysis Tools**
- [ ] NetFlow / IPFIX — what flow records contain and omit
- [ ] Why flow scales where full packet capture doesn't
- [ ] SiLK: `rwfilter`, `rwstats`, `rwcut`
- [ ] Beacon detection from flow timing
- [ ] Data exfiltration detection from volume asymmetry

**Network Forensics and Traffic Analysis**
- [ ] Building a timeline from a capture
- [ ] Correlating pcap + flow + logs on one incident
- [ ] Extracting files from a capture
- [ ] Establishing "normal" for an environment
- [ ] **Full analysis of an unknown pcap, start to conclusion** ← the exam in miniature

---

### 4.3 GCIH — GIAC Certified Incident Handler

**Exam:** 106 questions · 4 hours · **69%** to pass · CyberLive · pairs with **GX-IH**
**Primary-fit course:** SEC504

**Incident Response and Cyber Investigation**
- [ ] **PICERL**: Preparation, Identification, Containment, Eradication, Recovery, Lessons Learned
- [ ] **DAIR** model and how it differs from PICERL
- [ ] Preparation: what must exist *before* the incident
- [ ] Identification: distinguishing event from incident
- [ ] Containment: short-term vs long-term; the observe-vs-cut decision
- [ ] Eradication vs recovery — different goals
- [ ] Lessons learned: the phase everyone skips
- [ ] Evidence handling, chain of custody, order of volatility
- [ ] Communication and escalation under pressure

**Scanning and Mapping**
- [ ] Network discovery techniques
- [ ] nmap: scan types (`-sS -sT -sU -sV -O`), timing, NSE
- [ ] Service and version enumeration
- [ ] **Detecting scans in logs and captures** (defensive side)
- [ ] Defenses: rate limiting, deception, port knocking

**Understanding Passwords / Attacking Passwords**
- [ ] Identify hash formats on sight: NTLM, NTLMv2, MD5, SHA-*, bcrypt, `$y$`/yescrypt
- [ ] Where hashes live: SAM, NTDS.dit, `/etc/shadow`, LSASS memory
- [ ] Cracking: dictionary, rules, mask, brute force
- [ ] `hashcat` and `John the Ripper` usage
- [ ] Password spraying vs brute force vs credential stuffing — and their log signatures
- [ ] **Pass-the-hash** and why NTLM enables it
- [ ] Defenses: length over complexity, MFA, lockout, credential guard

**Securing Credentials and Data in the Cloud**
- [ ] Cloud IAM models and over-permissioned roles
- [ ] Credential exposure: repos, env vars, metadata service (SSRF → IMDS)
- [ ] Insecure storage: public buckets
- [ ] Cloud logging for credential abuse

**SMB Security**
- [ ] SMB versions and dialect negotiation
- [ ] Share enumeration; null sessions
- [ ] SMB relay attacks
- [ ] Named pipes as a lateral-movement channel
- [ ] SMB signing and hardening
- [ ] Detecting SMB abuse in logs

**Detecting Exploitation and Covert Communications Tools**
- [ ] Metasploit architecture: exploit / payload / encoder / listener
- [ ] Meterpreter behavior and its artifacts
- [ ] **netcat** — bind vs reverse shells (**extends your existing Applying-level netcat work**)
- [ ] Covert channels: DNS, ICMP, HTTP(S)
- [ ] **Beacon detection**: jitter, interval, size regularity
- [ ] Detecting these tools on host and network

**Endpoint Attack and Pivoting**
- [ ] Client-side attack vectors
- [ ] Privilege escalation on Windows and Linux
- [ ] **Pivoting**: port forwarding, SOCKS proxy, SSH tunneling
- [ ] Lateral movement: PsExec, WMI, WinRM, SSH
- [ ] Detecting lateral movement in Event Logs

**Detecting Evasive and Post-Exploitation Techniques**
- [ ] Persistence: registry Run keys, scheduled tasks, services, WMI subscriptions, cron, systemd
- [ ] **Living off the land** (LOLBins/LOLBAS) — why it defeats allowlisting
- [ ] Log tampering and anti-forensics
- [ ] Rootkits and hiding techniques
- [ ] Timestomping
- [ ] Actions on objectives: collection, staging, exfiltration
- [ ] Hunting each of the above

**Web Application Injection Attacks**
- [ ] SQL injection: in-band, blind, time-based
- [ ] Command injection
- [ ] **XSS**: reflected, stored, DOM-based
- [ ] XXE
- [ ] SSRF
- [ ] Template injection
- [ ] Defenses: parameterized queries, output encoding, allowlist validation
- [ ] Detecting injection attempts in web logs

**Exploiting Insecure Web Application References**
- [ ] **IDOR** — insecure direct object references
- [ ] Path traversal
- [ ] Forced browsing / unprotected endpoints
- [ ] Access-control testing methodology

**Web Application API Attacks**
- [ ] REST API structure; verbs and their misuse
- [ ] Authentication: API keys, JWT, OAuth
- [ ] **JWT attacks**: `alg: none`, weak secret, `kid` injection
- [ ] Broken object-level authorization (API IDOR)
- [ ] Rate limiting and enumeration

**Network and Log Investigations**
- [ ] Log sources worth pulling first
- [ ] Windows Event ID triage set
- [ ] Linux auth/audit log triage
- [ ] Timeline construction across sources
- [ ] Pivot-and-correlate methodology

**Malware and AI Assisted Investigations**
- [ ] Static triage: strings, hashes, PE headers, imports
- [ ] Dynamic triage in a sandbox
- [ ] Indicators of compromise vs indicators of attack
- [ ] Using AI tooling to accelerate triage — and where it fabricates

**Integrating LLMs with Offensive Operations**
- [ ] How LLM prompt processing works (system vs user vs tool content)
- [ ] **Prompt injection**: direct and indirect
- [ ] Jailbreaking and guardrail bypass
- [ ] Data exfiltration through model outputs
- [ ] LLM-assisted attacker tradecraft
- [ ] Defenses: input/output filtering, privilege separation, human confirmation
- [ ] *(You have direct prior work here — the Gemini hallucination-flagging experiment)*

---

### 4.4 GPEN — GIAC Penetration Tester

**Exam:** 82 questions · 3 hours · **73%** to pass · CyberLive · pairs with **GX-PT**
**Primary-fit course:** SEC560

**Penetration Test Planning**
- [ ] Pentest vs vuln assessment vs red team — different products
- [ ] Scoping and rules of engagement
- [ ] **Written authorization — the legal line** (get this exactly right)
- [ ] Methodology frameworks: PTES, OSSTMM
- [ ] Reporting: findings, risk rating, remediation guidance

**Reconnaissance**
- [ ] Passive OSINT: WHOIS, DNS, certificate transparency, search operators
- [ ] Subdomain enumeration
- [ ] Metadata harvesting from public documents
- [ ] People/org recon for social engineering
- [ ] Non-technical information gathering

**Scanning and Host Discovery**
- [ ] Host discovery techniques when ICMP is blocked
- [ ] Port scan types and their signatures
- [ ] OS and service version fingerprinting
- [ ] Timing/evasion options
- [ ] Scanning IPv6 (address space defeats brute force)

**Vulnerability Scanning**
- [ ] Authenticated vs unauthenticated scans
- [ ] Nessus / OpenVAS operation
- [ ] Interpreting results; false positive triage
- [ ] Manual verification before reporting

**Exploitation Fundamentals**
- [ ] Exploit vs payload vs shellcode
- [ ] Bind vs reverse shell, and why reverse wins through NAT
- [ ] Staged vs stageless payloads
- [ ] Shell upgrading to a full TTY
- [ ] **Memory-corruption exploitation** (*direct continuation of your stack-direction work*)
- [ ] Mitigations you'll actually hit: ASLR, DEP, canaries

**Metasploit**
- [ ] Framework structure; msfconsole workflow
- [ ] Selecting and configuring modules
- [ ] Payload generation with `msfvenom`
- [ ] Meterpreter post-exploitation commands
- [ ] Handlers, sessions, routing
- [ ] Auxiliary and post modules

**Escalation and Exploitation**
- [ ] Linux privesc: SUID, sudo misconfig, capabilities, cron, kernel exploits
- [ ] Windows privesc: unquoted service paths, weak service perms, token impersonation, AlwaysInstallElevated
- [ ] Enumeration scripts and what they actually check
- [ ] **Pivoting**: port forwards, SOCKS, routing through a session
- [ ] Data exfiltration from compromised hosts

**Password Attacks / Password Formats and Hashes / Attacking Password Hashes / Advanced Password Attacks**
- [ ] Online vs offline attacks
- [ ] Password guessing against live services
- [ ] Hash identification and format conversion
- [ ] hashcat attack modes; rule files; masks
- [ ] Extracting hashes: SAM, NTDS.dit, LSASS, `/etc/shadow`
- [ ] **Pass-the-hash**, pass-the-ticket, overpass-the-hash
- [ ] Responder / NTLM relay
- [ ] Defenses against each

**Kerberos Attacks / Domain Escalation and Persistence Attacks**
- [ ] Kerberos flow in full detail (build on Phase 0.3)
- [ ] **Kerberoasting** — service accounts and crackable TGS tickets
- [ ] **AS-REP roasting**
- [ ] **Golden ticket** and **silver ticket**
- [ ] Delegation abuse: unconstrained, constrained, RBCD
- [ ] DCSync
- [ ] AD ACL abuse; **BloodHound** attack-path analysis
- [ ] Domain persistence mechanisms
- [ ] Detection and defense for each of the above

**Command and Control (C2)**
- [ ] C2 architecture: implant, listener, redirector
- [ ] Common frameworks and their profiles
- [ ] Beaconing: interval, jitter, sleep
- [ ] Channels: HTTP(S), DNS, cloud-service fronting
- [ ] Detection signals (**the same ones you learn from the GCIA side — connect these**)

**Azure Overview, Attacks, and AD Integration / Azure Applications and Attack Strategies**
- [ ] Entra ID fundamentals; tenants, identities
- [ ] Hybrid identity: sync models and their trust implications
- [ ] Azure authentication techniques and token handling
- [ ] Common Entra ID attacks: consent phishing, device code phishing, token theft
- [ ] Federated and SSO environment attacks
- [ ] Azure RBAC and privilege escalation paths
- [ ] Cloud logging for detection

---

### 4.5 GREM — GIAC Reverse Engineering Malware

**Exam:** 66 questions · 3 hours · **73%** to pass · CyberLive · no paired GX
**Primary-fit course:** FOR610

**This is the cert your existing low-level work points directly at** — stack direction,
buffer overflows, and the SAT/CDCL → symbolic execution → angr thread you've been pulling on.

**Malware Analysis Fundamentals**
- [ ] The four analysis types: static properties, behavioral, static code, dynamic code
- [ ] Lab requirements: isolation, snapshots, network simulation
- [ ] Safe handling; why "it's just a file" is wrong
- [ ] Analysis workflow and when to stop

**Static Analysis Fundamentals**
- [ ] File type identification; magic bytes
- [ ] Hashing and threat-intel lookup
- [ ] **PE file format**: headers, sections, entry point
- [ ] Import Address Table — inferring capability from imports
- [ ] Strings extraction and interpretation
- [ ] Entropy as a packing indicator
- [ ] Embedded resources

**Behavioral Analysis Fundamentals**
- [ ] Sandbox setup and instrumentation
- [ ] Process monitoring (Procmon, Process Hacker)
- [ ] Registry and filesystem change tracking
- [ ] Network behavior with simulated internet (INetSim, FakeNet)
- [ ] Forming and testing a hypothesis about a sample

**Core Reverse Engineering Concepts**
- [ ] **x86/x64 assembly**: registers, instructions, addressing modes
- [ ] The stack: push/pop, prologue/epilogue, frame layout
      (*you already have stack growth direction at Understood — this is the payoff*)
- [ ] Calling conventions: cdecl, stdcall, fastcall, x64 ABI
- [ ] Disassembler use: IDA / Ghidra / Binary Ninja
- [ ] Debugger use: x64dbg, breakpoints, stepping, memory inspection
- [ ] Static vs dynamic analysis — when each is the right tool

**Reversing Functions in Assembly**
- [ ] Recognizing function boundaries
- [ ] Identifying parameters and return values from assembly
- [ ] Local variables on the stack
- [ ] Recognizing compiler-generated patterns
- [ ] Reconstructing structs from access patterns

**Malware Flow Control and Structures**
- [ ] Conditional jumps and the flags register
- [ ] Recognizing if/else, switch tables
- [ ] Loop patterns in assembly
- [ ] Arrays and pointer arithmetic
- [ ] Control flow graphs

**Common Malware Patterns**
- [ ] Windows API calls that reveal intent
- [ ] **Code injection**: CreateRemoteThread, APC, SetWindowsHookEx
- [ ] **Process hollowing**
- [ ] API hooking (inline, IAT)
- [ ] Persistence mechanisms as seen from the binary side
- [ ] C2 communication routines

**Analyzing Obfuscated Malware / Unpacking and Debugging Packed Malware**
- [ ] Detecting packing: entropy, section names, tiny import tables
- [ ] Common packers (UPX and beyond)
- [ ] Manual unpacking: find OEP, dump, fix imports
- [ ] Import reconstruction (Scylla / ImpREC)
- [ ] String obfuscation and decoding routines
- [ ] Obfuscated JavaScript deobfuscation

**Identifying and Bypassing Anti-Analysis Techniques**
- [ ] Debugger detection: IsDebuggerPresent, PEB flags, timing checks
- [ ] VM and sandbox detection
- [ ] Anti-disassembly tricks
- [ ] Patching checks out; hiding the debugger
- [ ] Data protection / encrypted config extraction

**Overcoming Misdirection Techniques**
- [ ] Opaque predicates and dead code
- [ ] Control-flow flattening
- [ ] Junk instructions
- [ ] *(**Connect this to your SAT work**: symbolic execution + solvers are the automated
      answer to path explosion — the angr/Z3 thread lands exactly here)*

**Analyzing Malicious Office Macros / PDFs / RTF Files**
- [ ] OLE/OOXML structure; extracting VBA
- [ ] VBA deobfuscation; `olevba`, `oletools`
- [ ] PDF object structure; JavaScript and embedded files; `pdf-parser`, `peepdf`
- [ ] RTF structure; embedded objects; **shellcode extraction and emulation** (`scdbg`)

**Examining .NET Malware**
- [ ] CIL/MSIL vs native code
- [ ] Decompilation: dnSpy / ILSpy
- [ ] .NET obfuscators and deobfuscation
- [ ] Debugging managed code

---

### 4.6 GICSP — Global Industrial Cyber Security Professional

**Exam:** 82 questions · 3 hours · **71%** to pass · CyberLive · no paired GX
**Primary-fit course:** ICS410

**Chosen because you work maritime on the lower Mississippi.** Vessel systems, locks, terminals,
and port infrastructure are ICS/OT. Your day job is domain expertise most candidates buy.

**ICS Overview & Concepts**
- [ ] What an industrial control system is; process control vs IT data processing
- [ ] Roles: engineer, operator, technician, IT vs OT staff
- [ ] **IT vs ICS priority inversion** — availability and safety over confidentiality
- [ ] Safety systems (SIS) as a distinct category
- [ ] Physical security as a control-system concern
- [ ] Real ICS incidents: Stuxnet, Ukraine grid, TRITON

**ICS Components & Architecture**
- [ ] **Purdue Reference Architecture** levels 0–5
- [ ] Level 0: sensors and actuators
- [ ] Level 1: PLCs, RTUs, IEDs
- [ ] Level 2: HMI, SCADA supervisory
- [ ] Level 3: site operations, historians
- [ ] Zones and conduits (IEC 62443)
- [ ] DMZ between OT and IT
- [ ] Secure architecture design using levels and zones

**PERA Level 0 & 1 Technology Overview and Compromise**
- [ ] Sensor and actuator types; analog vs digital signaling
- [ ] PLC architecture; ladder logic; scan cycle
- [ ] RTU vs PLC
- [ ] Firmware and its update problem in OT
- [ ] Attacks: logic manipulation, sensor spoofing, firmware implant
- [ ] Why patching is often genuinely impossible here

**PERA Level 2 & 3 Technology Overview and Compromise**
- [ ] HMI function and compromise impact
- [ ] SCADA master/server architecture
- [ ] Historians and data diodes
- [ ] Engineering workstation as the highest-value target
- [ ] Attacks at these levels

**Protocols, Communications, & Compromises**
- [ ] **Modbus** — and its total absence of authentication
- [ ] DNP3 and secure authentication additions
- [ ] EtherNet/IP, PROFINET, OPC / OPC-UA
- [ ] Serial vs Ethernet-based control communication
- [ ] Protocol attacks: replay, injection, unauthorized command
- [ ] Basic cryptography applied in ICS constraints

**Hardening & Protecting Endpoints**
- [ ] Windows hardening in an OT context (legacy OS reality)
- [ ] Unix/Linux hardening in OT
- [ ] Patching strategy when downtime is unacceptable
- [ ] Application allowlisting as the OT-appropriate control
- [ ] Removable media controls

**Wireless Technologies & Compromises**
- [ ] Industrial wireless: WirelessHART, ISA100, 802.11 in plants
- [ ] Radio/telemetry links
- [ ] Attack vectors and jamming
- [ ] Wireless defenses in industrial settings

**Intelligence Gathering & Threat Modeling**
- [ ] ICS threat landscape and actor types
- [ ] **Shodan** and internet-exposed control systems
- [ ] Threat modeling for a control process
- [ ] ICS-specific ATT&CK (ATT&CK for ICS)

**Risk Based Disaster Recovery & Incident Response**
- [ ] Risk measurement in a safety-critical context
- [ ] Consequence-driven analysis
- [ ] IR when you cannot simply power off
- [ ] Recovery planning and manual-operation fallback

**ICS Program & Policy Development**
- [ ] Building a security program for OT
- [ ] Enforceable policy writing
- [ ] Standards: IEC 62443, NIST SP 800-82
- [ ] Governance across the IT/OT organizational split

---

## 5. Applied Knowledge tracks (GX-*)

These are **not new-material exams.** Each is 25 hands-on lab challenges in 4 hours — roughly
**9.6 minutes per problem** — open book, in a live VM. They test whether you can *do* the
Practitioner material fast, without a signature course to prepare from.

So the atoms below are not concepts. They are **fluency drills**. The right preparation is a
tool-speed and note-quality regimen, not more reading.

### 5.1 Universal preparation for all four

- [ ] **Build an exam index.** Open book only helps if you can find it in 30 seconds.
- [ ] Practice with a **timer**, always. Untimed practice teaches the wrong skill.
- [ ] **Triage discipline**: identify the questions to skip. 25 problems, 4 hours — a rabbit hole costs two answers.
- [ ] Know your VM environment cold: where tools live, how to move data between them
- [ ] Command-line fluency to muscle memory — no syntax lookups for common tools
- [ ] Build a personal cheat-sheet repo of one-liners, organized by task not by tool
- [ ] Practice on real artifacts: pcaps, disk images, live-fire labs, CTFs

### 5.2 GX-CS — Experienced Cybersecurity Specialist

**Recommended after:** GSEC · Areas: network security analysis and tools; Windows and Linux OS
security evaluation; advanced security tools and techniques; common attacks and defenses;
implementing overall cybersecurity.

- [ ] Assess an unknown Windows host's security posture under time pressure
- [ ] Assess an unknown Linux host's security posture under time pressure
- [ ] Analyze a capture and state what happened, in minutes
- [ ] Identify a given attack from mixed evidence
- [ ] Apply a specific hardening change and verify it took effect
- [ ] Cross-domain reasoning — the breadth of GSEC applied, not recited

### 5.3 GX-IA — Experienced Intrusion Analyst

**Recommended after:** GCIA · Areas: protocol analysis and capture-file evaluation; network
forensics and artifact analysis; intrusion analysis with Wireshark, Scapy, and IDS tools.
Seven domains: Advanced Analysis, Application Traffic Analysis, IDS Application and Analysis,
Malicious Traffic Analysis, Network Forensics, Network Traffic Analysis, Protocol Analysis.

- [ ] Wireshark to expert speed — filters and statistics without hesitation
- [ ] tcpdump BPF including byte-offset filters, written cold
- [ ] Scapy packet construction from memory
- [ ] Write and validate an IDS rule against a supplied pcap
- [ ] Full network-forensic reconstruction from a capture
- [ ] Identify a malicious protocol behavior among heavy benign noise
- [ ] Extract files and artifacts from traffic
- [ ] Flow analysis at volume

### 5.4 GX-IH — Experienced Incident Handler

**Recommended after:** GCIH · Areas: incident handling and computer crime investigation;
computer and network hacker exploits; reconnaissance and in-depth analysis of attacks,
infrastructure, and vulnerabilities.

- [ ] Execute the full IR process against a live scenario
- [ ] Compromise assessment on a supplied host
- [ ] Reconstruct an attack chain from partial evidence
- [ ] Perform the attack yourself when the question requires it
- [ ] Log analysis at speed across mixed sources
- [ ] Identify persistence on an unfamiliar system
- [ ] Recommend containment that fits the scenario's constraints

### 5.5 GX-PT — Experienced Penetration Tester

**Recommended after:** GPEN · Areas: environment reconnaissance; network and vulnerability
scanning; password attacks; Active Directory attacks; vulnerability exploitation; privilege
escalation; C2; Linux and Windows pentesting tools.

- [ ] Recon an unknown environment under time pressure
- [ ] Scan and prioritize targets fast
- [ ] Execute password attacks end to end
- [ ] Execute AD attacks end to end (Kerberoast → crack → escalate)
- [ ] Exploit and escalate on both Linux and Windows
- [ ] Establish and use C2
- [ ] Pivot to a segmented target
- [ ] Tool fluency: nmap, Metasploit, hashcat, BloodHound, Impacket, no lookups

---

## 6. How to run this

1. **Phase 0 comes first, and networking comes first inside Phase 0.** It is the confirmed gap
   and the prerequisite for the most important cert in the portfolio.
2. **One session per topic cluster**, matching the existing Tech+ approach — keeps transcripts
   attributable when `/update-primer` runs.
3. **Atoms here are the map; the primer is the record.** Ticking a box here means "covered."
   Only the primer's Skills table records *demonstrated* mastery, with evidence.
4. **Revisit this file yearly.** GIAC just restructured the entire GSE — assume it changes again.
5. **Don't buy anything yet.** Confirm current pricing and requirements at giac.org before
   committing to a cert or a course. The GX discount for holding the paired Practitioner cert
   is the one structural fact worth planning around.

---

## Sources

- [GIAC Portfolio Certifications](https://www.giac.org/get-certified/giac-portfolio-certifications)
- [Sorting Through the Noise: GIAC's New Path to the GSE](https://www.giac.org/blog/sorting-through-the-noise-giac-s-new-path-to-the-gse)
- [GIAC Certification Categories: Practitioner and Applied Knowledge](https://www.giac.org/blog/giacs-new-certification-categories-practitioner-and-applied-knowledge)
- [GSEC](https://www.giac.org/certifications/security-essentials-gsec/) ·
  [GCIA](https://www.giac.org/certifications/certified-intrusion-analyst-gcia/) ·
  [GCIH](https://www.giac.org/certifications/certified-incident-handler-gcih/) ·
  [GPEN](https://www.giac.org/certifications/penetration-tester-gpen/) ·
  [GREM](https://www.giac.org/certifications/reverse-engineering-malware-grem/) ·
  [GICSP](https://www.giac.org/certifications/global-industrial-cyber-security-professional-gicsp/)
- [GX-CS](https://www.giac.org/certifications/experienced-cyber-security-gxcs) ·
  [GX-IA](https://www.giac.org/certifications/experienced-intrusion-analyst-gxia) ·
  [GX-IH](https://www.giac.org/certifications/experienced-incident-handler-gxih) ·
  [GX-PT](https://www.giac.org/certifications/experienced-penetration-tester-gxpt)
- [Best Practices for GIAC Applied Knowledge Exams](https://www.giac.org/how-to-prepare/applied-knowledge)
