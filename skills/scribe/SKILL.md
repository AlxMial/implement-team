---
name: scribe
description: "Record what a /jinshi or /maomao run did into the project's Obsidian vault: one changelog note with goal, spec, what changed, and review outcome. Final step of both chains."
---

# scribe

Writes one changelog note into Obsidian after a chain run finishes. Does NOT touch project code, does not commit anything.

## Steps

1. Locate the project's vault:
   ```
   obsidian=$(find . -maxdepth 3 -name .obsidian -type d 2>/dev/null | head -1)
   vault=""; [ -n "$obsidian" ] && vault=$(dirname "$obsidian")
   ```
   - If `$vault` is empty, **do not fall back to a vault elsewhere.** Stop here and
     tell the user: no Obsidian vault (`.obsidian/`) was found in this project, so
     the changelog note was not written — ask them to point at a vault or init one
     if they want this run recorded. Do not write anything.
   - Otherwise `mkdir -p "$vault/Changelog"` and continue.
2. Gather the facts from this run — do not invent anything:
   - `git diff`/`git log` for what actually changed
   - the spec (`.scratch/<slug>/spec.md`) and tickets (`.scratch/<slug>/issues/`), if any
   - the `code-review` verdict/findings from this run
   - who wrote the diff (Claude directly, or MiniMax M3 via `minimax-tmux`)
3. Use `claude-obsidian:obsidian-markdown` for correct frontmatter/wikilink syntax, then write ONE note at `$vault/Changelog/$(date +%F)-<short-slug>.md`:

   ```markdown
   ---
   date: <YYYY-MM-DD>
   type: changelog
   ---

   # <one-line title of the change>

   **Goal:** <what the task was>
   **Spec:** [[<spec basename, no .md>]]  (omit if no spec file exists)
   **Built by:** <Claude / MiniMax M3 via minimax-tmux>
   **Review:** <code-review verdict — pass, or fixed-once-then-passed>

   ## Changed
   - `path` — what changed and why

   ## Impact / watch-outs
   - <side effects, callers touched, anything future work should know>
   ```
4. Link related prior changelog notes with `[[note-name]]` if any turn up.

Final message: the path of the note you wrote, plus a one-line summary. Nothing else.
