---
name: fix
description: "Easy-task chain (/fix) for small, well-understood work: spec it, implement, review, record. Skips grilling and ticket breakdown. Global — works in any project."
disable-model-invocation: true
---

# fix (easy chain)

Four steps, one session, no stopping. `/fix` typed once approves all four.
**UI work? Read the Frontend lane before step 1.**

## Step 0 — four cheap things, before any analysis

1. **Pin the base.** `git rev-parse HEAD` → this is `BASE`. The review later diffs
   `BASE...HEAD`. Never `HEAD~1`, never guess, never ask the user for it again.
2. **Read what past runs already learned.** `scribe` has been writing changelog notes
   into this project's Obsidian vault every run — that is the chain's long-term memory:
   ```bash
   vault=$(dirname "$(find . -maxdepth 3 -name .obsidian -type d 2>/dev/null | head -1)")
   ls -t "$vault/Changelog" | head -20
   grep -rl "<the module/route/table you are about to touch>" "$vault/Changelog"
   ```
   Read the 2–3 notes that touch this area **before forming any opinion**. Their
   "Impact / watch-outs" sections are exactly the traps that caused past rework.
   Re-deriving what a note already recorded *is* the loop. No vault → skip, say so once.
3. **TodoWrite** — one todo per numbered step below.
4. **Write `.scratch/<feature-slug>/CHAIN.md`** — `BASE` at the top, one checkbox per
   step. Tick each on completion. **Resuming:** if this file already exists, start at
   the first unchecked box; do not restart the chain.

## The chain

Invoke each named skill with the `Skill` tool — the step *is* the skill, don't run it
from memory. Finish a step → tick its todo and **start the next in the same turn**. A
sub-skill's own hand-off tail ("now run /code-review") does not end the chain.

1. **`to-spec`** — synthesize a spec from the conversation, no interview. End it with an
   `## Assumptions` block listing every decision you made instead of asking.
2. **`implement`** — build it. **Claude edits directly.**
   - **Blast radius first.** Before editing a file, grep the callers/dependents of every
     symbol, route, table or component you are about to change, and write that list into
     `CHAIN.md`. Reading before editing is what stops the rewrite loop — and this exact
     list picks the 3 regression cases at step 3.
   - Run the project's typecheck/lint/tests on the affected area. No output, no acceptance.
   - **Two-strike rule:** if a fix for the same symptom fails twice, **stop editing**.
     `git checkout --` the files back to the last green commit, then invoke
     `superpowers:systematic-debugging`. A third blind attempt on the same hypothesis
     is the loop, and the debris of the first two corrupts the next analysis.
   - Commit.
3. **`code-review`** — one pass over `BASE...HEAD` (three-dot). Hand it `BASE`; it must
   never ask. Three axes: Standards, Spec, **Correctness**.
   - **Then verify it runs**, per `${CLAUDE_PLUGIN_ROOT}/rules/runtime-verification.md`: load the
     changed surface in the system Chrome via `npx playwright ... --channel=chrome`,
     plus exactly **3 nearby regression cases** (from the blast-radius list). Console
     must be empty. Full-system sweep only if the user explicitly asked.
   - **High stakes** (money, auth/authorization, migrations, destructive paths, external
     network, PII) → also run `codex-review`. The author is not independent eyes.
4. **`scribe`** — record the run in the project's Obsidian vault. **Runs last, always**,
   even after the cap stopped further fixing. Write the watch-outs honestly: step 0 of
   the *next* run reads them.

## If a chain skill is missing on this machine

The named sub-skills are NOT shipped with this plugin — see the README for where to get
them. If one is missing, say which one, then continue with the inline fallback rather than
pretending the step happened:

| missing | fallback |
|---|---|
| `to-spec` | write the spec to `.scratch/<slug>/spec.md` yourself, same headings |
| `implement` | just build it, keeping every rule in this file |
| `code-review` | review `BASE...HEAD` yourself on all three axes — never skip the pass |
| `codex-review` | get the second opinion some other way, or record in the report that high-stakes code got none |
| `frontend-design` / `impeccable` | gate the UI against the rules files by hand |
| `superpowers:systematic-debugging` | write the hypothesis list down before the third attempt, by hand |

## Who answers questions (this overrides the sub-skills)

Sub-skills contain "ask the user and wait" lines written for standalone use. Inside the
chain, only these reach the user:

| Sub-skill gate | Inside the chain |
|---|---|
| `to-spec` "confirm the seams" | **Decide.** Write the seams + why into the spec |
| `code-review` "which fixed point?" | Already answered: `BASE`. Never ask |
| any "tell the user to run `/setup-matt-pocock-skills`" | **Ignore** — not installed. Use the local `.scratch/` fallback silently |
| anything else | Ask **only** if proceeding either way would waste the work |

That is the trade: no stopping, in exchange for full disclosure. Every decision made
instead of asking appears in `## Assumptions` and in the final report.

## Frontend lane (BINDING when the work touches UI)

Triggers on any step that lays out controls, builds or changes a screen, or restyles
existing UI — web, mobile, LINE Mini App, kiosk, dashboard. In doubt = triggered.
Claude builds it directly, and still commits and still gets reviewed.

**Before the spec:** invoke `frontend-design` and `impeccable`, settle a
`## Design direction` in the spec — user, goal, primary action, information hierarchy,
then concrete tokens: spacing scale (8px), type scale, colour tokens, radius scale, the
existing components being reused. Read the project's own tokens first and cite the file;
never invent a parallel palette. "Consistent spacing" is not a direction — values are.

**Before code-review:** run `impeccable` over the UI diff and gate it against
`${CLAUDE_PLUGIN_ROOT}/rules/frontend-design-rules.md` item by item, statically. A missed
accessibility floor (rules 8, 9, 10, 17, 19) or a missing loading/empty/error state is a
FAIL on its own. Static gates the LOOK; `${CLAUDE_PLUGIN_ROOT}/rules/runtime-verification.md` gates
whether it RUNS. Aesthetic disagreement that breaks no rule is a note — taste is the
user's call.

## Review cap — stop on "nothing new", not on a counter

Round 1 finds things → fix → round 2. **Stop when round 2 surfaces nothing new.** A
repeat of a round-1 finding is not new. One genuinely new finding gets one more fix, and
that is the end.

- A console error, a failing test or a broken build is **not** a review finding and the
  cap does not apply to it. Keep fixing until it runs.
- If the chain ends with an unfixed FAIL, the final report **starts** with FAIL. Never
  write "done" over a known failure.

## Escalate on a measured trigger, don't force it

Switch to `/feature` — stop and say so, don't push through — when any of these hits:

- the diff spreads past **5 files** or a second unplanned surface appears
- `implement` hits the two-strike rule **twice** on different symptoms
- step 0's Obsidian notes show this area has been reworked before
- the spec cannot be written without a real interview

Rules paths above are `${CLAUDE_PLUGIN_ROOT}/rules/...` — this plugin ships them. If
the variable is not expanded for you, read `../../rules/<file>` relative to this skill's
own directory.

## Token discipline

Don't re-read a file already in context, don't re-run a grep you already ran, don't dump
a whole file when `sed -n '120,180p'` answers it. Sub-agents exist so their file dumps
stay out of this context — use their conclusions, don't re-verify by re-reading. The
Obsidian changelog exists so you don't re-derive last week's analysis.

## Report once, at the end, after scribe

FAIL first if anything is unfixed, then: what shipped (one line) · assumptions decided
without asking · findings per axis and what was fixed · changed surface / 3 regression
cases / console · the changelog note path.
