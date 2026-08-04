---
name: giac-update
description: Update BOTH Connor's learning primer (primer docs/learning-context.md) and the GSE roadmap (primer docs/gse-roadmap.md) from a saved conversation transcript. Updates the primer's auto-managed Tracks/Skills/traps sections with evidence-based mastery progression, ticks the roadmap atoms the session actually covered, then commits and pushes both. Invoke after a GIAC/GSE study session to record progress against the certification roadmap.
---

# giac-update

Update the two living learning documents from a saved conversation transcript. You read a
transcript the user exported from another chat (Claude, ChatGPT, Gemini, etc.), compare it
against both files, and write to each under its own rules.

**Relationship to `update-primer`:** that skill writes the primer only and knows nothing about
the roadmap. This one does everything it does, plus the roadmap. Run **one or the other**, never
both on the same transcript — the second run would find nothing to promote and could mislead you
into double-reporting.

## The two documents, and the different claims they make

| File | What a write means | Bar |
|---|---|---|
| `primer docs/learning-context.md` | **Demonstrated.** Connor did this, and here's the evidence. | High — see Mastery model |
| `primer docs/gse-roadmap.md` | **Covered.** This was taught and engaged with. | Lower — see Ticking atoms |

Keeping these separate is the whole design. A ticked atom is not a mastery claim, and a
`Learning` stage does not mean the atom is unticked. **Never** let one file's state drive the
other's — decide each independently from the transcript.

Both paths are relative to the project root (the folder containing the `.claude/` directory this
skill lives in). If either isn't there, search the project for `learning-context.md` /
`gse-roadmap.md` before doing anything else. Read both fully before making any edits. If the
roadmap is genuinely missing, report it and continue primer-only rather than creating one.

## Inputs

- **Argument (optional):** a path to the transcript file to ingest
  (e.g. `primer docs/transcripts/2026-08-10-networking-i.md`). Pass one only to **override**
  the automatic pick.
  - **If no path is given — the normal case — auto-select the transcript by recency**
    (see "Selecting the transcript"). Do **not** list files and ask which to use; the whole
    point is that the skill runs unattended.
- **Flags (optional):**
  - `--dry-run` — show the change report and proposed edits but write nothing. Do **not** pull,
    write, commit, or push; instead `git fetch` and note whether the branch is behind `origin`.
  - `--apply` — skip the confirmation step and write immediately (still make backups). Pull
    first, then write, then commit and push.
  - `--no-push` — do everything (pull, write, commit) but skip the final push, leaving the
    commit local. Combine with any mode.
  - `--primer-only` — update the primer and leave the roadmap untouched. For sessions that
    weren't GIAC-track work but still produced mastery evidence.
  - `--roadmap-only` — tick atoms but don't touch the primer. Rare; mainly for correcting a
    missed tick after the fact.
  - Default (no flag) — **preview-then-confirm**: pull first, show the report and proposed
    edits, then ask the user to confirm before writing. On confirmation, write, commit, push.

## Selecting the transcript

When no path argument is given (the default), pick the transcript automatically — no prompting:

1. List the regular files in `primer docs/transcripts/`.
2. Exclude non-transcripts: `README.md`, dotfiles, and anything that isn't a `.md` transcript.
3. Choose the file with the **most recent modification time (mtime)**. This is intentionally
   naming-agnostic: it doesn't matter whether the date sits at the front
   (`2026-08-10-topic.md`) or back (`topic-2026-08-10.md`) of the name, or is absent entirely.
4. Tie-break (rare): prefer the newer `YYYY-MM-DD` embedded in the filename, then fall back to
   alphabetical order. Any deterministic choice is acceptable.
5. If the directory has no eligible transcript, **stop and report** — never invent one.

Resolve it in one command from the project root, e.g.:

```
ls -t "primer docs/transcripts/"*.md | grep -v '/README\.md$' | head -1
```

`ls -t` orders by mtime (newest first), so `head -1` is the pick. Always **name the file you
selected** at the top of the change report. Auto-selection only chooses the file — it does
**not** bypass the preview-then-confirm gate before writing.

