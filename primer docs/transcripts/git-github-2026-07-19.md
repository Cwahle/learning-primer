# Git / GitHub

**Date:** 2026-07-19
**Participant:** Connor
**Context:** Learning session structured by `learning-context.md`. Teaching mode: scaffold-don't-solve, make-me-retrieve, first-principles, direct correction, connect back to tracked skills. Hands-on portion performed real operations on the `learning-primer` repo (branch `primer-doc-cleanup` → `main`), including a real remote divergence and merge-conflict resolution.

**Concepts covered:** what a commit *is* (hashed snapshot + parent pointer) · staging area (index) vs. commit · the three zones (working dir → staging → commit) · content-addressing / hash chain (connected to crypto hashing from OpenSSL work) · immutability & tamper-evidence of history · branch = movable pointer (one 40-byte file) · HEAD · fast-forward merge · two-parent merge commit · divergence · Git vs. GitHub (local system vs. remote host) · `origin` and remote-tracking refs (`origin/main`) · `push` / `fetch` / `pull` (= fetch + merge) · merge vs. rebase (and why rebase rewrites hashes) · merge conflicts, conflict markers, resolution (`add` to signal resolved) · the distributed "pull-before-you-work" habit.

---

## Connor

> i would like to learn more about GIT and github functionality today, use the attached doc to structure this chat

## Claude

Opened with two diagnostic questions to calibrate depth: (1) what *is* a commit (the thing, not the command); (2) the difference between Git and GitHub.

## Connor

> 1. the idea i have in my head for a git commit is using it like a staging ground before merging it to the main repo... 2. git is the command... whereas github is the website/company that wraps git as a command giving it more function

## Claude

**Correction #2:** Git is a complete, self-contained *distributed* version-control system that runs entirely locally with no GitHub needed. GitHub is a *host* (a remote home for a repo) plus a collaboration layer (PRs, issues, CI). It doesn't extend the `git` command. Git = the system (local, works alone); GitHub = one possible remote + social features. Hold onto "distributed."

**Correction #1 (the important one):** A commit is **not** a staging ground. Dissected the raw commit object (`git cat-file -p HEAD`) of `2693aee` live — showed `tree` (snapshot), `parent` (pointer back), author/committer, message. Established: a commit is a **permanent, immutable, hashed snapshot** of all tracked files + a pointer to its parent — a link in an append-only chain, not a transient pre-merge holding area. The word "staging" is a *separate* real concept: the staging area / index. Introduced the three zones: **working directory → staging area (`git add`) → commit (`git commit`)**.

**Retrieval Q1:** which command stages, which commits? **Q2:** after a second commit, does the first change/disappear/stay?

## Connor

> 1. add is the command to move to staging area, commit turns it into a snapshot
> 2. ... if you commit a second time even if you only changed 2 characters it will still make a new snapshot ... its a snapshot that is hashed which means if a single character is changed then the hash will be different between the 2 versions.

## Claude

Both correct. Sharpened the implicit point: the first commit stays **exactly as it was, untouched** — Git never edits a commit in place, it appends a new one. Because a commit's hash is computed from its full content *including the parent's hash*, altering any old commit would break every descendant's `parent` pointer → history is **tamper-evident by construction**.

