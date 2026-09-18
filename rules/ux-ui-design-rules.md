# UX/UI DESIGN RULES

> Global UX/UI standards for all web applications, mobile applications, dashboards, admin portals, LINE Mini Apps, kiosks, and internal systems.
>
> **Status: BINDING.** Adopted 2026-09-05 as the standing standard for every `[FRONTEND]`
> lane of the team chain. Architect decides `## Design direction` within these rules,
> Codex builds to them, Guardian gates against §64 and §65. A screen that violates a rule
> here is a FAIL finding, not a matter of taste. Where a project's own design system
> conflicts, the project's system wins on visual identity (§61) but never on
> accessibility (§34–§36) — those are floors, not preferences.

---

# 1. Purpose

This document defines the default UX/UI design rules that AI agents and developers MUST follow when designing or implementing any interface.

The objective is to create interfaces that are:

* Easy to understand
* Easy to use
* Consistent
* Accessible
* Responsive
* Visually clean
* Suitable for real-world usage
* Maintainable as the system grows

Do NOT optimize only for visual beauty.

A successful interface must prioritize:

1. Usability
2. Clarity
3. Accessibility
4. Consistency
5. Efficiency
6. Visual hierarchy
7. Aesthetic quality

---

# 2. Standards to Follow

All UX/UI work SHOULD reference the following standards.

## Primary Standards

### WCAG 2.2 Level AA

Use WCAG 2.2 AA as the minimum accessibility target.

Reference:

* W3C
* Web Content Accessibility Guidelines 2.2

### Nielsen's 10 Usability Heuristics

Use Nielsen's usability principles when evaluating flows and interactions.

### ISO 9241-210

Use Human-Centred Design principles.

Design around:

* User
* User goals
* Tasks
* Environment
* Usage context

### Platform Guidelines

Use appropriate platform guidelines when applicable.

For Android / Web:

* Material Design 3

For Apple platforms:

* Apple Human Interface Guidelines

---

# 3. Core Design Principle

Always design for the user task first.

Do NOT start by asking:

> How can this page look beautiful?

Start by asking:

> What is the user trying to accomplish?

Every screen MUST have a clear primary purpose.

If a UI element does not help the user:

* Understand something
* Make a decision
* Complete a task
* Navigate
* Receive feedback

consider removing it.

---

# 4. UX Priority

Use the following priority:

```text
User Goal
    ↓
User Flow
    ↓
Information Architecture
    ↓
Content Hierarchy
    ↓
Interaction
    ↓
Components
    ↓
Visual Design
    ↓
Animation / Decoration
```

Never reverse this hierarchy.

Do not design decoration before solving the user flow.

---

# 5. One Screen = One Primary Goal

Every screen MUST have one obvious primary goal.

Examples:

```text
Login
→ Sign in

Appointment Page
→ Book appointment

Patient Page
→ Understand patient information

Checkout
→ Complete payment

Dashboard
→ Understand current situation

Trainer Schedule
→ View or manage schedule
```

Secondary actions are allowed but MUST NOT compete visually with the primary action.

---

# 6. Visual Hierarchy

Users should understand the page within approximately 3–5 seconds.

Every page SHOULD clearly communicate:

1. Where am I?
2. What is this page?
3. What is important?
4. What can I do?
5. What happens next?

Use hierarchy through:

* Font size
* Font weight
* Spacing
* Position
* Contrast
* Grouping

Do NOT rely on excessive colors.

---

# 7. Layout System

Use a consistent layout system.

Recommended base spacing system:

```text
4px
8px
12px
16px
24px
32px
40px
48px
64px
```

Prefer an **8px grid system**.

Examples:

```text
Small gap       = 8px
Normal gap      = 16px
Section gap     = 24–32px
Large section   = 48–64px
```

Avoid random values such as:

```text
13px
19px
27px
37px
```

unless there is a specific design reason.

---

# 8. Page Container

Desktop content SHOULD normally use a maximum content width.

Recommended:

```text
max-width: 1200px–1440px
```

Do not stretch content across extremely wide monitors unnecessarily.

For forms and reading content:

```text
max-width: 600px–800px
```

is usually preferred.

---

# 9. Typography

Typography must prioritize readability.

