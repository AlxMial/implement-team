# /feature และ /fix — เอกสารสอนฉบับเต็ม

> อ่านไฟล์เดียวจบ สอนได้เลย ไม่ต้องเปิดไฟล์อื่นประกอบ
> ทุกอย่างในเอกสารนี้อยู่ที่ **github.com/AlxMial/implement-team**
> เวอร์ชันนี้คือ **Claude เขียนโค้ดเองล้วน** ไม่ต้องมี external worker หรือ CLI อะไรเพิ่ม
> ติดตั้งเป็น plugin: `/plugin marketplace add AlxMial/implement-team`

---

# ส่วนที่ 1 — มันคืออะไร

`/feature` และ `/fix` คือ **slash command สองตัวที่สั่งให้ Claude เรียก skill ที่มีอยู่แล้ว
ตามลำดับ** จบ. แค่นั้นจริง ๆ

มันไม่ใช่:
- ไม่ใช่ agent ใหม่
- ไม่ใช่ subagent ที่คุยกันเอง
- ไม่ใช่ framework
- ไม่มีโค้ด ไม่มี script ไม่มี dependency — มีแค่ไฟล์ markdown ไฟล์ละ ~60 บรรทัด

**ปัญหาที่มันแก้:** เวลาสั่ง Claude ว่า "ทำ feature นี้ให้หน่อย" Claude จะกระโดดไปเขียนโค้ดเลย
ไม่ได้ถาม ไม่ได้เขียน spec ไม่ได้ review ไม่ได้บันทึก พอสั่งเองทุกขั้นก็ลืมบ้าง ข้ามบ้าง
ลำดับไม่เหมือนเดิมทุกครั้ง — สองตัวนี้คือการ "แช่แข็งลำดับที่ดี" ไว้เป็นคำสั่งเดียว

**ทำไมต้องมีสองตัว:** เพราะงานมีสองขนาด ถ้ามีตัวเดียวคนจะเลี่ยงใช้ตอนงานเล็ก
(ขั้นตอนเยอะเกิน) แล้วสุดท้ายก็ไม่ได้ใช้เลย

| | `/feature` (ยาก) | `/fix` (ง่าย) |
|---|---|---|
| ใช้กับ | feature ใหม่, แก้ข้ามระบบ, requirement ยังไม่ชัด | งานเล็ก เข้าใจตรงกันแล้ว |
| ลำดับ | grill-with-docs → to-spec → to-tickets → implement → code-review → scribe | to-spec → implement → code-review → scribe |
| ต่างกันตรง | มีขั้น "ซัก" และ "แตก ticket" | ตัดสองขั้นนั้นทิ้ง |

ชื่อ `/feature` กับ `/fix` เลือกให้กลาง ๆ จำง่าย ไม่ผูกกับทีมใดทีมหนึ่ง
ไม่ชอบก็ตั้งชื่อเองได้ ไม่ต้อง fork — วิธีอยู่ในส่วนที่ 10

## ขั้นแต่ละขั้นทำอะไร

| skill | ทำอะไร | ทำไมต้องมี |
|---|---|---|
| `grill-with-docs` | ซัก plan กับ codebase และ doc ที่มีอยู่ ก่อนเขียนอะไรลงไป | จับ requirement ที่ขัดกับของเดิมตั้งแต่ก่อนเสียเวลาเขียน |
| `to-spec` | สรุปบทสนทนาเป็น spec (ไม่สัมภาษณ์ซ้ำ) | มีที่อ้างอิงว่า "ตกลงกันว่าอะไร" ตอน review |
| `to-tickets` | แตก spec เป็น ticket เล็ก ๆ พร้อมบอกว่าใบไหนบล็อกใบไหน | งานใหญ่ commit เดียวคือ review ไม่ได้ |
| `implement` | ลงมือทำทีละ ticket (เช็ก blast radius ก่อนแก้ทุกไฟล์) | รายชื่อ caller ที่ grep ไว้ = ตัวเลือก regression case ตอน review |
| `code-review` | review diff ทั้งก้อน (มี sub-agent แยก Standards / Spec ในตัว) | คนเขียนไม่ควรเป็นคนตรวจคนเดียว |
| `scribe` | บันทึกลง Obsidian vault ของโปรเจกต์ (เขียนเอง — ส่วนที่ 8 ถ้าไม่ใช้ Obsidian) | รอบหน้า **step 0** จะ grep โน้ตพวกนี้ก่อนคิดอะไรเลย |
| `codex-review` | ขอความเห็นที่สองตอนงานเป็น high stakes (เงิน, auth, migration, ลบข้อมูล, network, PII) | คนเขียนไม่ใช่ตาคู่ที่สอง |
| `superpowers:systematic-debugging` | เรียกเมื่อแก้อาการเดิมพลาด 2 ครั้ง (two-strike) | ครั้งที่ 3 แบบเดา = ลูป และซากของ 2 ครั้งแรกทำให้วิเคราะห์เพี้ยน |
| `frontend-design` | ทิศทางงานออกแบบ เรียกก่อนเขียน spec ที่มี UI | กันหน้าจอหน้าตา "AI generic" |
| `impeccable` | เครื่องมือ UI หลักของ chain — ดูหัวข้อถัดไป | เป็นตัวที่ทำให้ UI ถูก gate จริง ไม่ใช่แค่เขียนในกฎ |