## Git sync (pull before, push after)

The docs live in a git repo whose remote is the user's GitHub
(`origin/main` → `github.com/Cwahle/learning-primer`). To keep versions consistent across
machines and avoid merge conflicts, **always pull before touching either file and push after a
successful write.** Run all git commands from the project root, not from any nested repo.

### Before any work — pull

1. Confirm you're in the right repo and on `main` with an `origin/main` upstream
   (`git rev-parse --abbrev-ref @{u}` → `origin/main`). If not, stop and report; don't guess a
   remote or branch.
2. Check for uncommitted changes to **both** targets:
   `git status --porcelain -- "primer docs/learning-context.md" "primer docs/gse-roadmap.md"`.
   If either is already dirty, **stop and report** — don't pull over unsaved edits. (Ignored
   backups and the untracked `learning-primer/` folder don't count and won't block.)
3. Pull fast-forward only: `git pull --ff-only`.
   - Success (fast-forward or already up to date): proceed.
   - Fails because the branch **diverged** (local commits + remote commits): stop and report.
     Do not auto-merge or rebase; let the user reconcile.
   - Fails for network/auth reasons: stop and report the error. Don't proceed on possibly-stale
     files.
4. Under `--dry-run`, replace the pull with `git fetch` and just note whether `main` is behind
   `origin/main` — never mutate the working tree in dry-run.

### After a successful write — commit and push

1. Stage **only** the two documents, named explicitly:
   `git add "primer docs/learning-context.md" "primer docs/gse-roadmap.md"`
   Never `git add -A` (backups are gitignored; the stray `learning-primer/` folder must not be
   committed). Under `--primer-only` / `--roadmap-only`, stage just the one file you wrote.