Recommended font size:

```text
12px = small metadata only
14px = secondary text
16px = default body text
18px = emphasized body
20–24px = section title
28–36px = page title
```

Avoid body text smaller than 14px.

Default body text SHOULD normally be:

```text
16px
line-height: 1.4–1.6
```

Use no more than approximately:

```text
3–4 font sizes
3 font weights
```

per screen.

---

# 10. Text Rules

Avoid unnecessary technical terminology.

Prefer:

```text
Save changes
```

instead of:

```text
Persist configuration
```

Prefer:

```text
ไม่พบข้อมูลผู้ป่วย
```

instead of:

```text
Query returned empty dataset
```

UI text should speak the user's language, not the database's language.

---

# 11. Buttons

Buttons MUST clearly describe the action.

Good:

```text
บันทึกข้อมูล
จองคิว
ยืนยันการชำระเงิน
เพิ่มผู้ใช้งาน
ส่งข้อความ
```

Avoid vague labels such as:

```text
OK
Submit
Proceed
Action
Continue
```

unless the context makes the action completely obvious.

---

# 12. Button Hierarchy

Use three main levels.

## Primary

The main action.

Examples:

```text
Save
Book
Pay
Confirm
Create
```

Only one primary action SHOULD dominate a section.

## Secondary

Supporting action.

Examples:

```text
Edit
Preview
Back
```

## Tertiary / Ghost

Low-priority actions.

Examples:

```text
Cancel
Learn more
View details
```

Avoid having several visually dominant buttons next to each other.

---

# 13. Button Size

Interactive targets MUST be easy to tap.

Recommended minimum:

```text
44 × 44 px
```

Prefer:

```text
48px
```

for touch-oriented interfaces.

Especially important for:

* Mobile
* LINE Mini App
* Tablet
* Kiosk
* Healthcare interfaces

---

# 14. Forms

Forms MUST be easy to scan and complete.

Prefer:

```text
Label

[ Input field ]

Helper / error text
```

Do NOT use placeholder text as the only label.

Bad:

```text
[ Enter your email ]
```

Better:

```text
Email

[ example@email.com ]
```

---

# 15. Form Layout

For most forms, prefer a single-column layout.

Good:

```text
Name
[             ]

Phone
[             ]

Email
[             ]
```

Avoid excessive multi-column forms.

Multi-column forms may be used when fields are naturally related.

Example:

```text
First Name | Last Name
```

---

# 16. Validation

Validation should happen as close as possible to the relevant field.

Bad:

```text
Error occurred.
```

Good:

```text
หมายเลขโทรศัพท์ต้องมี 10 หลัก
```

Errors MUST explain:

1. What happened
2. Where the problem is
3. How to fix it

---

# 17. Prevent Errors

Whenever possible:

> Prevent errors instead of displaying errors after they happen.

Examples:

Use:

```text
Date Picker
```

instead of requiring:

```text
DD/MM/YYYY
```

Use:

```text
Dropdown
Radio
Checkbox
Autocomplete
```

when the possible values are known.

Disable invalid actions when appropriate.

---

# 18. System Status

The system MUST always communicate important status.

Examples:

```text
Loading...
Saving...
Uploading...
Processing...
Saved successfully
Payment completed
Connection failed
```

Users must never wonder:

> Did the button work?

---

# 19. Loading States

Never leave the UI frozen without feedback.

Use:

* Skeleton
* Spinner
* Progress indicator
* Loading message

For operations longer than a few seconds, explain what is happening.

Example:

```text
กำลังวิเคราะห์ข้อมูลสุขภาพ...
```

instead of only displaying a spinner.

---

# 20. Empty States

Do NOT show an empty blank screen.

Bad:

```text
No Data
```

Better:

```text
ยังไม่มีรายการนัดหมาย

เมื่อมีการจอง รายการนัดหมายจะแสดงที่นี่

[ สร้างนัดหมาย ]
```

Empty states SHOULD answer:

1. Why is this empty?
2. What can the user do next?

---

# 21. Error States

Error screens must provide recovery.

Bad:

```text
Error 500
```

Better:

```text
ไม่สามารถโหลดข้อมูลได้

กรุณาลองใหม่อีกครั้ง

[ ลองใหม่ ]
```

