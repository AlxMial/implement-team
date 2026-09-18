# FRONTEND DESIGN RULES

> **BINDING.** The short checklist every `[FRONTEND]` lane builds to and is gated
> against. The long form is `ux-ui-design-rules.md` in the same folder (70 rules) — this
> file is the working set; where they overlap they agree, where the long file is more
> specific it wins.

1. Prefer clean, modern, minimal UI.
2. Prioritize usability over decoration.
3. Follow an 8px-based spacing system.
4. Maintain clear visual hierarchy.
5. Use consistent typography, colors, radius and spacing.
6. Reuse existing components before creating new ones.
7. Design mobile-first and fully responsive.
8. Target WCAG 2.2 AA accessibility.
9. Every interactive component must include hover, focus, active, disabled and
   loading states where applicable.
10. Every data view must consider loading, empty and error states.
11. Avoid unnecessary gradients, excessive shadows and decorative effects.
12. Avoid excessive cards and nested containers.
13. Use one clear primary action per section.
14. Never sacrifice readability for aesthetics.
15. Do not introduce new UI patterns when an existing design-system component can
    solve the problem.
16. Keep layouts visually balanced with consistent alignment.
17. Use semantic HTML and keyboard-accessible interactions.
18. Prevent layout shifts and unexpected movement.
19. Make destructive actions clearly distinguishable and confirm them.
20. UI must look intentional at 375px, 768px, 1024px and 1440px.

## Non-negotiable floors

Rules 8, 9, 10, 17, 19 are floors, not preferences. A screen that misses one is a
FAIL finding, not a taste note.

**Design verification is static** — contrast, spacing, type scale and token use are read
from the markup, styles and tokens. Static is BETTER than a screenshot for these: a
contrast ratio is computed from the actual values, never judged by eye.

**Whether the screen RUNS is not static and cannot be.** Before any change to code that
executes on page load is called done, that page must be loaded once with the change live
and the console must be empty. A script that throws during init paints its loading state
forever, and no amount of reading the diff will show it.

Loading the happy path is not enough. If the change sits inside a condition — stored
state, a feature flag, an error branch — that condition must be created before the page
is loaded, or the check has not run.

> Amended 2026-09-17, replacing "Verification is static … no browser, no screenshots".
> That sentence covered two different things in one breath and the second one silently
> disappeared. It cost a day: `Cannot access 'openConfirmDialog' before initialization`
> killed an entire DOMContentLoaded handler and left every screen painting skeletons,
> while 25 static gates, 703 tests and a two-axis code review all passed. The defect sat
> behind a localStorage condition, so only a browser with that state could see it — and
> the rule forbade opening one. Driving the system Chrome via Playwright reproduces it in
> three seconds with no download and no login, so "no browser" was never a technical
> limit, only a policy.
