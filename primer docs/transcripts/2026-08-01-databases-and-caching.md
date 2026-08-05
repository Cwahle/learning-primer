# Session Transcript — Databases & Caching from First Principles

**Date:** 2026-08-01
**Track(s):** Databases & data modeling (was `New` — first real instruction); Compilers/
language tooling (binary search, tree structures); Python/`ais-tracker` (anchor); CompTIA
Tech+ (Databases domain — one of the two confirmed exam gaps)
**Format:** Socratic derivation. Connor was given no definitions up front — every concept
was reached by pushing on his own file-based design until it broke. Scaffolded per
Section 1; corrections given bluntly on request.

---

## Session purpose

Connor asked to learn "databases and caches." Databases was the largest confirmed gap in the
primer (total, self-reported on the 2026-07-31 Tech+ diagnostic) and the first block of the
agreed study plan. Caches were paired in deliberately — both answer "where does data live
and how fast can I get it back," and the read/write tradeoff is identical in both.

Deliberate method: **do not define a database.** Instead, have him design storage for
`ais-tracker` using only files, then break it. Every relational concept was introduced only
after he hit the failure it exists to solve.

Anchor scenario used throughout: ~200 vessels on the lower Mississippi, each AIS-reporting
every ~10 seconds, retained six months. Two target queries:
- **Q1** "Where was M/V X at 3pm last Tuesday?" (keyed by vessel)
- **Q2** "Which vessels crossed mile marker 95 northbound in June?" (not keyed by vessel)

---

## Arc of the session

| # | Beat | Verdict |
|---|---|---|
| 1 | Why storage exists; design a file-based scheme | Design good, failure analysis **missed** |
| 2 | Cost of a linear scan (arithmetic) | **Correct — cleared an old trap** |
| 3 | Why per-vessel files destroy Q2 | **Missed the killer** |
| 4 | Dictionary → sorted order & its insert cost | **Strong; reinvented the B-tree** |
| 5 | Choosing a key: name vs MMSI | Self-corrected mid-answer to MMSI |
| 6 | Normalization | **Derived unaided** |
| 7 | Building the two tables | Table 1 correct; **PK of table 2 wrong twice** |
| 8 | Surrogate keys | Concept stated unaided; **built a key-reuse bug on top** |
| 9 | Query plan for Q2 | **Strong — unaided transfer of binary search** |
| 10 | Indexes | Asked for explicit instruction; received it |
| 11 | Store vs. compute derived data | Right tradeoff, missed the deciding question |
| 12 | Caching ETA: TTL + invalidation | **Strong; missed thresholds** |

---

## Concepts established / corrected (in order)

### 1. Why persistence exists
Connor justified storage as "otherwise you'd have to just remember everything." **Sharpened:**
a Python dict *is* remembering — what kills it is process exit, not capacity. Named the
property: **non-volatile storage survives power loss, RAM does not.** Direct callback to the
volatile/non-volatile item from the Tech+ diagnostic, and to the C++ `volatile` false-friend
already in the traps list.

### 2. His file design — better than he knew
Proposed: one file per vessel, append new reports to the existing file, create a file on
first sighting. **Credited:** this is *partitioning by key*, a real strategy used by
production time-series systems. Not a toy answer.

### 3. The failure he missed — findability ≠ presence
Asked what breaks on the "3pm last Tuesday" query, Connor said he didn't see a problem so
long as timestamps were stored. **Corrected bluntly:** storing the timestamp is necessary and
nowhere near sufficient. **Data being present in a file and data being findable are separate
problems.** His design solved the first and did nothing for the second.

### 4. Rate arithmetic — OLD TRAP CLEARED
Asked for the line count of one vessel's six-month file, Connor computed **~1.57 million**
(actual 1,555,200 = 8,640/day × 180) and **60,480** for seven days — both correct.

**Notable:** he used **8,640 intervals/day**, not 1,440. This is the exact calculation he was
6× low on during the 2026-07-31 diagnostic. Corrected unprompted, one session later. The
"rate arithmetic" trap should be considered cleared.

### 5. Why you cannot jump to line N
Connor assumed a program "can only go one line at a time" without saying why. **Installed the
reason: records are variable-length.** No formula converts line number → byte offset, so
finding line N means reading from byte 0 and counting newlines. A file has no table of
contents.

**Corrected an overstatement of his:** he estimated "a few seconds." ~125 MB is well under a
second on SSD. The problem is not the stopwatch — it is that cost is **linear in file size,
and the file grows forever.** Every day makes every past query permanently slower.