## Step 0 — ก่อนขั้นที่ 1 ทั้งสอง chain ทำ 4 อย่างที่ถูกมาก

1. **ปัก `BASE`** ด้วย `git rev-parse HEAD` — review ตอนท้าย diff `BASE...HEAD` เสมอ
   ไม่ใช้ `HEAD~1` ไม่เดา และไม่ถาม user ซ้ำ
2. **อ่านโน้ตที่รอบก่อนเขียนไว้** — grep `Changelog/` ใน vault ด้วยชื่อ module / route /
   table ที่กำลังจะแตะ แล้วอ่าน 2–3 ใบที่เกี่ยว **ก่อนจะมีความเห็นอะไร** หัวข้อ
   "Impact / watch-outs" ในโน้ตคือกับดักที่ทำให้รอบก่อนต้องแก้ซ้ำ
3. **TodoWrite** ขั้นละ 1 todo — เป็นความจำของ chain ภายใน session
4. **เขียน `.scratch/<slug>/CHAIN.md`** — `BASE` อยู่หัวไฟล์ ขั้นละ 1 checkbox
   ถ้าไฟล์นี้มีอยู่แล้ว (session หลุด / เปิดใหม่) ให้เริ่มที่ช่องแรกที่ยังไม่ติ๊ก
   **ไม่ใช่เริ่ม chain ใหม่ทั้งหมด**

---

# ส่วนที่ 2 — `impeccable` คืออะไร (ตัวที่คนมักงง)

`impeccable` เป็น skill ภายนอก (Apache 2.0, ลงได้ด้วย `npx impeccable`) ที่ทำเรื่อง
frontend craft โดยเฉพาะ — ไม่ใช่ของที่เขียนขึ้นมาเองเหมือน `scribe`
chain ใช้มัน **สองจุด** และเป็นสองจุดที่ต่างกันคนละเรื่อง

| จุดที่เรียก | เรียกทำไม |
|---|---|
| **ก่อนเขียน spec** (ตอนงานแตะ UI) | ให้มันช่วยตั้ง `## Design direction` — โครงหน้าจอ, hierarchy, token ที่จะใช้ |
| **ก่อน `code-review`** | ให้มันไล่ตรวจ UI diff เทียบกฎ ทีละข้อ แบบอ่านโค้ด (static) |

`impeccable` gate **หน้าตา** เท่านั้น ส่วน "หน้านั้นรันได้จริงไหม" เป็นของ
`rules/runtime-verification.md` — เปิด Chrome จริงด้วย Playwright แล้ว console ต้องว่าง

มันมี sub-command ในตัว สั่งเจาะจงได้ เช่น
`impeccable shape <target>` (วางโครง), `impeccable craft <target>` (ลงมือทำ),
`impeccable audit <target>` (ตรวจ), `impeccable critique <target>` (วิจารณ์),
`impeccable polish` / `harden` / `clarify` / `typeset` / `layout`
— ในบริบท chain: **`shape` ตอนต้น, `audit` ตอนท้าย** คือคู่ที่ใช้บ่อยที่สุด

**ข้อควรระวัง 2 ข้อที่เจอตอนติดตั้งใหม่:**

1. **มันขอ `PRODUCT.md` ในโปรเจกต์** ถ้าไม่มี มันจะหยุดแล้วพาไป flow `init` ก่อน
   ซึ่งจะทำให้ chain สะดุดกลางคัน — โปรเจกต์ใหม่ให้รัน `impeccable init` แยกให้จบก่อน
   แล้วค่อยเริ่ม `/feature`
2. **path ใน SKILL.md ของมันเขียนแบบ project-local** (`node .claude/skills/impeccable/...`)
   แต่เราลงไว้ที่ `~/.claude/skills/` ถ้า script รันไม่ผ่าน ให้ Claude ใช้ path เต็ม
   `~/.claude/skills/impeccable/scripts/context.mjs` แทน