2. Commit with a message matching the existing history style:
   `Update primer + GSE roadmap from <transcript-topic> session (<YYYY-MM-DD>)`
   (drop the half you didn't write under `--primer-only` / `--roadmap-only`). Append the
   standard Claude Code co-author trailer.
3. Push: `git push`. If push is **rejected** because the remote moved ahead since the pull,
   report it and suggest re-running (which will pull the new commits first) — don't force-push.
4. Skip commit and push entirely under `--dry-run` or `--no-push`. Under `--no-push`, still
   commit locally and tell the user the commit is unpushed.

If any git step fails, report exactly what failed and stop — never silently swallow a pull/push
error, since that's what version control is here to prevent.

---

## Part A — the primer (`learning-context.md`)

### What you may edit — and what you must never touch

Only rewrite content **between** these marker pairs:

- `<!-- tracks:auto:start -->` … `<!-- tracks:auto:end -->` (Section 2, Tracks table)
- `<!-- skills:auto:start -->` … `<!-- skills:auto:end -->` (Section 5, Skills table)
- `<!-- traps:auto:start -->` … `<!-- traps:auto:end -->` (Section 6, recurring mistakes)
- The **Recently mastered:** line at the top of Section 5.

Everything else is hand-owned — Section 1 (teaching prefs), Section 3 (goals), Section 4
(anchor context), the stage legend, the typo list in Section 6, and all prose. Never edit
outside the markers. Never move, rename, or delete the markers themselves. Preserve the existing
table columns and formatting exactly.

**Carry-forward rule:** the GSE track row inside `tracks:auto` holds durable facts that did not
come from any transcript — the corrected portfolio requirements (6 Practitioner + 4 Applied
Knowledge), the GSP waypoint, and the pointer to `gse-roadmap.md`. When you regenerate that
region, **preserve those facts**. Only append session evidence and restamp the date. Losing the
pointer to the roadmap is the specific failure this rule exists to prevent.

### Mastery model

Stage ladder: `New` → `Learning` → `Applying` → `Understood`.

- `New` — added, not yet meaningfully engaged.
- `Learning` — actively working through it; asking questions, making mistakes.
- `Applying` — using it correctly, still with occasional prompting/correction.
- `Understood` — can use **and** explain it unaided; self-corrects.

#### Rules for changing a stage

1. **Evidence-based by default.** Only promote when the transcript shows concrete evidence:
   - → `Learning`: the user engaged with the topic (asked about it, attempted it).
   - → `Applying`: the user applied it correctly, possibly with some prompting.
   - → `Understood`: the user used it unaided **and** explained it / caught their own error /
     taught it back. Explaining *how it works*, not just getting a right answer.
   Tag every `Understood` entry with `(evidence)`.
2. **Explicit self-report overrides.** If the user plainly states a mastery level ("I've got X
   down now"), honor it — set that stage and, if it lands on Understood, tag it
   `(self-reported)` instead of `(evidence)`. This is the only case where you set a stage
   without demonstrated evidence.
3. **Never silently demote.** If the transcript suggests regression, do **not** lower the stage
   on your own — flag it in the change report and let the user decide.
4. **Only promote one stage at a time** unless a self-report jumps further.

### Adding new skills

- A topic genuinely engaged with that isn't already listed → add a row with the right stage, a
  one-line evidence note, and today's date.
- **Check for duplicates first.** Match against existing rows including close paraphrases. If
  it's the same underlying skill, update the existing row instead of adding one.
- New broad *efforts/areas* belong in the **Tracks** table; atomic, testable *concepts* belong
  in the **Skills** table. When unsure, prefer Skills.
- **Do not bulk-import roadmap atoms into the Skills table.** The roadmap holds ~700 atoms; the
  Skills table holds only what Connor has actually engaged with, each with evidence. Importing
  the map would destroy the table's meaning. Add a skill row only when the transcript supports it.

### Recently mastered

Set the **Recently mastered:** line in Section 5 to the skills that reached `Understood` in this
run, keeping up to the last 3 (newest first), each with its date. If nothing new was mastered,
leave the line as-is.

### Recurring mistakes

If the transcript shows a mistake — especially a repeated one — that isn't already in the traps
auto region, append a short bullet describing the pattern. Don't duplicate existing bullets.
Never touch the hand-owned typo list below the markers.

---

## Part B — the roadmap (`gse-roadmap.md`)

The roadmap has **no auto markers**. Its protection is a tight edit contract instead.

### The only edits you may make

1. **Tick an atom:** change `- [ ]` to `- [x]` on an existing line. The atom's text stays
   byte-for-byte identical.
2. **Append an atom:** add a new `- [ ]` line under an existing objective heading, when the
   session covered something genuinely in scope that the map omits.

**That is the complete list.** Never edit prose, headings, tables, the requirements section,
the portfolio table, the timeline, the sequencing rationale, or the sources. Never reorder,
reword, merge, split, or delete atoms. Never add a section. If the map looks wrong, **say so in
the report** and let Connor decide — the roadmap is hand-owned in every respect except these two
operations.

### Ticking atoms

The bar is **covered**, not mastered: the concept was taught in the session and Connor engaged
with it — asked about it, worked it, or answered on it. He does not need to have gotten it right.
A concept he struggled with is still covered; that struggle belongs in the primer as a
`Learning` stage, and the two records disagreeing is correct, not a bug.

- **Only tick what the transcript actually shows.** Adjacency is not coverage. Teaching MAC
  address tables does not tick "unknown destination MAC flooding" unless flooding was covered.
  When in doubt, leave it unticked and note it — an unticked covered atom costs one revisit; a
  ticked uncovered atom becomes a silent gap that surfaces on exam day.
- **A passing mention is not coverage.** Naming a term while explaining something else doesn't
  tick it.
- **Ticks span sections.** A session can legitimately tick atoms in several places — a
  networking session may reach into GCIA §4.2. Follow the transcript, not the section boundary.
- **Never untick.** If a session shows Connor lost something previously ticked, flag it in the
  report as a suggested revisit and leave the box ticked. Unticking silently rewrites history;
  the primer's stage is the right place for a regression signal.

### Appending atoms

Allowed, deliberately conservative:

- Only under an **existing** objective heading, in the section where it belongs.
- Only for material the session actually covered that the map genuinely lacks.
- Match the surrounding style: one line, terse, imperative or noun-phrase.
- Tick it in the same pass if it was covered.
- **Report every appended atom explicitly.** This is the one operation that changes the map's
  shape, so it never happens quietly.

Do not append to pad coverage, and do not append a rewording of an atom that already exists.

---

## Dates

Get the real current date at run time (`date +%F`). Stamp the **Last touched** column with that
date for any track or skill that appeared in this transcript. Leave the date unchanged for rows
the transcript didn't touch. Replace any `(seed)` marker with the real date once a row is
genuinely touched. The roadmap has no date columns — don't add any.

## Procedure

1. **Sync (pull):** follow "Before any work — pull". Stop and report if either file is dirty, if
   the repo diverged, or if the pull fails. (Dry-run: `git fetch` and note behind/ahead instead.)
2. **Select the transcript:** use the path argument if given; otherwise auto-pick the most
   recently modified transcript. Read the transcript, the primer, and the roadmap in full.
3. **Work out primer changes** per Part A: promotions, new rows, date stamps, recently-mastered,
   recurring mistakes.
4. **Work out roadmap changes** per Part B: atoms to tick, atoms to append. Decide these
   independently from step 3 — do not derive ticks from promotions or vice versa.
5. Build the **change report** (format below).
6. **Backups:** before writing, copy each file you're about to change:
   - `primer docs/backups/learning-context.<YYYY-MM-DD-HHMMSS>.bak.md`
   - `primer docs/backups/gse-roadmap.<YYYY-MM-DD-HHMMSS>.bak.md`

   Use the **same timestamp** for both so a pair can be restored together. Skip backups only
   under `--dry-run`.
7. Apply per the flag:
   - `--dry-run`: print the report + proposed content of each changed region and each atom
     flip; write nothing, commit nothing, push nothing.
   - default: print the report + proposed edits, then **ask the user to confirm**. On
     confirmation, back up and write. On decline, change nothing.
   - `--apply`: back up and write immediately, then print the report.
8. In the primer, only rewrite content between the markers. In the roadmap, only flip checkboxes
   and append atoms. Leave every other byte of both files intact.
9. **Sync (push):** after a successful write, follow "After a successful write — commit and
   push", staging only the documents you wrote. Skip under `--dry-run` and `--no-push`. Report
   the push result.

## Change report format

```
GIAC update — <transcript filename> (<date>)

📄 Ingested   <transcript filename>  (<auto-selected: newest by mtime | path given>)
⇣ Pulled     origin/main (<up to date | fast-forwarded N commits>)

PRIMER
✎ Promoted   "<skill>"  <old stage> → <new stage>   (<why, from transcript>)
＋ Added      "<skill>"  (<stage>)   (<why>)
✓ Mastered   "<skill>"  → Understood (<evidence|self-reported>)
⚠ Possible regression  "<skill>"  (<what you saw>) — left unchanged, your call
⧗ Recurring mistake logged: "<pattern>"
· Unchanged  <n> tracked skills not touched this session

ROADMAP
☑ Ticked     §<section> — <n> atoms   (<list them, or the first few + count>)
＋ Appended   §<section> — "<new atom>"   (<why the map lacked it>)
◔ Section    §<section> now <ticked>/<total> — <n> atoms remain
→ Next front <the earliest section still holding unticked atoms>
⚠ Not ticked "<atom>" — <touched but not covered enough; flagged for next session>

⇡ Pushed     <commit sha> to origin/main   (or: commit left local under --no-push)
```

Only include lines that apply. Keep it short and skimmable — this report is the main way Connor
learns what moved, whether he's reached understanding of something, and how much of the current
section is left.

## Notes

- **Never invent coverage to make the report look productive.** A session that ticked three
  atoms and promoted one skill is a normal session. The roadmap holds ~700 atoms across a 5–8
  year path; inflated ticks compound into a map that claims ground Connor hasn't taken.
- If the transcript shows GIAC's published requirements have changed, **report it and stop short
  of editing** the requirements section — that's hand-owned prose and a structural change Connor
  should make deliberately.
- The paired opener is [giac-tutor](../giac-tutor/SKILL.md), which reads both files and never
  writes. `begin-tutor` / `update-primer` remain the roadmap-unaware pair; don't mix them.
