# Session Transcript — CompTIA Tech+ (FC0-U71) Diagnostic & Study Plan

**Date:** 2026-07-31
**Track(s):** CompTIA Tech+ certification (new); touches Infrastructure/networking (new),
Databases (new), Applications & OS (new), IT concepts/notation (new), Software development
(existing), Security (existing)
**Format:** Diagnostic quiz — Connor answered 10 questions cold, no lookups, then received
straight grading and corrections. Scaffolded per Section 1; deep instruction deliberately
deferred to per-domain sessions.

---

## Session purpose

Connor set a goal of sitting the **CompTIA Tech+ (FC0-U71)** exam, the 2025 replacement for
ITF+. Timeline chosen: **2–4 weeks**. Rather than walking the objectives in order, he opted
for a **diagnostic first** to locate real gaps, on the reasoning that his existing primer
already puts him *above* Tech+ level in several domains (Linux/CLI, Security/CTF, compilers,
Python) and it would be wasted time to re-cover them.

The six Tech+ domains: IT Concepts and Terminology; Infrastructure; Applications and
Software; Software Development Concepts; Data and Database Fundamentals; Security.

---

## The diagnostic — 10 questions, all six domains

| # | Domain | Verdict |
|---|---|---|
| 1 | IT Concepts — hex | Answer correct, reasoning wrong |
| 2 | IT Concepts — units/storage math | Arithmetic wrong (6× error); second part wrong |
| 3 | Infrastructure — router/switch/AP | **Gap** |
| 4 | Infrastructure — volatile storage | Partial (RAM correct, term not named) |
| 5 | Applications — OS/driver/firmware stack | Sequence correct, **direction inverted** |
| 6 | Applications — software licensing | **Gap** |
| 7 | Software Dev — compiled vs interpreted | Correct shape, incomplete |
| 8 | Databases — keys, DB vs flat file | **Total gap** (self-reported, honest) |
| 9 | Databases — structured/semi/unstructured | Wrong (JSON mislabeled) |
| 10 | Security — CIA triad, authn vs authz | **Full marks** |

---

## Concepts established / corrected (in order)

### 1. Hexadecimal ↔ binary (Q1)
Connor answered `0xFF` = 255 correctly, but justified hex with *"it always doubles when
adding another digit."* **Corrected:** each hex digit multiplies the range by **16**, not 2.
The real reason hex pairs with binary is **16 = 2⁴** — one hex digit maps to exactly 4 bits,
two hex digits to exactly 8 bits = one byte, with no remainder. `0xFF` = `1111 1111`.
Decimal cannot do this because 10 is not a power of 2; `255` tells you nothing about the bit
pattern, `FF` tells you "all bits on."

### 2. Storage arithmetic and the MB/MiB units lie (Q2)
Scenario: 200 vessels × one 450-byte record per 10 seconds, per day.

- Connor computed 129,600,000 bytes. **Wrong by exactly 6×.** He used 1,440 intervals per
  day — that is *minutes*. At one record per 10 seconds it is 86,400 ÷ 10 = **8,640
  intervals**. Correct: 200 × 8,640 × 450 = **777,600,000 bytes ≈ 778 MB/day**.
- On "why does a 500 GB drive show as 465?", Connor guessed **partitioning / filesystem
  overhead**. **Corrected:** those are real but negligible. The actual cause is that the
  manufacturer and the OS use **two different definitions of "GB"** — drive makers use
  decimal (1 GB = 1,000,000,000 bytes), Windows reports binary (1,073,741,824 bytes) but
  still labels it "GB." 500,000,000,000 ÷ 1,073,741,824 = **465.7**. Nothing is missing; it
  is a units mismatch. The honest binary unit name is **gibibyte (GiB)**.
- Same principle applied back to his own figure: 777,600,000 bytes = 778 MB but only
  742 MiB.

