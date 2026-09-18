# implement-team

Two slash commands that run the skills you already have **in a fixed order**, so a piece of
work always gets spec'd, built, reviewed and recorded — instead of Claude jumping straight
to code and you remembering the rest by hand.

They are not agents, not a framework, not code. Two markdown files, ~130 lines each.

| | `/feature` (hard chain) | `/fix` (easy chain) |
|---|---|---|
| for | new features, cross-cutting changes, unclear requirements | small, well-understood work |
| order | grill-with-docs → to-spec → to-tickets → implement → code-review → scribe | to-spec → implement → code-review → scribe |

Both start with a **step 0** before any analysis: pin `BASE` (`git rev-parse HEAD`), grep the
project's Obsidian changelog for what past runs learned about this area, write a TodoWrite
list, and write `.scratch/<slug>/CHAIN.md` — one checkbox per step, so a dropped session
resumes at the first unticked box instead of restarting the chain.

## Install

```
/plugin marketplace add AlxMial/implement-team
/plugin install implement-team@implement-team
```

Restart Claude Code, then type `/feature` or `/fix`.

## What's in the box

- `skills/feature`, `skills/fix` — the two chains
- `skills/scribe` — writes one changelog note per run into the project's Obsidian vault
- `rules/frontend-design-rules.md` — the 20 rules the frontend lane gates against
- `rules/ux-ui-design-rules.md` — the long form (70 rules) for when 20 isn't enough
- `rules/runtime-verification.md` — the browser side: the changed surface plus 3 regression
  cases loaded in the system Chrome, console empty

## Prerequisites (not shipped — other people's work)

The chains call these by name. Install what you can; the chains name any missing one and
fall back inline rather than skipping the step silently.

| skill | where it comes from |
|---|---|
| `code-review`, `frontend-design` | official Claude Code plugin marketplace — `/plugin` |
| `impeccable` | `npx impeccable` |
| `grill-with-docs`, `to-spec`, `to-tickets`, `implement` | Matt Pocock's skill set (`/setup-matt-pocock-skills`) |
| `superpowers:systematic-debugging` | the superpowers plugin marketplace — called on the two-strike rule |
| `codex-review` | your own second-opinion skill — called only on high-stakes diffs |
| `claude-obsidian` | optional — `scribe` uses it for correct Obsidian syntax |

## The three rules worth keeping if you fork this

1. **Review cap** — round 1 finds things, fix, round 2; stop when round 2 surfaces nothing
   new (a repeat of a round-1 finding is not new). The predecessor of this chain let
   reviewer and builder talk until "pass" and it looped four rounds without the code
   getting better. A console error, a red test or a broken build is **not** a review
   finding and the cap does not cover it — keep fixing until it runs.
2. **`scribe` always runs last**, even when the cap ended the run with findings open.
   No record means the next run's grilling starts from zero.
3. **The frontend lane is binding** — a missed accessibility floor (rules 8, 9, 10, 17, 19)
   or a missing loading/empty/error state is a FAIL on its own, not a taste note. Static
   gates the look; the browser gates whether it runs. Skipping the browser cost a day once:
   one init-time error killed every screen while 25 static gates, 703 tests and a two-axis
   code review all passed.

## Calling them by your own name

The chain names are just skill names — if `/feature` and `/fix` clash with something else you use, alias them.
A personal alias is one file, no fork needed:

```bash
mkdir -p ~/.claude/commands
cat > ~/.claude/commands/ship.md <<'EOF'
---
description: Hard chain — spec, tickets, implement, review, record
---
Invoke the `feature` skill and follow it exactly.

Task: $ARGUMENTS
EOF
```

Now `/ship <task>` runs the hard chain. Same trick with `fix` for the easy one.
Put the file in `<project>/.claude/commands/` instead to make the name project-wide, or
commit it so the whole team gets it.

For a permanent team-wide rename, fork this repo and rename the skill directory **and**
the `name:` field in its frontmatter — they must match.

## Teaching it

`TEACHING.md` is a standalone walkthrough — concept, install, the skill files, the reasoning
behind each rule, worked example, FAQ. Read that one file and you can teach the whole thing.
