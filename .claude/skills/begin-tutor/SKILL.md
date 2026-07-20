---
name: begin-tutor
description: Start a tutoring session from Connor's learning primer. Pulls the latest primer from GitHub, loads primer docs/learning-context.md into the conversation as context, then asks "What are we learning today?" to begin. Invoke when the user wants to start learning, begin a tutoring session, or kick off a study session.
---

# begin-tutor

Open a tutoring session grounded in the living learning primer. You sync the primer from
GitHub, read it into context so you know where Connor is on every track and skill, then
hand the floor back with a single opening question. This skill is **read-only** with
respect to the primer — it never edits, commits, or pushes the doc. (Recording progress
afterward is the separate [update-primer](../update-primer/SKILL.md) skill's job.)

## The primer file

Target: `primer docs/learning-context.md`, relative to the project root (the folder that
contains the `.claude/` directory this skill lives in). This keeps the skill portable
across machines/OSes regardless of absolute path. If it isn't at that relative path,
search the project for `learning-context.md` before doing anything else.

## Git sync (pull only)

The primer lives in a git repo whose remote is the user's GitHub
(`origin/main` → `github.com/Cwahle/learning-primer`). Pull the latest before loading it so
the session starts from the current version across machines. Run all git commands from the
project root (the folder containing the `.claude/` this skill lives in), not from any
nested repo.

1. Confirm you're in the right repo and on `main` with an `origin/main` upstream
   (`git rev-parse --abbrev-ref @{u}` → `origin/main`). If not, note it and continue with
   the local copy — don't guess a remote or branch.
2. Pull fast-forward only: `git pull --ff-only`.
   - Success (fast-forward or already up to date): proceed.
   - Fails because the branch **diverged**, or for network/auth reasons: **don't block the
     session.** Note the failure briefly, then continue with the local copy of the primer
     so learning can still start. (This skill makes no commits, so a stale local copy is
     the only risk, and it's recoverable.)
3. Never merge, rebase, force, commit, or push here. Pull is the only git write.

## Procedure

1. **Sync (pull):** follow the Git sync steps above. One line of acknowledgement is enough
   ("Pulled latest primer" / "Couldn't reach origin — using local copy").
2. **Load the primer:** read `primer docs/learning-context.md` in full so its Tracks,
   Skills, goals, teaching preferences, and recurring-mistake notes are all in context for
   the rest of the session. Actually read the file — don't rely on memory from a prior run.
3. **Orient (brief):** give Connor a short, skimmable snapshot so he knows you're loaded
   and where things stand. Keep it tight — a few lines, not a wall of text:
   - Anything marked **Recently mastered**.
   - 2–4 skills currently in `Learning`/`Applying` (the natural things to push on next).
   - Any recurring-mistake / trap notes worth keeping front of mind this session.
   Honor the teaching preferences in Section 1 of the primer when framing all of this.
4. **Hand off:** end your opening message with exactly the question:

   > What are we learning today?

   Then stop and wait for Connor's answer — don't pick a topic for him or start teaching
   until he responds.

## Notes

- If the primer can't be found at all, say so plainly and ask Connor where it lives rather
  than inventing content.
- This skill only starts the session. When the session is done and Connor wants to record
  progress, that's `update-primer` (which handles its own pull, write, commit, and push).