### 6. The Q2 catastrophe — layout encodes one access path
Connor described the *logic* of the mile-marker query but skipped the fatal step. **Corrected:**
with 200 per-vessel files and no way to know in advance which vessels went near MM95, you must
open and fully scan **all 200 files — ~311 million lines** — to answer one question.

**Principle installed:**
> A storage layout physically encodes exactly one access path. Questions asked in that
> direction are cheap; every other question degenerates to brute force. **Organizing data by
> one thing is the same act as not organizing it by everything else.**

Connor's own instinct — *"that seems like a lot of steps, there would be a better way"* — was
credited as the correct question, and used as the door to indexes.

### 7. The dictionary — sorted order, binary search, and its cost
Asked how one finds a word in a 60,000-word dictionary in seconds:

- **(a)** Connor identified **pre-sorted order** as the enabling property and described opening
  to an approximate point and searching from there. Named for him: **binary search**
  (and interpolation search). Given the number: 1,555,200 records → **21 probes** vs. up to
  1,555,200 sequential reads.
  - **Added what he left implicit:** sorted order alone is insufficient — you also need
    **random access**. Sorting a front-to-back-only file buys nothing. Both are required.
- **(b)** **Strongest single answer of the session.** Connor identified that inserting into a
  sorted book forces **every downstream page to shift**, that physical rebinding is expensive,
  and then independently proposed **a binder with removable pages**, noting it would require
  changing the entire structure.

  **This is a B-tree.** He derived the motivation for the most common index structure in
  existence, from a dictionary, unaided. Told so explicitly and told to keep the mental model.

**Law derived from (a)+(b):**
> Sorted order makes reads fast and writes slow. Every time. No structure escapes this —
> databases only let you choose where to pay.

### 8. Natural vs. surrogate keys
Connor initially argued for **vessel name** as the key (river crews work by name, not MMSI),
then **changed his own position mid-answer to MMSI** while writing — the self-correction was
flagged as the notable event, not the answer.

**Failure modes of name-as-key made concrete:** rename → new file created → history silently
severed; collision → two hulls merged into one track; reuse → retired name given to a new
build. Emphasized that **all three fail silently** — clean, confident, incomplete answers.
Tied to his already-`Understood` failure-mode asymmetry skill.

**Scoping accepted, boundary named:** Connor judged sale/re-flagging irrelevant for lower-
Mississippi traffic. Accepted, but he was told the mechanism — **the first three MMSI digits
are the MID (country code), so re-flagging issues a new MMSI.** MMSI is *more* stable than
name, not stably stable.

**Rule installed:**
> Anything the real world controls can change. Your primary key should be something only
> you control.

Production shape given: surrogate `vessel_id` as PK, MMSI as a unique-indexed natural key,
name as a mutable attribute.

### 9. Normalization — DERIVED UNAIDED
Connor proposed *"index by MMSI and then just append the vessel name to that based on current
data,"* reasoning from uniform byte size. **This is normalization**, and he was told the
payoff is bigger than he realized: the name is stored **once**; the 1.5M position rows store
only the key; a rename is **one write** and all history is instantly correct.

> Every fact lives in exactly one place. Duplication across a million rows means a rename is
> a million writes — and any partial failure leaves the data disagreeing with itself. This is
> a correctness mechanism, not a space optimization.

### 10. Building the tables — Connor asked for the format
He explicitly asked what a table "looks like in my head." Given: table/relation, column/
field/attribute, row/record/tuple, primary key — plus a fully worked two-table `artists`/
`songs` example with the FK placed but **deliberately unexplained**, so the direction rule
stayed his to derive.

Also connected: a table's rigid identical-columns-per-row property **is** what "structured
data" means — the distinction he missed on the diagnostic when he called JSON structured.

**His schema:**
- **Table 1 — correct.** MMSI (PK), vessel name, home port/country (his own addition, good
  initiative). *Flagged:* he dropped the surrogate key he had just learned. Acceptable
  simplification, but he was told he made it.
- **Table 2 — PK wrong twice.** First **speed**, then self-corrected to **lat/long**. Both
  wrong.

### 11. Primary key = a uniqueness promise (main correction)
**Diagnosis of the error, which matters more than the answer:** Connor picked the column that
felt most *characteristic* of a position report. A PK is not the most important column — it is
a promise that **no two rows will ever share this value.**

Test case used, from his own trade: **a towboat moored nine hours, reporting every 10 seconds
= 3,240 rows with identical lat/long.** He was made to compute it.

- **Composite key** introduced as the option he skipped: `(vessel_id, timestamp)` — neither
  unique alone, unique as a pair. Legitimate; surrogate still preferred (smaller, immutable,
  survives duplicate timestamps).

