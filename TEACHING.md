# jinshi / maomao — เอกสารสอนฉบับเต็ม

> อ่านไฟล์เดียวจบ สอนได้เลย ไม่ต้องเปิดไฟล์อื่นประกอบ
> เวอร์ชันนี้คือ **Claude เขียนโค้ดเองล้วน** ไม่ต้องมี external worker หรือ CLI อะไรเพิ่ม
> ติดตั้งเป็น plugin: `/plugin marketplace add AlxMial/jinshi-maomao`

---

# ส่วนที่ 1 — มันคืออะไร

`/jinshi` และ `/maomao` คือ **slash command สองตัวที่สั่งให้ Claude เรียก skill ที่มีอยู่แล้ว
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

| | `/jinshi` (ยาก) | `/maomao` (ง่าย) |
|---|---|---|
| ใช้กับ | feature ใหม่, แก้ข้ามระบบ, requirement ยังไม่ชัด | งานเล็ก เข้าใจตรงกันแล้ว |
| ลำดับ | grill-with-docs → to-spec → to-tickets → implement → code-review → scribe | to-spec → implement → code-review → scribe |
| ต่างกันตรง | มีขั้น "ซัก" และ "แตก ticket" | ตัดสองขั้นนั้นทิ้ง |

ชื่อมาจากตัวละครใน *ยาผีบอกเภสัชกรสาว* (jinshi = คนที่คิดเยอะ, maomao = คนที่ลงมือ)
ตั้งชื่ออะไรก็ได้ แต่อย่าตั้งเป็น `/hard` `/easy` — เวลาพิมพ์จะไปชนกับ skill อื่น

## ขั้นแต่ละขั้นทำอะไร

| skill | ทำอะไร | ทำไมต้องมี |
|---|---|---|
| `grill-with-docs` | ซัก plan กับ codebase และ doc ที่มีอยู่ ก่อนเขียนอะไรลงไป | จับ requirement ที่ขัดกับของเดิมตั้งแต่ก่อนเสียเวลาเขียน |
| `to-spec` | สรุปบทสนทนาเป็น spec (ไม่สัมภาษณ์ซ้ำ) | มีที่อ้างอิงว่า "ตกลงกันว่าอะไร" ตอน review |
| `to-tickets` | แตก spec เป็น ticket เล็ก ๆ พร้อมบอกว่าใบไหนบล็อกใบไหน | งานใหญ่ commit เดียวคือ review ไม่ได้ |
| `implement` | ลงมือทำทีละ ticket | — |
| `code-review` | review diff ทั้งก้อน (มี sub-agent แยก Standards / Spec ในตัว) | คนเขียนไม่ควรเป็นคนตรวจคนเดียว |
| `scribe` | บันทึกลง Obsidian vault ของโปรเจกต์ (เขียนเอง — ส่วนที่ 8 ถ้าไม่ใช้ Obsidian) | รอบหน้า `grill-with-docs` จะได้อ่านว่ารอบก่อนทำอะไรไว้ |
| `ponytail` | กันงาน over-engineer ระหว่างเขียนโค้ด | chain มีขั้นตอนเยอะ = โมเดลมีแนวโน้มสร้างของเกินจำเป็น |
| `frontend-design` | ทิศทางงานออกแบบ เรียกก่อนเขียน spec ที่มี UI | กันหน้าจอหน้าตา "AI generic" |
| `impeccable` | เครื่องมือ UI หลักของ chain — ดูหัวข้อถัดไป | เป็นตัวที่ทำให้ UI ถูก gate จริง ไม่ใช่แค่เขียนในกฎ |

---

# ส่วนที่ 2 — `impeccable` คืออะไร (ตัวที่คนมักงง)

`impeccable` เป็น skill ภายนอก (Apache 2.0, ลงได้ด้วย `npx impeccable`) ที่ทำเรื่อง
frontend craft โดยเฉพาะ — ไม่ใช่ของที่เขียนขึ้นมาเองเหมือน `scribe`
chain ใช้มัน **สองจุด** และเป็นสองจุดที่ต่างกันคนละเรื่อง