### 3. Volatile vs. non-volatile — and a cross-domain false friend (Q4)
Connor correctly identified **RAM** as losing contents on power loss, but did not name the
property (**volatile**). He asked to expand on the `volatile` bonus question.

**Established:** the two meanings are **unrelated mechanisms** that share an etymology
(*unstable, liable to change*):
- **Storage volatility** — contents are lost without power (RAM = volatile; SSD, HDD =
  non-volatile).
- **C++ `volatile`** — a compiler directive: "this variable may change outside anything
  visible in the control flow; do not cache it in a register, do not optimize the read
  away."

Direct connect-back: the C++ keyword is exactly why Connor's earlier **stack-direction
probe** survived compiler optimization.

### 4. The firmware → driver → OS → application stack (Q5)
Asked for closest-to-hardware first, Connor answered *application, OS, device driver,
firmware* — the **exact reverse**. The sequence/relationship was right; only the direction
was inverted.

Flagged as a possible **recurring pattern**: this rhymes with his AST trap (*root = last
operation, not first token*). Both times the ordering relationship was correct and the
direction flipped.

**Driver definition corrected as too narrow.** Connor described it as "the interpreter for
signals from input devices." Drivers also serve GPUs, NICs, storage controllers, printers.
The real job: **the OS speaks one generic language ("write this block"); every device has
its own command set; the driver is the device-specific translator.** An OS cannot ship with
built-in knowledge of every device ever manufactured.

### 5. Compiled vs. interpreted, and where Python actually sits (Q7)
Connor had the right shape — compiler translates to machine code, interpreter executes via
an interpreter program — but flagged uncertainty about "the exact path."

**Established:** Python is **not cleanly either.** CPython *compiles* source to **bytecode**
(`.pyc`), which a **virtual machine** then interprets. Strong connect-back: Connor already
learned the front half of this pipeline in the AST session — **source → tokens → AST →
bytecode**. He had studied the middle of Python's own compiler without framing it that way.

### 6. Structured vs. semi-structured vs. unstructured data (Q9)
Connor offered **JSON as structured** and raw binary as unstructured. The binary example was
fine; **JSON was mislabeled** — it is the textbook example of **semi-structured**.

The three-way split established:
- **Structured** — fixed schema, rows and columns (relational table, CSV).
- **Semi-structured** — carries tags/keys that describe itself, but no rigid schema
  (JSON, XML).
- **Unstructured** — no predefined model (images, video, free text, raw binary).

Anchored to his project: the `aisstream.io` AIS feed arrives as **JSON**, i.e.
semi-structured — which is precisely why it must be transformed before landing in a
relational database.