**ไม่มี `impeccable` แล้วใช้ chain ได้ไหม** — ได้ แต่ Frontend lane จะเหลือแค่
`frontend-design` + ไล่กฎเอง ซึ่งหลวมกว่าเยอะ ถ้าทีมทำ UI เป็นหลัก แนะนำให้ลง

---

# ส่วนที่ 3 — ติดตั้ง

**1. ลง plugin (2 คำสั่ง)**

```
/plugin marketplace add AlxMial/implement-team
/plugin install implement-team@implement-team
```

restart Claude Code แล้วพิมพ์ `/feature` หรือ `/fix` ได้เลย
ได้มาทั้ง chain สองตัว + `scribe` + ไฟล์กฎ UI (อยู่ในตัว plugin ไม่ต้องก๊อปแยก)
อัปเดตภายหลังด้วย `/plugin update implement-team`

**2. ลง skill ที่ chain เรียกใช้ (ของคนอื่น ไม่ได้แถมมาใน plugin)**

| skill | ลงยังไง |
|---|---|
| `code-review`, `frontend-design` | official marketplace ของ Claude — `/plugin` |
| `impeccable` | `npx impeccable` |
| `codex-review` | ของใครของมัน — chain เรียกเฉพาะงาน high stakes ถ้าไม่มีให้เขียนในรายงานว่างานนี้ไม่ได้ตาคู่ที่สอง |
| `superpowers` | marketplace ของมันเอง — chain ใช้ `superpowers:systematic-debugging` ตอน two-strike |
| `grill-with-docs`, `to-spec`, `to-tickets`, `implement` | ชุด skill ของ Matt Pocock (`/setup-matt-pocock-skills`) |
| `claude-obsidian` | เฉพาะคนใช้ Obsidian — `scribe` เรียกเพื่อให้ syntax ถูก |

**ขาดบางตัวแล้วใช้ได้ไหม** — ได้ chain จะบอกว่าขาดตัวไหน แล้วทำ fallback แทน
(มีตารางอยู่ในตัว `SKILL.md` ส่วนที่ 4) ไม่ใช่เงียบแล้วข้ามขั้น
แต่ยิ่งขาดมาก คุณภาพยิ่งตก โดยเฉพาะ `code-review` กับ `impeccable`

**เช็กก่อนไปต่อ** — พิมพ์ `/` ต้องเห็น `feature` กับ `fix` ถ้าไม่เห็นแปลว่ายังไม่ได้ restart

---

# ส่วนที่ 4 — ตัว skill (เนื้อเต็ม)

ลง plugin แล้วได้สองไฟล์นี้อัตโนมัติ ที่แปะไว้เพื่ออ่านตอนสอนโดยไม่ต้องเปิดไฟล์

## `skills/feature/SKILL.md`

~~~~~markdown
---
name: feature
description: "Hard-task chain (/feature) for multi-step work: grill the plan against the codebase/docs, spec it, break into tickets, implement, review, record. Use for new features, cross-cutting changes, or anything with unclear requirements. Global — works in any project."
disable-model-invocation: true
---

# feature (hard chain)

Six steps, one session, no stopping. `/feature` typed once approves all six.
**UI work? Read the Frontend lane before step 1.**

## Step 0 — four cheap things, before any analysis

1. **Pin the base.** `git rev-parse HEAD` → this is `BASE`. Every review later diffs
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
3. **TodoWrite** — one todo per numbered step below. That list is the chain's memory
   within the session.
4. **Write `.scratch/<feature-slug>/CHAIN.md`** — `BASE` at the top, one checkbox per
   step. Tick each on completion. **Resuming:** if this file already exists, start at
   the first unchecked box; do not restart the chain.

## The chain

Invoke each named skill with the `Skill` tool — the step *is* the skill, don't run it
from memory. Finish a step → tick its todo and **start the next in the same turn**. A
sub-skill's own hand-off tail ("now run /code-review") does not end the chain.

1. **`grill-with-docs`** — stress-test the plan against the codebase and docs.
   **Grill cap:** questions the codebase can answer are not questions — go read.
   What is left, ask in **batches of up to 5**, each with your recommended answer,
   **max 2 batches**. Anything still open after that: decide it yourself and record it
   under `## Assumptions`.
2. **`to-spec`** — turn the grilled plan into a spec. End the spec with an
   `## Assumptions` block listing every decision you made instead of asking.