Technical details should be available only when useful to developers.

---

# 22. Destructive Actions

Destructive actions MUST be visually and behaviorally distinct.

Examples:

```text
Delete
Remove
Cancel Appointment
Reset
```

Use confirmation when the action:

* Cannot be undone
* Deletes important data
* Impacts another user
* Causes financial consequences

Avoid confirmation dialogs for harmless actions.

---

# 23. Undo

When possible, prefer:

```text
Action + Undo
```

over unnecessary confirmation dialogs.

Example:

```text
ลบรายการแล้ว

[ Undo ]
```

---

# 24. Navigation

Navigation must answer:

> Where am I?

Users should understand their current location.

Use:

* Active navigation state
* Page title
* Breadcrumb where appropriate

Do NOT hide important navigation unnecessarily.

---

# 25. Navigation Depth

Avoid excessively deep navigation.

Prefer:

```text
2–3 levels
```

Example:

```text
Patients
→ Patient Detail
→ Lab Result
```

Avoid structures like:

```text
Menu
→ Settings
→ Configuration
→ Management
→ Data
→ Patient
→ Edit
```

---

# 26. Mobile Navigation

For common mobile applications, prefer approximately:

```text
3–5 primary navigation destinations
```

Examples:

```text
Home
Schedule
Activity
Messages
Profile
```

Avoid putting 8–10 primary tabs at the bottom.

---

# 27. Cards

Cards should group related information.

Do NOT turn every piece of content into a card.

Bad:

```text
Card inside card
inside card
inside another card
```

Use whitespace and headings instead.

Cards are useful when information has:

* Independent meaning
* Independent action
* Repeating structure
* Clear grouping

---

# 28. Tables

Tables are appropriate for structured datasets.

Desktop:

Use tables for:

* Admin systems
* Reports
* Logs
* Transactions
* Patient lists
* Inventory

Mobile:

Do NOT force large desktop tables into narrow screens.

Consider:

* Responsive cards
* Horizontal scroll
* Priority columns
* Detail view

---

# 29. Search

Search SHOULD be available when users may need to find items from a large dataset.

Examples:

* Patients
* Customers
* Orders
* Appointments
* Products
* Trainers

Search should tolerate reasonable partial matches.

---

# 30. Filters

Avoid overwhelming users with filters.

Display common filters first.

Advanced filters can be hidden behind:

```text
More filters
Advanced filters
```

Always show active filters.

Provide:

```text
Clear filters
```

when multiple filters can be applied.

---

# 31. Color

Color MUST have meaning.

Example system:

```text
Primary
Secondary
Success
Warning
Error
Info
Neutral
```

Do NOT randomly use colors simply to make the screen appear more interesting.

---

# 32. Semantic Colors

Use colors consistently.

Example:

```text
Green  → Success
Red    → Error / Danger
Yellow → Warning
Blue   → Information
Gray   → Neutral
```

Do NOT use the same red color for both:

```text
Error
```

and

```text
Normal decoration
```

---

# 33. Never Use Color Alone

Never communicate important meaning only with color.

Bad:

```text
Red dot
Green dot
```

Better:

```text
● Offline
● Online
```

or:

```text
⚠ Needs attention
✓ Completed
```

---

# 34. Accessibility

Target:

```text
WCAG 2.2 Level AA
```

Minimum requirements include:

* Sufficient color contrast
* Keyboard navigation
* Visible focus states
* Proper semantic HTML
* Accessible form labels
* Alternative text
* Appropriate touch target size
* Error identification
* Screen reader compatibility

---

# 35. Contrast

Normal text SHOULD generally meet:

```text
4.5 : 1
```

Large text SHOULD generally meet:

```text
3 : 1
```

Do not use very light gray text on white backgrounds.

---

# 36. Focus State

Keyboard focus MUST be visible.

Never globally apply:

```css
outline: none;
```

without providing an accessible replacement.

Interactive components MUST visually communicate keyboard focus.

---

# 37. Icons

Icons SHOULD support understanding.

Do not assume every icon is universally understood.

Use text labels for important actions.

Prefer:

```text
🗑 Delete
✎ Edit
⬇ Download
```