### 7. Security (Q10) — no corrections needed
Connor named **confidentiality, integrity, availability** unaided, correctly identified
**availability** as the pillar broken by ransomware-with-no-backups, and gave a crisp,
correct distinction: **authentication = proving who you are; authorization = whether you
have permission to do this.** Only addition offered (exam-relevant): modern ransomware
usually breaks **confidentiality** too, via exfiltration before encryption ("double
extortion").

---

## Confirmed gaps (no instruction given — deferred to dedicated sessions)

- **Q3 Infrastructure — router vs. switch vs. access point.** Connor: routers "send traffic
  where it needs to go" (directionally right, imprecise); **switch — no knowledge at all**;
  AP guessed as "the actual connection point that is connected to rather than the router."
  Not taught this session.
- **Q6 Applications — software licensing models.** Only "open source" known, and not
  confidently as a *licensing* model. Requested depth; deferred.
- **Q8 Databases.** Self-reported total gap, stated honestly rather than guessed. Primary
  keys, foreign keys, and DB-vs-flat-file reasoning all untouched. Confirmed as the single
  largest hole.

---

## Connor's performance / evidence

- **Security is genuinely strong** — the only question answered with full marks and no
  hedging. Consistent with his existing Security/CTF track sitting above Tech+ depth.
- **Honest calibration.** On Q6, Q8, and Q9 he explicitly said he didn't know and asked to
  go deeper rather than bluffing. On Q4 and Q7 he stated the boundary of what he was sure
  of. This makes the diagnostic signal clean and is worth preserving as a habit.
- **Misses cluster tightly** — Infrastructure and Databases account for the overwhelming
  majority of lost ground. The other errors were single-point corrections on concepts he
  substantially had.
- **Process instinct:** Connor independently proposed splitting further study into separate
  sessions to cut context clutter. Endorsed, with a sharper reason supplied — his
  primer workflow is per-session-transcript → `/update-primer`, so one session per domain
  produces attributable skill evidence rather than one sprawling blur.

---

## Mistakes / corrections (candidate traps)

1. **Positional-notation reasoning:** justified hex with "doubles per digit." Each digit
   multiplies range by the **base** (16), and hex pairs with binary because **16 = 2⁴**
   (1 hex digit = exactly 4 bits).
2. **Interval arithmetic:** used 1,440 (minutes/day) where the rate was one event per
   10 seconds (8,640 intervals/day). Check *what unit the interval is actually in* before
   multiplying out a daily total.
3. **"Missing" drive capacity:** reached for partitioning / filesystem overhead. The cause
   is a **decimal-vs-binary units mismatch** (GB vs GiB), not consumed space.
4. **Ordering direction inverted:** asked for closest-to-hardware first, gave the list
   furthest-first. Echoes the AST trap (*root = last operation, not first token*) — the
   relationship is right, the direction flips. Slow down on "order these" prompts.
5. **Driver scope too narrow:** framed drivers as input-device-only. Drivers translate for
   *all* hardware classes.
6. **JSON classified as structured** — it is **semi-structured**. Test: fixed schema of rows
   and columns → structured; self-describing tags but no rigid schema → semi-structured;
   no model at all → unstructured.
7. **Cross-domain false friend:** conflating storage **volatility** (lost on power off) with
   the C++ **`volatile`** keyword (compiler optimization barrier). Same word, unrelated
   mechanisms.

---

## Study plan agreed (3 weeks, one session per block)

**Week 1 — the two real gaps**
1. **Databases I** — relational model, tables/rows/columns, primary & foreign keys, why not
   a CSV (concurrency, integrity, query). Anchored to `ais-tracker` vessel data.
2. **Databases II** — structured/semi/unstructured, SQL vs NoSQL, CRUD, basic `SELECT`.
   Ties into the JSON feed.
3. **Infrastructure I** — switch vs. router vs. AP, MAC vs. IP, LAN/WAN, why a home router
   box is secretly all three devices.

**Week 2 — finish infrastructure, then applications**
4. **Infrastructure II** — storage & memory (volatile/non-volatile, RAM vs SSD vs HDD),
   CPU/GPU, peripherals and connectors. Largely memorization.
5. **Infrastructure III** — networking basics: IP addressing, DNS, DHCP, common ports,
   wired vs. wireless.
6. **Applications & OS** — firmware→driver→OS→app stack, file systems, OS types, **software
   licensing models** (trap: open source ≠ free ≠ public domain).

**Week 3 — cleanup and drill**
7. **IT Concepts** — notational systems, units and the MB/MiB distinction, data value,
   troubleshooting methodology.
8. **Software Dev + Security review** — both mostly vocabulary mapping for Connor. Fast.
9. **Full practice exam + weak-spot retest.**

---

## Suggested next steps (not yet done)

- **Download the official FC0-U71 exam objectives PDF** from CompTIA (gated behind a
  lead-capture form, so it must be fetched manually) and drop it in this repo, so each
  session can be anchored to real objective numbers instead of a reconstructed domain list.
  Worth doing before session 1.
- Begin with **Databases I** — the largest confirmed gap and the highest-value first block.
- Percentage weights per domain were **not** stated this session (deliberately — not
  confident from memory); confirm them from the objectives PDF and use them to re-rank the
  plan if the split differs materially from the assumption that Infrastructure is the
  heaviest domain.