3. **`to-tickets`** — tracer-bullet tickets with blocking edges.
4. **`implement`** — work the frontier until every ticket is done. **Claude edits
   directly.**
   - **Blast radius first.** Before editing a file, grep the callers/dependents of every
     symbol, route, table or component you are about to change, and write that list into
     `CHAIN.md`. Reading before editing is what stops the rewrite loop — and this exact
     list picks the 3 regression cases at step 5.
   - Run the project's typecheck/lint/tests on the affected area. No output, no acceptance.
   - **Two-strike rule:** if a fix for the same symptom fails twice, **stop editing**.
     `git checkout --` the files back to the last green commit, then invoke
     `superpowers:systematic-debugging`. A third blind attempt on the same hypothesis
     is the loop, and the debris of the first two corrupts the next analysis.
   - Commit per ticket, small.
5. **`code-review`** — one pass over `BASE...HEAD` (three-dot). Hand it `BASE`; it must
   never ask. Three axes: Standards, Spec, **Correctness**.
   - Tick the acceptance-criteria boxes in `.scratch/<feature-slug>/issues/*.md` against
     what actually landed. An unticked box is a Spec finding.
   - **Then verify it runs**, per `${CLAUDE_PLUGIN_ROOT}/rules/runtime-verification.md`: load the
     changed surface in the system Chrome via `npx playwright ... --channel=chrome`,
     plus exactly **3 nearby regression cases** (from the blast-radius list). Console
     must be empty. Full-system sweep only if the user explicitly asked.
   - **High stakes** (money, auth/authorization, migrations, destructive paths, external
     network, PII) → also run `codex-review`. The author is not independent eyes.
6. **`scribe`** — record the run in the project's Obsidian vault. **Runs last, always**,
   even after the cap stopped further fixing. Write the watch-outs honestly: step 0 of
   the *next* run reads them.

## If a chain skill is missing on this machine

The named sub-skills are NOT shipped with this plugin — see the README for where to get
them. If one is missing, say which one, then continue with the inline fallback rather than
pretending the step happened:

| missing | fallback |
|---|---|
| `grill-with-docs` | grill the plan against the codebase yourself, batched and capped as above |
| `to-spec` | write the spec to `.scratch/<slug>/spec.md` yourself, same headings |
| `to-tickets` | list the tickets in `.scratch/<slug>/issues/` yourself, one file each, each naming its blockers |
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
| `grill-with-docs` interview | **Ask** — batched and capped as above |
| `to-spec` "confirm the seams" | **Decide.** Write the seams + why into the spec |
| `to-tickets` "quiz the user, iterate until approved" | **Decide.** List the tickets in the spec and move on |
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
~~~~~

## `skills/fix/SKILL.md`

~~~~~markdown
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
~~~~~

**สังเกต frontmatter:** `disable-model-invocation: true` สำคัญ — แปลว่าเรียกได้ด้วย `/` เท่านั้น
ไม่งั้นโมเดลจะหยิบ chain ทั้งชุดมาใช้เองตอนที่คุณแค่ถามอะไรสั้น ๆ

**สังเกต path ของไฟล์กฎ:** ใช้ `${CLAUDE_PLUGIN_ROOT}/rules/...` ไม่ใช่ `~/.claude/rules/...`
เพราะกฎเดินทางมากับ plugin — ตัดเคสคนลง skill แล้วลืมก๊อป rules (ซึ่งจะพังแบบเงียบ ๆ)

---

# ส่วนที่ 5 — แก้ / fork repo

ทุกอย่างที่สอนในเอกสารนี้อยู่ใน repo เดียว: **github.com/AlxMial/implement-team**

```
.claude-plugin/plugin.json        ← ชื่อ, เวอร์ชัน, คำอธิบาย plugin
.claude-plugin/marketplace.json   ← ทำให้ repo นี้เป็น marketplace ในตัว
skills/feature/SKILL.md           ← hard chain
skills/fix/SKILL.md               ← easy chain
skills/scribe/SKILL.md            ← ตัวบันทึก changelog
rules/frontend-design-rules.md    ← 20 ข้อ ที่ frontend lane gate ทีละข้อ
rules/ux-ui-design-rules.md       ← ฉบับยาว 70 ข้อ
rules/runtime-verification.md     ← กฎเปิด browser จริง: การเปลี่ยน + 3 regression case
README.md  TEACHING.md
```

## แก้แล้วทดสอบก่อน push

clone แล้วลง plugin **จาก path ในเครื่อง** ได้เลย ไม่ต้อง push ขึ้น GitHub ก่อน:

