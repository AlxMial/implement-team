---
name: jinshi
description: "Hard-task chain for multi-step work: grill the plan against the codebase/docs, spec it, break into tickets, implement, review, record. Use for new features, cross-cutting changes, or anything with unclear requirements. Global — works in any project."
disable-model-invocation: true
---

# jinshi (hard chain)

> **UI work? Read the Frontend lane section below before step 1.** It is binding for
> any step that lays out controls or builds a screen.

Run these skills **in order**, in this same session. Invoke each one with the Skill tool —
do not work from memory of what it does. No extra subagent layer beyond what each skill
already spawns internally (`code-review` has its own parallel Standards/Spec sub-agents).
If a skill is missing on this machine, say so and stop — do not silently skip it.

1. **`grill-with-docs`** — stress-test the plan against the codebase and existing docs
   before writing anything down.
2. **`to-spec`** — turn the grilled plan into a spec.
   - No-tracker fallback: if the project has no `docs/agents/issue-tracker.md`,
     write the spec to `.scratch/<feature-slug>/spec.md` instead of publishing.
3. **`to-tickets`** — break the spec into tracer-bullet tickets. Same no-tracker
   fallback (it already writes to `.scratch/<feature-slug>/issues/`).
4. **`implement`** — work the frontier (tickets whose blockers are all done) until every
   ticket is done:
   - Invoke **`ponytail`** before the first line of code and keep it in force: no
     abstraction, boilerplate, config or dependency nobody asked for.
   - Read each ticket, then explore the codebase for the concrete file list it touches —
     `to-tickets` deliberately omits paths.
   - One ticket at a time. Do not roll several tickets into one commit.
   - **Re-read your own diff, then run the project's typecheck/lint/tests on the
     affected area.** No output, no acceptance.
   - Commit, same as `implement` normally does.
5. **`code-review`** — one pass over the full diff since the feature/branch start.
   Never skip it because you wrote the diff yourself; lean on its sub-agents and be
   harder on yourself, not softer.
6. **`scribe`** — record the run (goal, spec, what changed, review outcome). Runs last,
   always, even if the review cap stopped further fixing.

## If a chain skill is missing on this machine

`to-spec`, `to-tickets`, `implement`, `grill-with-docs`, `code-review`, `frontend-design`,
`impeccable` and `ponytail` are NOT shipped with this plugin — see the README for where to
get them. If one is missing, say which one, then continue with the inline fallback rather
than pretending the step happened:

| missing | fallback |
|---|---|
| `grill-with-docs` | interview the user about the plan against the codebase yourself, then continue |
| `to-spec` | write the spec to `.scratch/<slug>/spec.md` yourself, same headings |
| `to-tickets` | list the tickets in `.scratch/<slug>/issues/` yourself, one file each, each naming its blockers |
| `implement` | just build it, keeping every rule in this file |
| `code-review` | review the diff yourself against the spec and the repo's standards — never skip the pass |
| `ponytail` | apply the rule by hand: no abstraction, boilerplate, config or dependency nobody asked for |
| `frontend-design` / `impeccable` | gate the UI against the rules file below by hand |

## Frontend lane (BINDING when the work touches UI)

Triggers on any step that lays out controls, builds or changes a screen, or restyles
existing UI — web, mobile, LINE Mini App, kiosk, dashboard. If in doubt, it triggers.

**Before the spec is written**, invoke `frontend-design` and `impeccable`, then settle a
`## Design direction` in the spec: user, goal, primary action, information hierarchy,
then the concrete tokens — spacing scale (8px), type scale, colour tokens, radius scale,
and the existing components being reused. Read the project's existing tokens first and
cite the file; do not invent a parallel palette. "Consistent spacing" is not a design
direction — actual values are.

**Before code-review**, run `impeccable` over the UI diff and gate it against
`${CLAUDE_PLUGIN_ROOT}/rules/frontend-design-rules.md` (this plugin ships it; if the
variable is not expanded for you, read `../../rules/frontend-design-rules.md` relative to
this skill's own directory) item by item, statically — markup, styles,
tokens; no browser, no screenshots. A missed accessibility floor (rules 8, 9, 10, 17, 19)
or a missing loading/empty/error state is a FAIL on its own and goes back for a fix.
Aesthetic disagreement that breaks no rule is a note, not a FAIL — the user decides taste.

The review cap below still applies: fix once, re-review once.

**Review cap:** if `code-review` finds anything, fix it once and re-review once. Stop
after that second review regardless of outcome — do not loop further. Report the result
to the user and hand control back.

If a step surfaces that this was actually small enough for `/maomao`, say so, but don't
switch mid-chain — finish the current one.