rather than icon-only controls when meaning may be ambiguous.

Tooltips may support icon-only buttons.

---

# 38. Icon Consistency

Use one icon family per product where possible.

Examples:

* Lucide
* Material Symbols
* Heroicons

Do NOT mix several unrelated icon styles.

---

# 39. Modal / Dialog

Use dialogs only when the user must make an immediate decision.

Good use cases:

* Confirmation
* Important warning
* Short form
* Critical selection

Bad use cases:

* Long forms
* Full pages
* Large data tables
* Multi-step workflows

---

# 40. Toast Notifications

Use toast notifications for temporary feedback.

Examples:

```text
Saved successfully
Copied to clipboard
Appointment created
```

Do NOT put important information only inside a toast because it disappears.

---

# 41. Responsive Design

Every interface MUST consider at least:

```text
Mobile
Tablet
Desktop
```

Recommended breakpoints may include:

```text
Mobile      < 640px
Tablet      640–1024px
Desktop     > 1024px
```

Breakpoints should respond to layout needs rather than devices alone.

---

# 42. Mobile First

For consumer-facing applications, consider mobile-first design.

Ask:

```text
Can the user complete the main task with one hand?
```

Important actions SHOULD be reachable without excessive scrolling.

---

# 43. Kiosk / Large Touch Screen

For kiosk interfaces:

* Use large touch targets
* Minimize typing
* Use high contrast
* Keep flows short
* Use large typography
* Avoid small controls
* Give clear progress feedback
* Automatically recover from inactivity where appropriate

Recommended touch target:

```text
48–64px+
```

---

# 44. LINE Mini App

For LINE Mini Apps:

Prioritize:

* Mobile-first
* Fast loading
* Minimal steps
* Large tap targets
* LINE-native behavior
* Clear back navigation
* Avoid unnecessary login
* Avoid duplicate user information when LINE profile data is available

Do NOT design LINE Mini Apps like desktop admin systems.

---

# 45. Healthcare Interfaces

For healthcare systems, prioritize clarity over decoration.

Important information SHOULD be immediately visible.

Examples:

```text
Patient
Age
Important condition
Current status
Critical lab values
Medication
Allergy
Next action
```

Never communicate critical medical information using color alone.

Use:

```text
Color + Icon + Text
```

Example:

```text
⚠ LDL สูง
```

not just a red number.

---

# 46. Dashboard Design

A dashboard must answer:

```text
What is happening?
What needs attention?
What should I do?
```

Priority order:

```text
Critical information
↓
Primary KPI
↓
Trends
↓
Actionable information
↓
Detailed data
```

Do NOT fill dashboards with charts simply because space is available.

Every chart must answer a specific question.

---

# 47. Charts

Before creating a chart, ask:

> What question does this chart answer?

Examples:

```text
Has revenue increased?
Which category performs best?
How has weight changed?
Which clinic has the longest wait?
```

Avoid decorative charts.

Prefer simple visualization over visually complex visualization.

---

# 48. Information Density

Match information density with user type.

Consumer:

```text
Low–medium density
```

Admin:

```text
Medium–high density
```

Professional:

```text
High density when justified
```

Healthcare / Operations:

```text
High density but strong hierarchy
```

Do not apply the same UI density to every user type.

---

# 49. Progressive Disclosure

Do not show everything at once.

Show:

```text
Most important information
```

first.

Reveal:

```text
Advanced details
```

when needed.

Use:

* Accordion
* Details
* More
* Advanced options
* Drill-down

---

# 50. Recognition Over Recall

Do not force users to remember information between screens.

Prefer:

```text
Select clinic
[ Narada Clinic ]
```

instead of asking the user to remember a clinic code.

Show relevant context whenever possible.

---

# 51. Consistency

The same action MUST behave the same way across the application.

Examples:

If primary buttons are blue, keep them blue.

If destructive buttons are red, keep them red.

If Save is bottom-right in forms, maintain this pattern when practical.

Do not redesign basic interaction patterns screen-by-screen.

---

# 52. Component Reuse

Before creating a new component, check whether an existing component already solves the problem.

Prefer:

```text
Design System Component
```

over:

```text
One-off custom component
```

Create new patterns only when required.

---

# 53. Design Tokens