```bash
git clone git@github.com:AlxMial/implement-team.git
cd implement-team
```
```
/plugin marketplace add ./implement-team      # ชี้ไปที่โฟลเดอร์ที่ clone มา
/plugin install implement-team@implement-team
```

แก้ไฟล์ → restart Claude Code → ทดสอบ → พอใจค่อย commit แล้ว push
(ถ้าเคยลงจาก GitHub ไว้ ให้ `/plugin uninstall` ตัวเดิมก่อน กันสองตัวชนกัน)

## fork เป็นของทีมตัวเอง

1. fork หรือ clone แล้วสร้าง repo ใหม่
2. แก้ `.claude-plugin/marketplace.json` — เปลี่ยน `name` และ `owner` เป็นของทีม
3. แก้ `rules/*.md` ให้เป็นมาตรฐาน UI ของทีม (path ใน SKILL.md ชี้เข้า plugin อยู่แล้ว ไม่ต้องแก้)
4. push แล้วบอกทีมว่า `/plugin marketplace add <org>/<repo>`

อยากเปลี่ยนชื่อ chain ให้เปลี่ยน **ชื่อโฟลเดอร์** กับ **`name:` ใน frontmatter** ให้ตรงกัน
(ไม่ตรงกัน = ไม่โผล่ใน `/`)

## ปล่อยเวอร์ชันใหม่

แก้ `version` ใน `plugin.json` ตาม semver แล้ว commit + push
ฝั่งผู้ใช้อัปเดตด้วย `/plugin update implement-team`

## อยากได้เวอร์ชันของตัวเองโดยไม่ fork

เอา `SKILL.md` ทั้งสองไฟล์ในส่วนที่ 4 ให้ Claude แล้วบอกว่าจะเปลี่ยนอะไร
(เช่น ตัดขั้น ticket ออก, เปลี่ยน tracker, เพิ่มขั้น deploy) แล้วให้มันเขียนไฟล์ลง
`~/.claude/skills/<ชื่อ>/SKILL.md` ให้ — ได้ skill ส่วนตัวที่ไม่ผูกกับ plugin นี้

---

# ส่วนที่ 6 — กฎที่ห้ามตัดทิ้ง

ทุกข้อในนี้มีที่มาจากของที่เคยพังจริง ตอนสอนให้เล่าที่มาด้วย ไม่งั้นคนจะตัดทิ้ง

## 1. Review cap — หยุดตอน "ไม่มีของใหม่" ไม่ใช่หยุดตามตัวนับ

chain รุ่นก่อนหน้า (team-chain) ให้ reviewer กับ builder คุยกันจนกว่าจะผ่าน
ผลคือติดลูป 4 รอบ reviewer หาเรื่องใหม่ได้เรื่อย ๆ โดยโค้ดไม่ได้ดีขึ้น เผา token ฟรี
สุดท้ายรื้อทิ้งทั้งระบบ

**ดังนั้น: รอบ 1 เจอของ → แก้ → รอบ 2 ถ้าไม่มีของใหม่ = จบ**
ของเดิมที่โผล่ซ้ำไม่นับว่าใหม่ ถ้ารอบ 2 เจอของใหม่จริง แก้ได้อีกครั้งเดียวแล้วจบ

**แต่ console error / test แดง / build พัง ไม่ใช่ review finding** — cap ไม่คุ้มพวกนี้
แก้จนรันได้ ถ้าจบ chain แล้วยังมี FAIL ค้าง รายงาน**เริ่ม**ด้วยคำว่า FAIL
ห้ามเขียนว่า "เสร็จ" ทับความพังที่รู้อยู่

## 2. `scribe` รันเป็นขั้นสุดท้ายเสมอ

แม้ review cap จะตัดจบแบบยังมีของค้าง ก็ยังต้องบันทึก
ไม่บันทึก = รอบหน้า `grill-with-docs` ไม่มีอะไรให้อ่าน = เริ่มจากศูนย์ทุกครั้ง

## 3. Design direction ต้องเป็นค่าจริง

ก่อนเขียน spec ที่มี UI ต้องเรียก `frontend-design` + `impeccable` แล้วสรุปหัวข้อ
`## Design direction` ที่มี: user, goal, primary action, information hierarchy
แล้วตามด้วย **token จริง** — spacing scale, type scale, colour token, radius scale,
component เดิมที่จะ reuse

คำว่า "ใช้ spacing ให้สม่ำเสมอ" ไม่ใช่ design direction — `8/16/24/32` คือ design direction
และต้องอ่าน token ที่โปรเจกต์มีอยู่ก่อน แล้วอ้างชื่อไฟล์ ห้ามคิด palette ใหม่ขนานกับของเดิม

