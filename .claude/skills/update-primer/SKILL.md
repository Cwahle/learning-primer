---
name: update-primer
description: Update Connor's learning primer (primer docs/learning-context.md) from a saved conversation transcript. Reads a transcript file, updates the auto-managed Tracks and Skills sections with evidence-based mastery progression, adds newly-seen skills, records recurring mistakes, and reports what changed. Invoke when the user wants to refresh/update the primer or learning context after a conversation.
---

# update-primer

Update the living learning primer from a saved conversation transcript. You read a
transcript the user exported from another chat (Claude, ChatGPT, Gemini, etc.),
compare it against the current primer, and rewrite only the auto-managed sections.

## Inputs

- **Argument (required):** a path to the transcript file to ingest
  (e.g. `primer docs/transcripts/2026-07-15-bandit.md`).
  - If no path is given, list the files in `primer docs/transcripts/` and ask the
    user which one to use. Do not guess.
- **Flags (optional):**
  - `--dry-run` — show the change report and proposed edits but write nothing.
  - `--apply` — skip the confirmation step and write immediately (still make a backup).
  - Default (no flag) — **preview-then-confirm**: show the report and proposed edits,
    then ask the user to confirm before writing.

## The primer file

Target: `primer docs/learning-context.md`, relative to the project root (the folder
that contains the `.claude/` directory this skill lives in). This keeps the skill
portable across machines/OSes regardless of the absolute path. If it isn't at that
relative path, search the project for `learning-context.md` before doing anything
else. Read it fully before making any edits.

## What you may edit — and what you must never touch

Only rewrite content **between** these marker pairs:

- `<!-- tracks:auto:start -->` … `<!-- tracks:auto:end -->` (Section 2, Tracks table)
- `<!-- skills:auto:start -->` … `<!-- skills:auto:end -->` (Section 5, Skills table)
- `<!-- traps:auto:start -->` … `<!-- traps:auto:end -->` (Section 6, recurring mistakes)
- The **Recently mastered:** line at the top of Section 5.

Everything else is hand-owned — Section 1 (teaching prefs), Section 3 (goals),
Section 4 (anchor context), the stage legend, the typo list in Section 6, and all
prose. Never edit outside the markers. Never move, rename, or delete the markers
themselves. Preserve the existing table columns and formatting exactly.

## Mastery model

Stage ladder: `New` → `Learning` → `Applying` → `Understood`.

- `New` — added, not yet meaningfully engaged.
- `Learning` — actively working through it; asking questions, making mistakes.
- `Applying` — using it correctly, still with occasional prompting/correction.
- `Understood` — can use **and** explain it unaided; self-corrects.

### Rules for changing a stage

1. **Evidence-based by default.** Only promote a skill when the transcript shows
   concrete evidence for the next stage:
   - → `Learning`: the user engaged with the topic (asked about it, attempted it).
   - → `Applying`: the user applied it correctly, possibly with some prompting.
   - → `Understood`: the user used it unaided **and** explained it / caught their own
     error / taught it back. Explaining *how it works*, not just getting a right answer.
   Tag every `Understood` entry with `(evidence)`.
2. **Explicit self-report overrides.** If the user plainly states a mastery level in
   the transcript ("I've got X down now", "I finally understand Y"), honor it — set
   that stage and, if it lands on Understood, tag it `(self-reported)` instead of
   `(evidence)`. This is the only case where you set a stage without demonstrated
   evidence.
3. **Never silently demote.** If the transcript suggests a regression (the user now
   struggles with something previously higher), do **not** lower the stage on your
   own — flag it in the change report and let the user decide.
4. **Only promote one stage at a time** unless a self-report jumps further.

### Adding new skills

- A topic the user genuinely engaged with that isn't already listed → add a new row
  with the right stage, a one-line evidence note, and today's date.
- **Check for duplicates first.** Match against existing rows (including close
  paraphrases) so you don't add a near-copy of something already tracked. If it's the
  same underlying skill, update the existing row instead of adding one.
- New broad *efforts/areas* belong in the **Tracks** table; atomic, testable
  *concepts* belong in the **Skills** table. When unsure, prefer Skills.

## Dates

Get the real current date at run time (`date +%F`). Stamp the **Last touched** column
with that date for any track or skill that appeared in this transcript. Leave the date
unchanged for rows the transcript didn't touch. Replace any `(seed)` marker with the
real date once a row is genuinely touched.

## Recently mastered

After processing, set the **Recently mastered:** line in Section 5 to the skills that
reached `Understood` in this run, keeping up to the last 3 (newest first), each with
its date. If nothing new was mastered, leave the existing line as-is.

## Recurring mistakes

If the transcript shows a mistake — especially a repeated one — that isn't already in
the traps auto region, append a short bullet describing the pattern so it can be
flagged in future sessions. Don't duplicate existing bullets. Never touch the
hand-owned typo list below the markers.

## Procedure

1. Resolve and read the primer file. Read the transcript file.
2. Work out the changes: track/skill promotions, new rows, date stamps, recently-
   mastered, recurring mistakes — following the rules above.
3. Build the **change report** (format below).
4. **Backup:** before writing, copy the current primer to
   `primer docs/backups/learning-context.<YYYY-MM-DD-HHMMSS>.bak.md`.
   (Skip the backup only under `--dry-run`.)
5. Apply per the flag:
   - `--dry-run`: print the report + the proposed new content of each changed region;
     write nothing.
   - default: print the report + proposed edits, then **ask the user to confirm**.
     On confirmation, make the backup and write. On decline, change nothing.
   - `--apply`: make the backup and write immediately, then print the report.
6. Only ever rewrite content between the markers; leave the rest byte-for-byte intact.

## Change report format

```
Primer update — <transcript filename> (<date>)

✎ Promoted   "<skill>"  <old stage> → <new stage>   (<why, from transcript>)
＋ Added      "<skill>"  (<stage>)   (<why>)
✓ Mastered   "<skill>"  → Understood (<evidence|self-reported>)
⚠ Possible regression  "<skill>"  (<what you saw>) — left unchanged, your call
⧗ Recurring mistake logged: "<pattern>"
· Unchanged  <n> tracked skills not touched this session
```

Only include lines that apply. Keep it short and skimmable — this report is the main
way the user learns what moved and whether they've reached understanding of something.
