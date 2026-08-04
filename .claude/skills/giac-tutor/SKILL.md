---
name: giac-tutor
description: Start a GSE-track tutoring session. Pulls the latest docs from GitHub, loads BOTH primer docs/learning-context.md (mastery record) and primer docs/gse-roadmap.md (the GIAC/GSE concept map), reports position on the roadmap plus the next unticked atoms, then asks "What are we learning today?". Invoke for GIAC, GSE, GSEC, GCIA, GCIH, GPEN, GREM, or GICSP study sessions, or any session that should be anchored to the certification roadmap.
---

# giac-tutor

Open a tutoring session grounded in **both** learning documents: the primer (what Connor has
demonstrated) and the GSE roadmap (what's left, and in what order). Sync both from GitHub,
read them into context, report where he actually stands on the map, then hand the floor back
with a single opening question.

This skill is **read-only** with respect to both files — it never edits, commits, or pushes.
Recording progress afterward is [giac-update](../giac-update/SKILL.md)'s job.

**Relationship to `begin-tutor`:** that skill is the general-purpose opener and deliberately
knows nothing about the roadmap. Use it for open-ended sessions. Use this one when the session
should be anchored to the certification path. Never invoke both in the same session.

## The files

Both paths are relative to the project root (the folder containing the `.claude/` directory
this skill lives in), which keeps the skill portable across machines and OSes.

| Role | Path | What it is |
|---|---|---|
| **Primer** | `primer docs/learning-context.md` | The *record*. Teaching preferences, Tracks, Skills with evidence, known traps. |
| **Roadmap** | `primer docs/gse-roadmap.md` | The *map*. GSE requirements, chosen portfolio, phases, and ~700 atoms as `- [ ]` / `- [x]` checkboxes. |

If either isn't at its stated path, search the project for `learning-context.md` /
`gse-roadmap.md` before doing anything else. If the **roadmap** is genuinely missing, say so
and continue primer-only — don't invent a map. If the **primer** is missing, say so plainly
and ask where it lives.

**Keep the distinction straight all session:** a ticked atom means *covered*. A Skills-table
stage means *demonstrated*. They are not the same claim, and the roadmap never overrides the
primer on what Connor can actually do.

## Git sync (pull only)

The docs live in a git repo whose remote is the user's GitHub
(`origin/main` → `github.com/Cwahle/learning-primer`). Pull the latest before loading so the
session starts from the current version across machines. Run all git commands from the project
root, not from any nested repo.

1. Confirm you're in the right repo and on `main` with an `origin/main` upstream
   (`git rev-parse --abbrev-ref @{u}` → `origin/main`). If not, note it and continue with the
   local copies — don't guess a remote or branch.
2. Pull fast-forward only: `git pull --ff-only`.
   - Success (fast-forward or already up to date): proceed.
   - Fails because the branch **diverged**, or for network/auth reasons: **don't block the
     session.** Note the failure briefly, then continue with the local copies so learning can
     still start. (This skill makes no commits, so a stale local copy is the only risk, and
     it's recoverable.)
3. Never merge, rebase, force, commit, or push here. Pull is the only git write.

## Finding the current position

The roadmap carries no cursor or state field — position is **derived**, so it can never go
stale:

1. Scan the roadmap top to bottom for `- [ ]` lines.
2. The **current front** is the earliest section that still has unticked atoms. Sections run in
   intended study order: Phase 0 foundations (§3.1–3.5) → Practitioner tracks (§4.1–4.6) →
   Applied Knowledge (§5.1–5.5).
3. **The front is a default, not a rule.** If Connor says he's working elsewhere, follow him —
   don't argue him back to the front. Note the jump and move on.

Count remaining atoms in the current section for the snapshot. Don't count the whole file — a
"703 remaining" figure is discouraging and tells him nothing actionable.

## Procedure

1. **Sync (pull):** follow the Git sync steps. One line of acknowledgement is enough
   ("Pulled latest" / "Couldn't reach origin — using local copies").
2. **Load both files in full.** Read the primer for teaching preferences, Tracks, Skills, and
   traps. Read the roadmap for phase structure and checkbox state. Actually read them — don't
   rely on memory from a prior run, and don't skim to the checkboxes. Section 1 of the primer
   is standing instruction for the whole session.
3. **Orient (brief).** A short, skimmable snapshot — a few lines, not a wall of text:
   - **Position:** current section, and how many atoms remain *in that section*.
   - **Next up:** 3–5 unticked atoms from the current front — the concrete candidates for today.
   - **Mastery:** anything under **Recently mastered**, plus 2–3 skills in `Learning`/`Applying`
     that the current section builds on. Prefer skills relevant to where he is on the map over
     an arbitrary sample of the table.
   - **Traps:** the recurring-mistake notes that plausibly fire on today's material. Not the
     whole list — the ones that are actually live for this topic.
   Honor the teaching preferences in Section 1 when framing all of this.
4. **Hand off:** end your opening message with exactly the question:

   > What are we learning today?

   Then stop and wait for Connor's answer. Offering the next atoms in step 3 is the point of
   this skill — but they're a menu, not a decision. Don't pick for him and don't start teaching
   until he responds.

## Notes

- **Never edit either file here**, including ticking a checkbox mid-session. It is tempting and
  it is wrong: the write path is `giac-update`, which works from a saved transcript and keeps
  git history coherent. A tick made here is a tick made twice.
- The roadmap's requirements section is dated and GIAC restructures. If a session turns up
  evidence that requirements changed, flag it rather than teaching around it.
- Session close-out is unchanged (primer Section 7): remind Connor to save the transcript, then
  run `/giac-update` on it — that single command now covers both documents.