## 4. Static gate หน้าตา / browser gate ว่ามันรัน

ก่อน `code-review` ให้รัน `impeccable` ทับ UI diff แล้วไล่กฎทีละข้อจาก **markup, style, token**
— contrast กับขนาด target คำนวณจากค่า token จริง ซึ่งแม่นกว่ามองจาก screenshot

ข้อที่พลาดแล้วนับเป็น FAIL ทันที (accessibility floor): ข้อ 8, 9, 10, 17, 19
บวกกับ "ขาด loading / empty / error state"
ส่วนที่ไม่ผิดกฎแต่ไม่ถูกใจ = note ไม่ใช่ FAIL — เรื่องรสนิยมเป็นสิทธิ์ของ user

**แต่ static ตอบไม่ได้ว่าหน้านั้นรันได้จริงไหม** เวอร์ชันก่อนหน้าเขียนว่า "ไม่เปิด
browser" แล้วเสียเวลาไปหนึ่งวัน: `Cannot access 'openConfirmDialog' before
initialization` ฆ่า DOMContentLoaded ทั้งตัว ทุกหน้าค้างที่ skeleton ขณะที่ static
gate 25 ข้อ, test 703 ตัว และ code review สองแกนผ่านหมด — บั๊กซ่อนหลังเงื่อนไข
localStorage เห็นได้จาก browser ที่มี state นั้นเท่านั้น

ดังนั้นตอนนี้: โค้ดที่รันตอนโหลดหน้า ต้องเปิดหน้านั้นจริง 1 ครั้งด้วย
`npx playwright ... --channel=chrome` (Chrome ในเครื่อง ไม่ต้องโหลดอะไร ไม่ต้อง login)
พร้อม state/flag ที่ของใหม่ต้องพึ่ง แล้ว **console ต้องว่าง** ขอบเขตคือ
**ของที่แก้ + 3 regression case** ที่เลือกจากรายชื่อ blast radius (caller/parent,
sibling ที่ใช้ component-store-table เดียวกัน, flow ก่อน/หลัง) — ไม่กวาดทั้งระบบ
ยกเว้น user สั่งเอง

## 5. Self-gate

คนเขียน diff ไม่ใช่คนตรวจ diff ตัวเอง แต่ chain นี้ Claude เป็นทั้งสองอย่าง
ชดเชยด้วยการ **ห้ามข้าม `code-review` เพราะ "ก็เราเขียนเอง"** ให้พึ่ง sub-agent ของ
`code-review` และเข้มกับตัวเองมากขึ้น ไม่ใช่ผ่อนลง

## 6. เรียก skill จริง ๆ อย่าทำจากความจำ

ทุกชื่อที่เขียนเป็น `code` ใน SKILL.md คือ skill ที่ต้อง invoke จริงด้วย Skill tool
ไม่ใช่ "จำได้ว่ามันทำอะไร แล้วทำเอง" — skill มีการอัปเดต และเนื้อในยาวกว่าที่จำไว้เสมอ
ถ้าเรียกไม่ได้ ให้บอก user ตรง ๆ อย่าเงียบแล้วข้าม

## 7. Blast radius ก่อนแก้ + two-strike

ก่อนแก้ไฟล์ ให้ grep caller/dependent ของทุก symbol, route, table, component ที่จะเปลี่ยน
แล้วเขียนรายชื่อลง `CHAIN.md` — อ่านก่อนแก้คือสิ่งที่หยุดลูป "แก้แล้วพังที่อื่น"
และรายชื่อนี้เองที่ใช้เลือก 3 regression case ตอน review

**two-strike:** แก้อาการเดิมพลาด 2 ครั้ง = **หยุดแก้** `git checkout --` ไฟล์กลับไป commit
ที่เขียวล่าสุด แล้วเรียก `superpowers:systematic-debugging` ครั้งที่ 3 แบบเดาคือลูป
และซากของ 2 ครั้งแรกทำให้วิเคราะห์รอบถัดไปเพี้ยน

## 8. ไม่หยุดกลางทาง แต่ต้องเปิดเผยทุกอย่างที่ตัดสินใจเอง

sub-skill หลายตัวเขียนว่า "ถาม user แล้วรอ" เพราะมันถูกเขียนไว้ให้ใช้เดี่ยว ๆ
ใน chain มีแค่ `grill-with-docs` ที่ได้ถามจริง และถามแบบมีเพดาน — **batch ละไม่เกิน 5 ข้อ
พร้อมคำตอบที่แนะนำ สูงสุด 2 batch** ที่เหลือตัดสินใจเองแล้วเขียนลง `## Assumptions`
ใน spec และพูดซ้ำในรายงานสุดท้าย