| จุดที่เรียก | เรียกทำไม |
|---|---|
| **ก่อนเขียน spec** (ตอนงานแตะ UI) | ให้มันช่วยตั้ง `## Design direction` — โครงหน้าจอ, hierarchy, token ที่จะใช้ |
| **ก่อน `code-review`** | ให้มันไล่ตรวจ UI diff เทียบกฎ ทีละข้อ แบบอ่านโค้ด ไม่เปิด browser |

มันมี sub-command ในตัว สั่งเจาะจงได้ เช่น
`impeccable shape <target>` (วางโครง), `impeccable craft <target>` (ลงมือทำ),
`impeccable audit <target>` (ตรวจ), `impeccable critique <target>` (วิจารณ์),
`impeccable polish` / `harden` / `clarify` / `typeset` / `layout`
— ในบริบท chain: **`shape` ตอนต้น, `audit` ตอนท้าย** คือคู่ที่ใช้บ่อยที่สุด

**ข้อควรระวัง 2 ข้อที่เจอตอนติดตั้งใหม่:**

1. **มันขอ `PRODUCT.md` ในโปรเจกต์** ถ้าไม่มี มันจะหยุดแล้วพาไป flow `init` ก่อน
   ซึ่งจะทำให้ chain สะดุดกลางคัน — โปรเจกต์ใหม่ให้รัน `impeccable init` แยกให้จบก่อน
   แล้วค่อยเริ่ม `/jinshi`
2. **path ใน SKILL.md ของมันเขียนแบบ project-local** (`node .claude/skills/impeccable/...`)
   แต่เราลงไว้ที่ `~/.claude/skills/` ถ้า script รันไม่ผ่าน ให้ Claude ใช้ path เต็ม
   `~/.claude/skills/impeccable/scripts/context.mjs` แทน

**ไม่มี `impeccable` แล้วใช้ chain ได้ไหม** — ได้ แต่ Frontend lane จะเหลือแค่
`frontend-design` + ไล่กฎเอง ซึ่งหลวมกว่าเยอะ ถ้าทีมทำ UI เป็นหลัก แนะนำให้ลง

---

# ส่วนที่ 3 — ติดตั้ง

**1. ลง plugin (2 คำสั่ง)**

```
/plugin marketplace add AlxMial/jinshi-maomao
/plugin install jinshi-maomao@jinshi-maomao
```

restart Claude Code แล้วพิมพ์ `/jinshi` หรือ `/maomao` ได้เลย
ได้มาทั้ง chain สองตัว + `scribe` + ไฟล์กฎ UI (อยู่ในตัว plugin ไม่ต้องก๊อปแยก)
อัปเดตภายหลังด้วย `/plugin update jinshi-maomao`

**2. ลง skill ที่ chain เรียกใช้ (ของคนอื่น ไม่ได้แถมมาใน plugin)**

| skill | ลงยังไง |
|---|---|
| `code-review`, `frontend-design` | official marketplace ของ Claude — `/plugin` |
| `impeccable` | `npx impeccable` |
| `ponytail` | marketplace ของมันเอง — `/plugin install ponytail` |
| `grill-with-docs`, `to-spec`, `to-tickets`, `implement` | ชุด skill ของ Matt Pocock (`/setup-matt-pocock-skills`) |
| `claude-obsidian` | เฉพาะคนใช้ Obsidian — `scribe` เรียกเพื่อให้ syntax ถูก |

**ขาดบางตัวแล้วใช้ได้ไหม** — ได้ chain จะบอกว่าขาดตัวไหน แล้วทำ fallback แทน
(มีตารางอยู่ในตัว `SKILL.md` ส่วนที่ 4) ไม่ใช่เงียบแล้วข้ามขั้น
แต่ยิ่งขาดมาก คุณภาพยิ่งตก โดยเฉพาะ `code-review` กับ `impeccable`

**เช็กก่อนไปต่อ** — พิมพ์ `/` ต้องเห็น `jinshi` กับ `maomao` ถ้าไม่เห็นแปลว่ายังไม่ได้ restart

---

# ส่วนที่ 4 — ตัว skill (เนื้อเต็ม)

ลง plugin แล้วได้สองไฟล์นี้อัตโนมัติ ที่แปะไว้เพื่ออ่านตอนสอนโดยไม่ต้องเปิดไฟล์

