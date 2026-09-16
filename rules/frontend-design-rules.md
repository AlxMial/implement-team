# FRONTEND DESIGN RULES

> **BINDING.** The short checklist every `[FRONTEND]` lane builds to and is gated
> against. The long form is `~/.claude/rules/ux-ui-design-rules.md` (70 rules) — this
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
FAIL finding, not a taste note. Verification is static — read the markup, styles and
tokens; no browser, no screenshots.