นั่นคือดีล: ไม่หยุดถามเป็นช่วง ๆ แลกกับเปิดเผยครบ คำถามที่ codebase ตอบได้ไม่ใช่คำถาม
— ไปอ่านโค้ด

---

# ส่วนที่ 7 — ไฟล์กฎ UI (เนื้อเต็ม)

ตัวที่ chain ใช้ gate จริง เดินทางมากับ plugin ที่ `rules/frontend-design-rules.md`

~~~~~markdown
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
~~~~~

`rules/runtime-verification.md` เดินทางมาคู่กัน — เป็นฝั่ง "พิสูจน์ว่ามันรัน":
เปิด Chrome ในเครื่องด้วย Playwright, ขอบเขต = ของที่แก้ + 3 regression case,
console error นับเป็น FAIL น้ำหนักเท่าพลาด accessibility floor และรายงานต้องบอก 3 บรรทัด
(changed surface / regression 3 เคส / console)

ฉบับยาว 70 ข้อ (`ux-ui-design-rules.md`) อยู่ในโฟลเดอร์ `rules/` ครอบคลุม layout system,
typography scale, form, empty/error state, color semantics, dashboard, healthcare,
LINE Mini App, kiosk ฯลฯ ใช้ตอนที่ 20 ข้อตอบไม่ได้

---

# ส่วนที่ 8 — วิธีใช้จริง

```
$ claude
> /fix เพิ่มปุ่ม export CSV ในหน้ารายการลูกค้า
```

สิ่งที่จะเกิด:
1. **Step 0** — ปัก `BASE`, grep `Changelog/` หาโน้ตที่แตะหน้ารายการลูกค้า, TodoWrite,
   เขียน `.scratch/<slug>/CHAIN.md`
2. `to-spec` → ได้ spec + `## Assumptions` (ไม่มี tracker ก็เขียนลง `.scratch/<slug>/spec.md`)
3. งานนี้แตะ UI → เรียก `frontend-design` + `impeccable` ตั้ง `## Design direction` ก่อน
4. `implement` → grep caller ของหน้านั้นลง `CHAIN.md` ก่อนแก้ → เขียน → รัน
   typecheck/lint/test เฉพาะส่วนที่แตะ → commit
5. `impeccable` ไล่ UI diff เทียบกฎ → `code-review` ทับ `BASE...HEAD` → เปิดหน้านั้นใน
   Chrome จริง + 3 regression case, console ต้องว่าง → เจอของก็แก้ แล้วหยุดตอนรอบถัดไป
   ไม่มีของใหม่
6. `scribe` → บันทึกลง vault → รายงานครั้งเดียวตอนท้าย (FAIL ขึ้นก่อนถ้ามีของค้าง)

**ระหว่างทางเราทำอะไรได้บ้าง:** กด Esc แทรกได้ตลอด chain ไม่ได้ล็อกอะไร
มันแค่เป็นลำดับที่ Claude ถืออยู่

**เลือกผิดตัวทำไง:** ถ้า `/fix` ไปเจอว่างานใหญ่กว่าที่คิด มันจะหยุดแล้วเสนอ `/feature`
ส่วน `/feature` ที่เจอว่างานเล็กเกิน จะบอกเฉย ๆ แต่ทำต่อจนจบ — ไม่สลับ chain กลางคัน
เพราะสลับแล้วของที่ทำไปแล้วจะค้าง

---

# ส่วนที่ 9 — ดัดแปลง / FAQ

**`scribe` ต้องแก้ prompt ไหม** → **ไม่ต้อง** chain อ้าง `scribe` ด้วยชื่ออย่างเดียว
ตัวมันหา vault เองจากในโปรเจกต์ (`find . -maxdepth 3 -name .obsidian`) ไม่มี path ฝังไว้
ย้ายเครื่องก็ทำงานได้เลย เขียนโน้ตเดียวที่ `<vault>/Changelog/YYYY-MM-DD-<slug>.md`
ไม่แตะโค้ด ไม่ commit

**ไม่ใช้ Obsidian / โปรเจกต์ไม่มี `.obsidian/`** → พฤติกรรม default คือ **ไม่เขียนอะไรเลย**
แล้วบอก user ว่าไม่เจอ vault (ตั้งใจ — กันไปเขียนมั่วลง vault อื่น)
ให้แก้ `skills/scribe/SKILL.md` ข้อ 1 จาก "หา `.obsidian/`" เป็น `mkdir -p docs/changelog`
แล้วลบการเรียก `claude-obsidian:obsidian-markdown` ในข้อ 3 ทิ้ง — โครงโน้ตที่เหลือใช้ได้เหมือนเดิม
แก้ใน fork ของตัวเองแล้ว `/plugin update`