## `skills/jinshi/SKILL.md`

~~~~~markdown
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
~~~~~

## `skills/maomao/SKILL.md`

~~~~~markdown
---
name: maomao
description: "Easy-task chain for small, well-understood work: spec it, implement, review, record. Skips grilling and ticket breakdown. Global — works in any project."
disable-model-invocation: true
---

# maomao (easy chain)

> **UI work? Read the Frontend lane section below before step 1.** It is binding for
> any step that lays out controls or builds a screen.

Run these skills **in order**, in this same session. Invoke each one with the Skill tool —
do not work from memory of what it does. If a skill is missing on this machine, say so
and stop — do not silently skip it.

1. **`to-spec`** — synthesize a spec from the current conversation (no interview).
   - No-tracker fallback: if the project has no `docs/agents/issue-tracker.md`,
     write the spec to `.scratch/<feature-slug>/spec.md` instead of publishing.
2. **`implement`** — build it:
   - Invoke **`ponytail`** before the first line of code and keep it in force: no
     abstraction, boilerplate, config or dependency nobody asked for.
   - Read the spec, then explore the codebase for the concrete file list it touches —
     `to-spec` deliberately omits paths.
   - **Re-read your own diff, then run the project's typecheck/lint/tests on the
     affected area.** No output, no acceptance.
   - Commit, same as `implement` normally does.
3. **`code-review`** — one pass over the diff since the spec/feature start. Never skip it
   because you wrote the diff yourself; lean on its sub-agents and be harder on yourself,
   not softer.
4. **`scribe`** — record the run (goal, spec, what changed, review outcome). Runs last,
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

**Escalate, don't force it:** if partway through this turns out to need grilling or a
ticket breakdown after all, stop and suggest `/jinshi` instead of pushing it through the
easy chain.
~~~~~

**สังเกต frontmatter:** `disable-model-invocation: true` สำคัญ — แปลว่าเรียกได้ด้วย `/` เท่านั้น
ไม่งั้นโมเดลจะหยิบ chain ทั้งชุดมาใช้เองตอนที่คุณแค่ถามอะไรสั้น ๆ

**สังเกต path ของไฟล์กฎ:** ใช้ `${CLAUDE_PLUGIN_ROOT}/rules/...` ไม่ใช่ `~/.claude/rules/...`
เพราะกฎเดินทางมากับ plugin — ตัดเคสคนลง skill แล้วลืมก๊อป rules (ซึ่งจะพังแบบเงียบ ๆ)

---

# ส่วนที่ 5 — prompt สำหรับให้ Claude สร้างเอง (ทางเลือก)

ถ้าอยากได้ chain เวอร์ชันของตัวเองแทนการลง plugin — เช่น จะเปลี่ยนชื่อ หรือปรับให้เข้ากับ
skill ชุดอื่น — เอาข้อความข้างล่างนี้ไปวางใน Claude Code แล้วมันจะสร้าง `SKILL.md` ให้ใหม่
ข้อดีคือมันจะไล่เช็กว่า skill ที่อ้างถึงมีจริงในเครื่องนั้นไหม และปรับ path ให้ตรงกับของจริง

~~~~~markdown
# Prompt: สร้าง skill /jinshi และ /maomao (เวอร์ชัน Claude เขียนเอง ไม่มี external worker)

> เอาข้อความใต้เส้น `---` ไปวางใน Claude Code ของเครื่องตัวเอง (พิมพ์ทีเดียวจบ)

## ต้องมีก่อน (prerequisites)
- skills ที่ chain เรียกใช้: `grill-with-docs`, `to-spec`, `to-tickets`, `implement`,
  `code-review`, `scribe`
- `ponytail` (plugin) — กันงาน over-engineer ระหว่าง implement
- `frontend-design` + `impeccable` — ต้องมีถ้างานแตะ UI
- ไฟล์กฎ UI: `~/.claude/rules/frontend-design-rules.md`
  (ไม่มีก็ได้ — แต่ต้องแก้ Frontend lane ให้ชี้ไปที่กฎที่ตัวเองใช้จริง)

---

