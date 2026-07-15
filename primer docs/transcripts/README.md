# Transcripts

Drop exported conversations here, then run `/update-primer <file>` on one.

**How to save a conversation:**
1. In your chat (Claude, ChatGPT, Gemini…), select the conversation text and copy it.
2. Save it into this folder as a `.md` or `.txt` file.
3. Name it so future-you can tell them apart, e.g. `2026-07-15-bandit-l16.md`.

**Then, in Claude Code (inside the "projects with cladue" folder):**
```
/update-primer "primer docs/transcripts/2026-07-15-bandit-l16.md"
```

The skill previews what it will change and asks before writing. A backup of the
primer is saved to `primer docs/backups/` on every write.