Use design tokens.

Example:

```text
color.primary
color.success
color.warning
color.error

spacing.1
spacing.2
spacing.3

radius.sm
radius.md
radius.lg

font.body
font.heading
```

Avoid hardcoded visual values across components.

---

# 54. Recommended Border Radius

Choose a consistent radius system.

Example:

```text
4px
8px
12px
16px
```

Do not randomly mix:

```text
5px
9px
13px
21px
```

---

# 55. Shadows

Use shadows sparingly.

Use them primarily to communicate elevation.

Examples:

* Dropdown
* Floating panel
* Modal
* Popover

Do not add strong shadows to every card.

---

# 56. Animation

Animation MUST serve a purpose.

Valid purposes:

* Show relationship
* Show state change
* Provide feedback
* Guide attention

Avoid animation purely for decoration when it slows interaction.

Recommended UI animation duration:

```text
150–300ms
```

---

# 57. Avoid Visual Noise

Avoid unnecessary:

* Gradients
* Shadows
* Glassmorphism
* Neon effects
* Excessive borders
* Excessive cards
* Excessive badges
* Excessive icons
* Excessive animation

Use these only when they strengthen the product identity or hierarchy.

---

# 58. Do Not Blindly Copy Trends

Do NOT blindly copy designs from:

* Dribbble
* Behance
* Pinterest
* AI-generated UI galleries

These can be used for visual inspiration.

They are NOT UX standards.

Real product UX must prioritize usability.

---

# 59. AI Design Behavior

When AI is asked to design a screen, it MUST first determine:

```text
User
Goal
Context
Primary Action
Secondary Actions
Information Priority
Possible Error States
Empty State
Loading State
Responsive Behavior
```

Only then should AI create the visual interface.

---

# 60. AI Must Not Invent Unnecessary Features

Do not add features simply to make the UI appear sophisticated.

Examples to avoid unless required:

```text
AI Assistant
Chatbot
Analytics
Charts
Notifications
Gamification
Social Feed
Complex filters
```

Every feature must solve a user need.

---

# 61. AI Must Preserve Existing Design Systems

When modifying an existing product:

AI MUST first inspect:

* Existing colors
* Typography
* Components
* Layout
* Button styles
* Input styles
* Navigation
* Radius
* Spacing
* Icons

Do NOT redesign the entire UI unless explicitly requested.

Prefer extending the current design system.

---

# 62. AI Must Avoid UI Inconsistency

AI MUST NOT randomly introduce:

* New colors
* New fonts
* New button styles
* New border radii
* New spacing scales
* New icon libraries

without a clear reason.

---

# 63. AI Screen Design Process

For every new screen follow:

```text
1. Identify user
2. Identify user goal
3. Define primary action
4. Define information hierarchy
5. Define user flow
6. Choose components
7. Design layout
8. Add responsive behavior
9. Add accessibility
10. Add loading state
11. Add empty state
12. Add error state
13. Review against usability rules
```

---

# 64. UX Review Checklist

Before considering a screen complete, verify:

## Goal

* [ ] Is the main purpose obvious?
* [ ] Is there one clear primary action?

## Hierarchy

* [ ] Can users understand the screen within 3–5 seconds?
* [ ] Is important information visually prioritized?

## Navigation

* [ ] Does the user know where they are?
* [ ] Can the user go back safely?

## Form

* [ ] Are labels visible?
* [ ] Are errors understandable?
* [ ] Are input types appropriate?

## Feedback

* [ ] Is loading communicated?
* [ ] Is success communicated?
* [ ] Are errors recoverable?

## Empty State

* [ ] Does the empty state explain what happens next?

## Accessibility

* [ ] Is contrast sufficient?
* [ ] Are touch targets large enough?
* [ ] Is keyboard navigation supported?
* [ ] Is focus visible?
* [ ] Is important information not color-only?

## Responsive

* [ ] Mobile checked
* [ ] Tablet checked
* [ ] Desktop checked

## Consistency

* [ ] Uses existing components
* [ ] Uses design tokens
* [ ] Matches product visual language

---

# 65. Nielsen Heuristic Review

Before finalizing, AI SHOULD evaluate the interface against:

### 1. Visibility of System Status