สร้าง global skill 2 ตัวใน `~/.claude/skills/` ให้หน่อย ตัวละ 1 ไฟล์ `SKILL.md`
ไม่ต้องมีไฟล์อื่น ทั้งคู่ใส่ frontmatter `name`, `description`, และ
`disable-model-invocation: true` (ให้เรียกด้วย `/` เท่านั้น ไม่ให้โมเดลเรียกเอง)

**แนวคิด:** ทั้งสองตัวไม่ใช่ agent ใหม่ — มันแค่ "ลำดับการเรียก skill ที่มีอยู่แล้ว"
ให้รันตามลำดับใน session เดียวกัน ไม่ต้องสร้าง subagent เพิ่ม
(`code-review` มี sub-agent ของมันเองอยู่แล้ว พอ)

## 1. `jinshi` — hard chain
description: งานหลายขั้น/feature ใหม่/แก้ข้ามระบบ/requirement ยังไม่ชัด
ลำดับ: `grill-with-docs` → `to-spec` → `to-tickets` → `implement` → `code-review` → `scribe`

## 2. `maomao` — easy chain
description: งานเล็ก เข้าใจตรงกันแล้ว ข้ามการ grill และการแตก ticket
ลำดับ: `to-spec` → `implement` → `code-review` → `scribe`
ท้ายไฟล์เพิ่มข้อ **Escalate, don't force it**: ถ้าทำไปแล้วพบว่าต้อง grill หรือแตก ticket จริง ๆ
ให้หยุดแล้วเสนอ `/jinshi` แทน อย่าดันต่อ

## กฎที่ต้องมีเหมือนกันทั้งสองไฟล์

**เรียก skill พวกนี้จริง ๆ อย่าทำจากความจำ** — ทุกชื่อที่เป็น `code` ในไฟล์นี้คือ skill
ที่ต้อง invoke ด้วย Skill tool จริง ๆ ไม่ใช่ "นึกว่าจำได้แล้วทำเอง" skill มีการอัปเดต
และเนื้อหาข้างในยาวกว่าที่จำไว้เสมอ ถ้าเรียกไม่ได้/ไม่มีในเครื่อง ให้บอก user ตรง ๆ
อย่าเงียบแล้วข้าม

**`ponytail` ก่อนลงมือ implement ทุกครั้ง** — เรียกก่อนเขียนโค้ดบรรทัดแรก ให้มันคุม
ไม่ให้ chain นี้ผลิต abstraction/boilerplate/dependency ที่ไม่มีใครขอ
(ใครอยากให้ติดตลอด session ไม่ต้องเรียกซ้ำ ไปตั้งเป็น SessionStart hook แทนได้)

**No-tracker fallback** — ถ้าโปรเจกต์ไม่มี `docs/agents/issue-tracker.md`
ให้ `to-spec` เขียนลง `.scratch/<feature-slug>/spec.md` และ `to-tickets` เขียนลง
`.scratch/<feature-slug>/issues/` แทนการ publish ขึ้น tracker

**Implement — Claude เขียนเองทั้งหมด**
- `jinshi`: ไล่ทำ ticket ที่ blocker เคลียร์หมดแล้ว (frontier) ไปเรื่อย ๆ จนครบทุกใบ
- ก่อนลงมือแต่ละ ticket/spec: อ่านมันก่อน แล้วไล่ codebase หา "รายชื่อไฟล์จริง" ที่จะแตะ
  — `to-spec`/`to-tickets` ตั้งใจไม่ใส่ path มาให้
- ทำทีละ ticket อย่ารวบหลายใบใน commit เดียว
- **หลังแก้เสร็จ: อ่าน diff ของตัวเองซ้ำ และรัน typecheck/lint/test ของโปรเจกต์
  เฉพาะส่วนที่แตะ** — ไม่มี output ไม่ถือว่าเสร็จ
- แล้วค่อย commit ตามที่ `implement` ทำปกติ

**Frontend lane (binding เมื่องานแตะ UI)** — ใส่เป็นหัวข้อของตัวเอง และใส่บรรทัดเตือน
ไว้บนสุดของไฟล์ว่า "UI work? อ่าน Frontend lane ก่อนเริ่ม step 1"
- trigger: ทุก step ที่วาง layout, สร้าง/แก้หน้าจอ, restyle UI เดิม (web, mobile,
  LINE Mini App, kiosk, dashboard) — ไม่แน่ใจ = ถือว่า trigger