**ไม่มี issue tracker** → ไม่ต้องทำอะไร มี fallback อยู่แล้ว: `to-spec` เขียนลง
`.scratch/<slug>/spec.md`, `to-tickets` เขียนลง `.scratch/<slug>/issues/`

**`impeccable` หยุดแล้วขอ PRODUCT.md** → รัน `impeccable init` ให้จบก่อน แล้วค่อยเริ่ม chain
อย่าปล่อยให้มันไป init กลางคัน chain

**ทีมมี design system อยู่แล้ว** → fork repo นี้ แล้วแทนที่ `rules/` ด้วยของทีม (path ใน SKILL.md
ชี้เข้า plugin อยู่แล้ว ไม่ต้องแก้)
กฎ accessibility (ข้อ 8, 9, 10, 17, 19) ให้เก็บไว้ — ข้อพวกนี้เป็นพื้น ไม่ใช่รสนิยม

**อยากให้ model อื่นช่วยเขียนโค้ด (worker)** → ทำได้ แต่กฎที่ต้องมีคู่กันเสมอคือ
**งาน UI ห้ามส่งออกไป** worker เห็นแค่ "รายชื่อไฟล์ + ข้อความ" ไม่มีอะไรจับได้ว่าหน้าจอดูผิด
จุดส่งต่องานคือจุดที่คุณภาพ UI ตาย — ticket ผสมให้แตกตามไฟล์ logic ส่งออก UI ทำเอง
และ diff ที่ได้กลับมาต้องอ่านเองและรัน test ก่อนรับทุกครั้ง

**อยากได้ chain ที่ 3** → ได้ แต่เตือนไว้ก่อน: ต้นฉบับเคยมีระบบ 3 ชั้นที่ใหญ่กว่านี้มาก
(team-chain / team-b / team-c) แล้วรื้อทิ้งเพราะมันลูป เพิ่มขั้นตอนไม่ได้แปลว่าได้คุณภาพ
ทุกขั้นที่เพิ่มต้องตอบให้ได้ว่า "ขั้นนี้จับอะไรที่ขั้นอื่นจับไม่ได้"

**เปลี่ยนชื่อ** → เปลี่ยนได้ทั้ง `name:` ใน frontmatter และชื่อโฟลเดอร์ (ต้องตรงกัน)

---

## ส่วนที่ 10 — ตั้งชื่อคำสั่งเอง

`/feature` `/fix` เป็นแค่ชื่อ skill ถ้าไม่ชอบ หรือไปชนกับ plugin อื่น ตั้งชื่อเองได้ ไม่ต้อง fork
ทำเป็นไฟล์เดียว:

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

จากนั้น `/ship <งาน>` = รัน hard chain ส่วน easy chain ก็ทำแบบเดียวกันแต่ชี้ไป `fix`

- อยากให้ชื่อนี้ใช้เฉพาะในโปรเจกต์ → วางไฟล์ไว้ที่ `<project>/.claude/commands/` แล้ว commit
  ทั้งทีมจะได้ชื่อเดียวกัน
- อยากเปลี่ยนชื่อถาวรทั้งทีม → fork repo นี้ แล้วเปลี่ยน **ชื่อโฟลเดอร์ skill** กับ
  **`name:` ใน frontmatter** ให้ตรงกัน (ต้องตรงกันทั้งคู่ ไม่งั้นไม่โผล่)

ข้อดีของวิธี alias คือชื่อเดิมยังอยู่ — เอกสารกับคนที่คุ้นชื่อเดิมยังใช้ต่อได้

---

# สรุปสำหรับคนสอน

ถ้ามีเวลา 5 นาที พูดแค่ 3 ประโยคนี้พอ:

1. มันคือลำดับการเรียก skill ที่มีอยู่แล้ว ไม่ใช่ของใหม่ — ไฟล์ markdown ไฟล์ละ 60 บรรทัด
2. มีสองขนาดเพราะถ้ามีขนาดเดียวคนจะเลิกใช้ตอนงานเล็ก
3. หัวใจคือ **review cap**, **scribe** และ **step 0** — ตัวแรกกันลูป ตัวที่สองเขียนความจำ
   ตัวที่สามคือตอนที่เอาความจำนั้นมาใช้จริง