### 12. Foreign keys — right answer, general rule installed
Connor placed the FK in the position table, reasoning that positional data needs something
tying it to identity, and cross-referenced the earlier retention discussion (good connection).

**Rule given, simpler than his argument:**
> The foreign key lives on the **many** side.

Shown why the reverse is impossible: `vessels` would need a column holding 1.5 million
position IDs, but a column holds one value. Each report belongs to exactly one vessel, so one
key fits.

### 13. Smaller modeling corrections
- **`position (lat/long)` must be two columns.** One fact per column — otherwise every query
  splits strings and nothing indexes or compares properly.
- **`heading` ≠ direction of travel. (REPEAT ERROR — corrected twice this session.)** AIS
  heading is degrees 0–359, reported by the transponder. Upriver/downriver is **derived** from
  consecutive positions. They genuinely differ: a towboat holding station in current can point
  upriver while drifting downriver.
- Noted that latitude is a rough proxy for river mile on a river that winds as hard as the
  lower Mississippi.

### 14. Key reuse — a mechanism built for a non-existent problem
Connor stated the surrogate key concept correctly and unaided: *"it doesn't matter exactly
what the data is so long as it's something you have control over."* Then built a **12-digit
counter that recycles back to `000000000001`**, sized against the purge policy.

**Three corrections:**
1. **This is key reuse — the exact failure he himself diagnosed** for vessel names two
   messages earlier. Any archive, backup, export, or log referencing report `47` now points at
   a different event.
2. **It makes correctness depend on the purge job running forever, on time.** Never rest data
   integrity on a janitor.
3. **He solved a problem that does not exist.** His own numbers: 1,728,000 rows/day; a
   12-digit counter lasts **~1,584 years**. A standard `BIGINT` lasts **~14.6 billion years** —
   longer than the universe has existed.

**Corollaries:** the database issues the counter (`BIGSERIAL`/`AUTO_INCREMENT`/`IDENTITY`) —
you never implement it. And **purging ≠ recycling**: gaps in a PK sequence are normal and
free, because the value is meaningless — his own point, turned back on him.

### 15. Query plan for Q2 — unaided transfer
Asked to answer the mile-marker question against the new schema in plain English, Connor
produced: binary-search the timestamps to isolate June → group rows by vessel → check for
points below and above the threshold → resolve vessel id to name.

**This is a real query plan**, and it applied binary search from the dictionary beat four
messages earlier to a different problem, unprompted. Named for him: **index range scan →
selection (`WHERE`) → `GROUP BY` → `JOIN`.** The join was flagged as the payoff of
normalization — the name isn't in the fact table, so the FK is followed once per vessel.

**Correction:** binary search requires the rows be *sorted* by timestamp. Insert order only
*happens* to approximate this for a live feed (late/out-of-order/backfilled reports break it).
Binary search needs a guarantee, not a coincidence — which motivated indexes.

### 16. Indexes (explicitly requested instruction)
Connor said he could not picture this. Given a concrete before/after: an unsorted
`position_reports` sample, then the index on `timestamp` shown as a literal second table of
**sorted values → row pointers**.

> An index is a separate, smaller structure holding **one column kept sorted, plus a pointer
> to the full row.** The data itself never moves — writes stay cheap, and you can have several
> indexes, each a new access path into the same unsorted pile.

- **Composite indexes** sort on multiple columns in order; `(vessel_id, timestamp)` serves both
  his grouping and his ordering. Column order matters.
- **Primary keys are automatically indexed.**
- **Foreign keys usually are not** — an unindexed FK makes every JOIN a full scan. Flagged as
  one of the most common real-world performance bugs.
- **The bill:** 1,728,000 rows/day × 3 indexes ≈ **5.2M index insertions/day**, each into a
  sorted structure — i.e. the dictionary-rebinding problem, which is why it's a B-tree.
  Explicit callback to his binder.

Unified: **index, cache, and a stored derived column are one idea wearing three hats** — a
redundant structure trading space and write cost for read speed.

### 17. Store vs. compute — the deciding question he skipped
Asked whether to store a derived `direction` column, Connor gave the correct general tradeoff
(compute cost vs. **staleness** — his word, unaided) but concluded it was a coin flip.

**Corrected:** the question that settles it is **"can the source data change?"** A position
report is a *historical fact* and will never be edited — **immutable source → the derived value
can never go stale.** Not a tradeoff; a free lunch. He should have concluded, not shrugged.

> Derived data goes stale exactly when its source changes. Enumerate every way the source can
> change and you have enumerated every case you must invalidate.