- **ก่อนเขียน spec:** เรียก `frontend-design` และ `impeccable` แล้วสรุปหัวข้อ
  `## Design direction` ลงใน spec: user, goal, primary action, information hierarchy
  ตามด้วย token จริง — spacing scale (8px), type scale, colour token, radius scale,
  component เดิมที่จะ reuse อ่าน token ที่โปรเจกต์มีอยู่ก่อนแล้วอ้างชื่อไฟล์
  ห้ามคิด palette ใหม่ขนานกัน — คำว่า "spacing สม่ำเสมอ" ไม่ใช่ design direction ต้องเป็นค่าจริง
- **ก่อน code-review:** รัน `impeccable` ทับ UI diff แล้ว gate กับ
  `~/.claude/rules/frontend-design-rules.md` ทีละข้อ แบบ static (อ่าน markup, style, token
  ไม่เปิด browser ไม่ screenshot) — พลาด accessibility floor (ข้อ 8, 9, 10, 17, 19)
  หรือขาด loading/empty/error state = FAIL ในตัวมันเอง ต้องกลับไปแก้
  ถ้าไม่ผิดกฎแต่ไม่ถูกใจ = note ไม่ใช่ FAIL เรื่องรสนิยมเป็นสิทธิ์ user

**Review cap (สำคัญที่สุด)** — ถ้า `code-review` เจออะไร ให้แก้ **1 รอบ** แล้ว review
ซ้ำ **1 รอบ** จบแค่นั้น ไม่ว่าผลจะออกมายังไง ห้ามวนต่อ แล้วรายงานผลให้ user
(ข้อนี้มีเพราะ chain รุ่นก่อนเคยติดลูป reviewer ↔ builder 4 รอบโดยไม่ดีขึ้นเลย)

**Self-gate** — chain นี้ Claude เป็นทั้งคนเขียนและคนตรวจ ซึ่งเป็นจุดอ่อนที่ต้องชดเชย:
`code-review` ต้องรันเสมอ ห้ามข้ามเพราะ "ก็เราเขียนเอง" ให้พึ่ง sub-agent ของ
`code-review` และเข้มกับตัวเองมากขึ้น ไม่ใช่ผ่อนลง

**`scribe` รันเป็นขั้นสุดท้ายเสมอ** แม้ review cap จะตัดจบไปแล้ว — ยังมีเรื่องให้บันทึกอยู่ดี

เขียนไฟล์ให้กระชับ เป็น bullet ไม่ต้องอธิบายยาว
~~~~~

---

# ส่วนที่ 6 — กฎที่ห้ามตัดทิ้ง

ทุกข้อในนี้มีที่มาจากของที่เคยพังจริง ตอนสอนให้เล่าที่มาด้วย ไม่งั้นคนจะตัดทิ้ง

## 1. Review cap — แก้ 1 รอบ + review ซ้ำ 1 รอบ แล้วจบ

chain รุ่นก่อนหน้า (team-chain) ให้ reviewer กับ builder คุยกันจนกว่าจะผ่าน
ผลคือติดลูป 4 รอบ reviewer หาเรื่องใหม่ได้เรื่อย ๆ โดยโค้ดไม่ได้ดีขึ้น เผา token ฟรี
สุดท้ายรื้อทิ้งทั้งระบบ

**ดังนั้น: review เจอปัญหา → แก้ 1 รอบ → review ซ้ำ 1 รอบ → หยุด ไม่ว่าผลจะออกมายังไง**
แล้วรายงาน user ให้ตัดสินใจเอง คนเป็นคนเบรก ไม่ใช่โมเดล

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

## 4. Gate UI แบบ static เท่านั้น

ก่อน `code-review` ให้รัน `impeccable` ทับ UI diff แล้วไล่กฎทีละข้อจาก **markup, style, token**
— ไม่เปิด browser ไม่ screenshot คนเป็นคนดูหน้าจอจริง เครื่องตรวจแค่สิ่งที่อ่านได้จากโค้ด

