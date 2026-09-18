# RUNTIME VERIFICATION RULES

> **BINDING** for the review step of `/feature` and `/fix`, and for any UI change
> called done. Companion to `frontend-design-rules.md` in the same folder — that file
> covers what the markup must BE, this one covers proving it RUNS.

## 1. The browser is allowed. Playwright CLI is the tool.

Static review (contrast, tokens, spacing, semantics) stays static. But **whether the
screen runs is not static**: before any change to code that executes on page load is
called done, that page is loaded once with the change live and the console must be
empty.

Use the system Chrome via Playwright — no download, no login, no extra install:

```bash
npx playwright open --channel=chrome <url>          # interactive poke
npx playwright screenshot --channel=chrome <url> out.png
npx playwright cr <url>                              # console-visible session
```

For anything scripted, write a throwaway `.spec.ts`/`.mjs` in the scratchpad and run
`npx playwright test --project=chromium` — the script does not get committed unless
the project already has a Playwright suite and the case belongs in it.

Any project that already ships its own e2e runner: use that instead. Don't install a
second one.

## 2. Test scope: the change + 3 neighbours. That is the default.

**Default (every `/feature` and `/fix` run):**

1. **The changed surface** — every screen, route, or flow the diff actually touches.
   Load each one, exercise the changed behaviour, console empty.
2. **Three nearby regression cases, no more.** Pick the three by blast radius,
   highest first:
   - a direct caller / parent of the changed code,
   - a sibling that shares the changed component, store, hook, or table,
   - the flow immediately upstream or downstream (the screen you arrive from, or
     land on).
   Name the three in the review output and say why each was picked. Three real ones
   beat thirty skimmed.
3. Stop there. Do **not** sweep the rest of the app.

**Conditional branches count.** If the change sits behind stored state, a feature flag,
a role, or an error path, create that condition before loading — otherwise the check
has not run. A happy-path load of a flag-gated change is not verification.

**Full testing only on explicit request.** Sweep the whole system only when the user
says so — "review ทั้งระบบ", "full testing", "full regression", "review everything".
Never self-escalate to a full sweep because the change "felt risky"; say it felt risky
in the report and let the user call it.

## 3. Report what ran

The review output states, in three lines:

```
Changed surface tested: <routes/flows>            → pass / fail
Regression (3): <case> / <case> / <case>          → pass / fail
Console: clean | <the error, verbatim>
```

A console error is a FAIL, not a note — same weight as a missed accessibility floor.
Untested because it could not be reached: say so explicitly, never silently.

## 4. This does not extend the review cap

The review cap (fix once, re-review once) counts REVIEW rounds. A screen that does not
run is not a review finding — it is a failed build. Keep fixing until it runs.