Always keep users informed.

### 2. Match Between System and Real World

Use familiar language and concepts.

### 3. User Control and Freedom

Support:

```text
Back
Cancel
Undo
Exit
```

### 4. Consistency and Standards

Follow common UI conventions.

### 5. Error Prevention

Prevent problems before they happen.

### 6. Recognition Rather Than Recall

Do not force users to remember information.

### 7. Flexibility and Efficiency

Support efficient workflows.

### 8. Aesthetic and Minimalist Design

Remove irrelevant information.

### 9. Help Users Recover from Errors

Explain the problem and solution.

### 10. Help and Documentation

Provide guidance when required.

---

# 66. Final UX Principle

Whenever there is a conflict between:

```text
Beautiful UI
```

and

```text
Easy-to-use UI
```

choose:

```text
Easy-to-use UI
```

Then make it beautiful without damaging usability.

---

# 67. AI Final Instruction

When generating UI:

DO NOT immediately generate code.

First reason about:

```text
User → Goal → Flow → Hierarchy → Components → UI
```

Always prefer:

```text
Simple
Clear
Predictable
Fast
Accessible
Consistent
```

over:

```text
Fancy
Complex
Experimental
Decorative
```

unless the project explicitly requires an experimental visual experience.

---

# 68. Default Design Philosophy

Use this philosophy by default:

> Clean, modern, minimal, functional, accessible, and user-centered.

The interface should feel polished but should never make the user think about how to use it.

Good UX feels obvious.

---

# 69. Definition of Done

A UI is NOT complete only because:

```text
It looks good.
```

A UI is complete when:

```text
User understands it
+
User can complete the task
+
System provides feedback
+
Errors are recoverable
+
UI works across screen sizes
+
Accessibility requirements are satisfied
+
Design system remains consistent
```

---

# 70. Decision Rule

If uncertain between two designs, choose the design that requires:

```text
Less thinking
Less memory
Less typing
Fewer clicks
Fewer decisions
Less navigation
```

while still preserving necessary information and user control.

---

# END OF UX/UI DESIGN RULES

---

# Team-chain application notes

These notes are how the chain applies the rules above. They add nothing to the standard;
they say who enforces which part.

**Stage 1 — architect.** `## Design direction` is decided *inside* these rules. §59 and
§63 are the order of reasoning; the section may not be written before user, goal, primary
action and information priority are settled. §7, §9, §53 and §54 mean the spec states an
actual spacing scale, type scale, token names and radius scale — not "consistent spacing".
§61 and §62 mean an existing product's tokens ARE the direction: read them and cite the
file rather than inventing a parallel palette. §60 means no feature enters the spec because
it would look impressive.

**Stage 2a — Codex.** Builds the decided direction exactly, and every state §64 lists
(§19 loading, §20 empty, §21 error, §41 responsive) is part of "built", not a follow-up.
Accessibility floors — §13 touch targets, §35 contrast, §36 focus, §33 never colour alone,
§14 real labels — are not tradeable for a smaller diff.

**Stage 3a — guardian.** Gates against §64 item by item and §65, reading the markup, styles
and tokens the diff produced. Contrast and target size are computed from the actual token
values, not eyeballed — that part of the 2026-09-09 decision stands, because a computed
ratio beats a screenshot.

What that decision got wrong, **amended 2026-09-17**, is the half that said "no browser"
and handed the rendered screen to the user's own eyes at 3a′. That made every runtime
defect the user's job to find, one at a time, after the chain had already declared itself
done. Whether the code RUNS is now part of 3a: the page is loaded once with the change
live, with any stored state the change depends on, and the console must be empty. 3a′
stays what it always should have been — the user's call on TASTE (§66), not the only
place anyone notices the screen is broken. A
violation of an accessibility floor (§13, §33, §35, §36, §14) is a FAIL on its own. So is a
missing empty / loading / error state, and so is §57 visual noise substituting for
hierarchy. Aesthetic disagreement that breaks no rule here is a note, not a FAIL — §66 and
§3a′ leave taste to the user.

**Stage 3a′ — the user.** §66 is his call, not the chain's: when beauty and usability
conflict the chain ships usable, and he decides whether the result is good enough to keep.