ข้อที่พลาดแล้วนับเป็น FAIL ทันที (accessibility floor): ข้อ 8, 9, 10, 17, 19
บวกกับ "ขาด loading / empty / error state"
ส่วนที่ไม่ผิดกฎแต่ไม่ถูกใจ = note ไม่ใช่ FAIL — เรื่องรสนิยมเป็นสิทธิ์ของ user

## 5. Self-gate

คนเขียน diff ไม่ใช่คนตรวจ diff ตัวเอง แต่ chain นี้ Claude เป็นทั้งสองอย่าง
ชดเชยด้วยการ **ห้ามข้าม `code-review` เพราะ "ก็เราเขียนเอง"** ให้พึ่ง sub-agent ของ
`code-review` และเข้มกับตัวเองมากขึ้น ไม่ใช่ผ่อนลง

## 6. เรียก skill จริง ๆ อย่าทำจากความจำ

ทุกชื่อที่เขียนเป็น `code` ใน SKILL.md คือ skill ที่ต้อง invoke จริงด้วย Skill tool
ไม่ใช่ "จำได้ว่ามันทำอะไร แล้วทำเอง" — skill มีการอัปเดต และเนื้อในยาวกว่าที่จำไว้เสมอ
ถ้าเรียกไม่ได้ ให้บอก user ตรง ๆ อย่าเงียบแล้วข้าม

---

# ส่วนที่ 7 — ไฟล์กฎ UI (เนื้อเต็ม)

ตัวที่ chain ใช้ gate จริง เดินทางมากับ plugin ที่ `rules/frontend-design-rules.md`

~~~~~markdown
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
~~~~~

ฉบับยาว 70 ข้อ (`ux-ui-design-rules.md`) อยู่ในโฟลเดอร์ `rules/` ครอบคลุม layout system,
typography scale, form, empty/error state, color semantics, dashboard, healthcare,
LINE Mini App, kiosk ฯลฯ ใช้ตอนที่ 20 ข้อตอบไม่ได้

---

# ส่วนที่ 8 — วิธีใช้จริง

```
$ claude
> /maomao เพิ่มปุ่ม export CSV ในหน้ารายการลูกค้า
```

สิ่งที่จะเกิด:
1. Claude เรียก `to-spec` → ได้ spec (ไม่มี tracker ก็เขียนลง `.scratch/<slug>/spec.md`)
2. งานนี้แตะ UI → เรียก `frontend-design` + `impeccable` ตั้ง `## Design direction` ก่อน
3. เรียก `ponytail` → แล้วลงมือเขียน
4. อ่าน diff ตัวเอง + รัน typecheck/lint/test เฉพาะส่วนที่แตะ → commit
5. `impeccable` ไล่ UI diff เทียบกฎ → `code-review` → เจอปัญหาแก้ 1 รอบ review ซ้ำ 1 รอบ หยุด
6. เรียก `scribe` → บันทึกลง vault → รายงานผล

**ระหว่างทางเราทำอะไรได้บ้าง:** กด Esc แทรกได้ตลอด chain ไม่ได้ล็อกอะไร
มันแค่เป็นลำดับที่ Claude ถืออยู่

**เลือกผิดตัวทำไง:** ถ้า `/maomao` ไปเจอว่างานใหญ่กว่าที่คิด มันจะหยุดแล้วเสนอ `/jinshi`
ส่วน `/jinshi` ที่เจอว่างานเล็กเกิน จะบอกเฉย ๆ แต่ทำต่อจนจบ — ไม่สลับ chain กลางคัน
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

# สรุปสำหรับคนสอน

ถ้ามีเวลา 5 นาที พูดแค่ 3 ประโยคนี้พอ:

1. มันคือลำดับการเรียก skill ที่มีอยู่แล้ว ไม่ใช่ของใหม่ — ไฟล์ markdown ไฟล์ละ 60 บรรทัด
2. มีสองขนาดเพราะถ้ามีขนาดเดียวคนจะเลิกใช้ตอนงานเล็ก
3. หัวใจคือ **review cap** กับ **scribe** — ตัวแรกกันลูป ตัวหลังทำให้รอบหน้าไม่เริ่มจากศูนย์