**Connect-back:** this is the same cryptographic hashing Connor met in the OpenSSL/SSL-TLS work — Git is **content-addressed** (an object's ID *is* the hash of its content), and the "each block references the previous block's hash" structure is literally a hash chain (same mechanism as a blockchain). Connor recognized the primitive from the security context.

## Claude

**Branches.** Retrieval: what is a branch, mechanically? Connor guessed "a snapshot of whatever file you opened that you then modify."

**Correction:** a branch is **not** a snapshot or a copy of files. Showed on disk: `.git/refs/heads/primer-doc-cleanup` contains exactly one 40-char hash; `HEAD` contains `ref: refs/heads/primer-doc-cleanup`. A branch is a **movable pointer to a single commit** (a ~40-byte file); HEAD points at the branch you're on. When you commit, Git writes the new commit and slides the current branch's pointer forward. This is why branching is instant/cheap — you're writing 40 bytes, not copying the project.

**Retrieval:** Q1 — after a commit, which pointer moves and how does Git know? Q2 — what could "merging into main" mean just in terms of moving a pointer?

## Connor

> Q1. ... the pointer from primer doc cleanup would move and git knows that because of the head pointer.
> Q2. the pointer would just be appended on to the snapshot currently in main ... giving the whole chain of versions hence "version control"

## Claude

Q1 correct and precise. Q2 — right instinct; introduced the term: because `main` was a direct **ancestor** of the branch tip (same straight line), merging is a **fast-forward** — Git just slides `main`'s pointer forward, no new commit, no conflicts. Then set up the contrast: if `main` had gained its own commits meanwhile, the branches would **diverge** and a pointer-slide is impossible.

**Retrieval:** in a diverged picture, what must Git *create* to reunite the two lines? (Hint: the `parent` line — how many parents?)

## Connor

> ... it either takes one and then the next based on timestamps or merges the 2 commits and then moves the pointer forward

## Claude

Killed the wrong half: Git does **not** pick a winner by timestamp or discard either side — nothing is lost in a merge. Crowned the right half: Git creates a **merge commit** whose defining feature is **two parents** (vs. the one-parent object dissected earlier). The merge commit's tree is the combined result; its two parents let history trace back through both lines. Noted that when both sides edit the *same lines*, Git can't compute the combined tree and stops for a human to resolve the conflict — the fast-forward case never has this problem.

Summarized the arc: Connor's original one-sentence model ("commit = staging ground before merging to main") was fusing four distinct pieces of machinery — staging area, commit, branch, merge — all built from three primitives (snapshots + pointers + hashes), one of which (hashing) he already knew.

---

## Hands-on portion (real operations on `learning-primer`)

Connor chose to actually merge `primer-doc-cleanup` into `main`.

- Confirmed state: `main` @ `61b11de`, `primer-doc-cleanup` @ `2693aee`, `main` an ancestor → fast-forward possible.
- Connor predicted the commands (`git checkout main` then `git merge primer-doc-cleanup`) using tab-completion.
- Ran it: Git reported `Fast-forward`, `Updating 61b11de..2693aee`. Both pointers landed on `2693aee`, single straight-line graph. Connor watched `main` slide forward.

**Remote layer.** Git's output ("up to date with `origin/main`") surfaced the remote. Explained `origin` = nickname for the GitHub URL; `origin/main` = local cache of GitHub's `main`; local `main` was 2 commits ahead. Connor asked to push himself.

- First `git push origin main` failed: `Could not resolve host: github.com`. Diagnosed as **DNS failure** (below Git entirely — no IP to connect to; connected to netcat/TCP layer). Connor noted he was on a phone hotspot. Two pings (`8.8.8.8` and `github.com`) both succeeded on retry (DNS resolved `github.com` → `140.82.113.3`) — the earlier failure was a transient hotspot drop. Noted the high ping jitter as cellular variance.
- Retry `git push origin main` was **rejected** (`! [rejected] ... (fetch first)`): the remote had work the local repo lacked. **This was the divergence scenario, live.**
- `git fetch origin` revealed the fork: `origin/main` had advanced to `7bbcc7a` ("Update primer from message-brokers session"), which the laptop had never seen. Graph showed both lines forking from shared ancestor `61b11de`. (Noted and corrected a glitched intermediate line in my own tooling output that wrongly said "no common ancestor.")

**Root cause — diagnosed by Connor unaided:**

> i ran the updater skill on my desktop and then didnt get the latest repo changes on my laptop which put the 2 versions out of sync.

Exactly right — the distributed hazard. Reinforced the fix: **`git pull` before you start working** on a shared repo, not just before pushing.

**Reconciling the fork.** `git pull` refused (divergent branches, no strategy configured). Explained **merge vs. rebase**: merge = two-parent commit preserving true history; rebase = replays your commits onto the remote tip, giving a linear history but **rewriting commit hashes** (new parent → new hash → literally new commits; connected to the earlier hashing lesson). Chose merge (`git pull --no-rebase --no-edit origin main`).

**Merge conflict** in `primer docs/learning-context.md` (four regions). Taught the conflict-marker anatomy (`<<<<<<< HEAD` / `=======` / `>>>>>>> 7bbcc7a`). Key lesson: **a conflict does not mean "pick a winner"** — here both sides recorded different real learning sessions (SAT-solver vs. message-brokers), so the correct resolution was **keep both**, combined. Connor caught the fix himself and requested message-broker context ordered before SAT-solver context. Resolved all four regions (3 = keep-both, 1 = kept the more-recent "recently mastered" line). Verified **no leftover markers** before committing (flagged this as the classic beginner bug).

- Noted `UU` status: editing the file fixes content, but the file stays *unmerged* until `git add` signals resolution — `git add` (the staging step from lesson 1) doing double duty.
- Connor authorized the finish/push. Ran `git add` → `git commit --no-edit` → `git push origin main`. Merge commit `a35ea8f` created with **two parents** (`2693aee`, `7bbcc7a`); push succeeded (`7bbcc7a..a35ea8f main -> main`). Final graph = the fork-and-join "diamond"; local and `origin/main` in sync.

---

## Evidence summary

- **Commit-as-snapshot:** understood and taught back the hash/immutability reasoning unaided (Q2). Connected single-char-change → different hash independently.
- **Branch-as-pointer:** initial snapshot/copy misconception, corrected via on-disk demonstration.
- **Fast-forward:** predicted the merge commands; watched and read the pointer slide.
- **Merge commit / two parents:** derived the "must create something new" idea; corrected the timestamp/discard misconception.
- **Divergence root cause:** diagnosed the desktop→laptop out-of-sync cause unaided.
- **Conflict resolution:** grasped "keep both" and drove the ordering decision; executed the full resolve → add → commit → push cycle.
- **Distributed model:** independently articulated why the two repos diverged; learned the pull-before-work habit.

**Recurring-mistake candidates observed:** (1) conflating "staging" with "committing"; (2) modeling a branch as a copy/snapshot of files rather than a pointer. Both corrected within the session.