**One real exception given:** out-of-order arrival. A report landing after a later one was
already processed leaves that row's derived direction silently wrong forever.

### 18. Caching
**Definition given:** a cache is a **second, faster copy of data whose real home is somewhere
else.** Everything follows from *second copy* — a copy can disagree with the original, and
knowing when to discard it is **cache invalidation** (Karlton's two-hard-problems joke noted).

**Memory hierarchy** taught as Tech+ material and connected to his existing knowledge: register
→ L1/L2/L3 → RAM → SSD → disk/network, each level a small fast copy of the one below,
~100× slower per step. Noted the top four levels are **volatile**, which is *why* a cache must
always be rebuildable — losing a cache must never lose data. That property is what makes it a
cache rather than storage.

### 19. Caching the ETA — strong, with one real bug
Applied to `ais-tracker`'s ETA-to-dock feature. Connor proposed **3–5 minutes (or 30s for
higher accuracy), invalidating on speed or course change**, and argued staleness was tolerable
because *"ships are slow"* and *"it isn't actually a pain point."*

- Both mechanisms named for him: **TTL** and **event-based invalidation** — the two used by
  real systems, both derived himself.
- His TTL was defended from **physics**, not vibes — credited.
- **His best insight:** cache tolerance is a property of the **consumer**, not the data. The
  same 4-minute-old ETA is fine for a deckhand and catastrophic for collision avoidance. You
  cannot pick a TTL by looking at the data — only by asking who reads it and what they do next.

**Correction 1 — "there is no cost" overreaches; staleness is asymmetric by direction.**
Cached ETA too *late* (she slowed) → crew waits, cheap. Cached ETA too *early* (she sped up)
→ dock not ready, crew not on station, expensive. Same 20 minutes, wildly different bills. The
sophisticated version invalidates aggressively when the ETA would move earlier and lazily when
later. **Same asymmetry reasoning as his already-`Understood` fire-then-log choice** in the
message-broker session.

**Correction 2 — invalidation triggers with no threshold will thrash.**
AIS SOG jitters ±0.1–0.2 kt constantly. Invalidating on *any* change invalidates on every
report; hit rate → 0, and you pay full compute **plus** cache bookkeeping — strictly worse than
no cache. Worse: **COG is computed from displacement between GPS fixes, so at near-zero speed
it is derived from pure noise and swings wildly.** His rule would thrash hardest on the moored
towboat — the exact case he intended it to help, inverting his claimed "2 for 1."

**Fix given:** thresholds (speed Δ > ~1 kt, course Δ > ~10°) and **gate course on speed**,
ignoring course below ~0.5 kt. **Cache hit rate** introduced as the metric that tells you
whether the cache earns its complexity.

---

## Final schema Connor built

**`vessels`** — `vessel_id` (PK) · `mmsi` (unique) · `name` · `home_port`
**`position_reports`** — `report_id` (PK) · `vessel_id` (FK) · `latitude` · `longitude` ·
`speed` · `heading` · `timestamp`

A normalized two-table relational schema, substantially self-derived, starting from "one file
per vessel" and "I'm still very inexperienced with coding."

---

## Recurring-mistake candidates from this session

- **Builds a mechanism without first checking the problem exists.** Twice: the recycling ID
  counter (unnecessary for ~1,584 years), and speed/course invalidation triggers (fire so often
  they defeat the cache). Prompt: *before designing the guard, compute whether the thing it
  guards against can happen, and how often the trigger fires.*
- **Reintroduces a failure he already diagnosed, in a new costume.** He identified name-reuse as
  fatal, then proposed deliberate ID reuse four messages later.
- **Picks a primary key by "most characteristic column" rather than by uniqueness guarantee.**
  Test: *can two rows ever share this value? A moored vessel reports identical lat/long 3,240
  times.*
- **Conflates `heading` (reported, degrees) with direction of travel (derived from consecutive
  positions).** Corrected twice in one session — flag on sight.
- **States a real tradeoff and stops there instead of resolving it.** With derived data, ask
  "can the source change?" — if not, there is no tradeoff to weigh.

## Trap to mark cleared

- **Rate arithmetic** — used 8,640 intervals/day correctly and unprompted, the exact
  calculation he was 6× low on in the 2026-07-31 session.

## Open threads

- **SQL** — he designed a database and never wrote a query. Should be short: he already knows
  what range scan / filter / group / join *do*, only the syntax is missing. Also Tech+
  Databases material.
- **Surrogate keys under-applied** — he restated the concept correctly but reverted to MMSI-as-PK
  when building. Worth one more pass.
- **Implication graph, De Morgan** — still outstanding from the SAT session, untouched here.
