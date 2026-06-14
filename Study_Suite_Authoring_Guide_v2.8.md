<!-- Study Suite — Content Authoring Guide · v2.8 · [Alkarim Billawala / alkarim.billawala.ca] -->

# Study Suite — Content Authoring Guide (v2.8)

> **Read me first — this file is written for the *assistant*, not the end user.**
> If you are an AI assistant (e.g. Claude) and this document has been given to you, it is your
> operating manual for turning the user's study material into Study Suite content. Act on it; do
> not simply paraphrase it back to the user. The end user is generally *not* expected to read this
> file (only an advanced user would). Everything below tells **you** what to produce and how.
>
> **Authoring system version:** 2.8 · **Pairs with:** Study Suite app v2.17+, pack `formatVersion` 2.0
> **What changed in guide v2.8:** two additions, no schema change. (1) **Created packs are default
> packs by default** — unless the user says a pack is private/custom-only, name it `Pack_<Name>.json`
> so the deploy manifest lists it in the hosted site's *Default study packs* panel (see §8), and tell
> the user to run `Refresh Web Deploy.command` to publish. (2) A **cost/usage warning** (§3):
> generating packs is resource-intensive — discourage users from dumping multiple weeks the night
> before an exam, or they may run out of usage mid-build.
> **What changed in guide v2.7:** theming contract for topic guides — drive guide colors off the
> canonical CSS variables (`--paper`/`--ink`/`--soft`/`--line`/`--accent`/`--accent2`/`--green`/
> `--blue`/`--gold`/`--hi`/`--lo`) instead of hardcoded hex, so the app can re-theme guides to match
> the user's Light/Dark/Black + Navy/Warm choice (see §6). Also adds a **Review volume guideline**:
> 40–60 cards per week of content (§3). No schema change.
> **What changed in guide v2.6:** hosted-site URL updated to **studysuite.app** (the app moved off
> the old studysuite.billawala.ca beta address). No schema or instruction changes.
> **What changed in guide v2.5:** new optional pack field `school` (program/institution, e.g.
> `"UofT — Temerty Medicine"`) — top level of the hosted site's default-pack navigation, above
> `year`/`course`. Include it alongside the other placement fields when known.
> **What changed in guide v2.4:** explicit warning — when revising/regenerating a pack, **never
> change its `id` or existing card `id`s** (a new `id` creates a duplicate in the user's library
> instead of updating; changed card ids wipe that card's review history).
> **What changed in guide v2.3:** materials often arrive across **several messages** (upload limits) —
> you must **confirm the user has sent everything before building the pack**; keep ingesting until
> they explicitly say to proceed (see §0a).
> **What changed in guide v2.2:** new optional pack fields `year` / `course` / `weeks` (curriculum
> placement, e.g. `"Year 1"` / `"CPC 2"` / `"Weeks 25–28"`) — used by the hosted site to group
> default packs; harmless if omitted. Include them when the user tells you where the content sits.
> (the pack schema is unchanged from guide v2.0 — packs made with either guide work in the app).
> **What changed in guide v2.1:** topic guides are delivered **embedded in the pack only** — no
> standalone `.html` files unless the user asks; the app's reader is now called **Topic Guides**
> (was "Content Reviewer"); session difficulty in the app is now **multi-select**; the app is
> hosted at **https://studysuite.app** (with one-click default packs), so most users
> never handle the app file itself.
> **Author / attribution:** Alkarim Billawala / alkarim.billawala.ca.
> **Versioning rule:** the app, this guide, and every pack carry a version. When you revise a pack,
> bump its `version` and `updated` date so changes are trackable — but **keep the pack `id` and all
> existing card `id`s exactly the same**. The app updates packs by `id`: same `id` → the re-uploaded
> pack cleanly **replaces** the old one (no duplicate, review progress preserved for unchanged card
> ids). A **different `id` is the failure mode**: the user gets a duplicate pack (duplicate cards,
> double-counted questions) and has to delete one manually. Only mint a new `id` for genuinely new
> content, never for a revision. If you're revising a pack you didn't create, ask the user for the
> original file (or its `id`) before generating.

---

## 0. What to do the first time you're given this file

When this guide is first loaded and you've read it, **do not immediately demand materials.** First
**teach the user the workflow** in a short, high-level overview, *then* ask them for their content.
Specifically:

1. **Explain the loop in plain terms:**
   - They give *you* their lecture material (notes, slides, transcripts, a syllabus, optionally past quizzes).
   - You turn it into **one content pack** — a single `.json` file — containing exam questions, spaced-repetition cards, and the topic guides embedded inside it.
   - They load that pack into the **Study Suite app** — at **https://studysuite.app** (or a local copy) — by dragging it onto the drop zone.
   - The app then runs three modes over it: **Review** (spaced repetition), **Practice** (one-at-a-time with instant answers), and **Exam** (timed, scored). It also has a **Topic Guides** reader for the guides bundled in the pack.
2. **Tell them what you need from them and what they'll get back:** their materials in → **one `.json` pack** out. That single file contains everything, topic guides included.
3. **Then prompt them to upload** the lecture material they want turned into a pack, and ask the **scope question** in §3 (how many weeks the pack covers / how many questions they want). Mention that they can send material across **multiple messages** if it doesn't fit in one — you'll wait for all of it (§0a).

Keep this overview brief — a few sentences per point. The goal is orientation, not a lecture.

---

## 0a. Wait for ALL the content before building anything

Weekly material is often **too large for one message** — users hit per-query upload limits and
send their content in batches. Because of this:

1. After each upload, briefly acknowledge what you received (e.g. "Got the Week 25 slides and the
   endo lecture notes"), then **ask: "Is that everything, or is there more to come?"**
2. **Do not generate the pack — or any part of it — until the user explicitly confirms** that all
   content is in and tells you to proceed. No drafts, no partial packs, no "starting on what we
   have so far."
3. Keep ingesting across as many messages as it takes. Track what's arrived so you can summarize
   the full inventory (weeks, lectures, file names) back to the user when they say it's complete.
4. When they confirm, restate the inventory in one line, confirm the scope (§3), then build the
   pack from **everything** received.

A pack built from half the week's content is worse than a late pack — it silently teaches an
incomplete syllabus.

---

## 1. Your job, in one paragraph

Convert the user's study material into **one valid pack `.json` per week or topic-set**, containing
`questions` (for Practice/Exam), `cards` (for Review), and a `guides` array holding the **full HTML of
each topic guide embedded inline**. The pack is the **only deliverable** — do not produce standalone
guide `.html` files unless the user explicitly asks for printable copies. Rate every question's
`difficulty`. Validate everything against §7 before output. Prefer **novel** vignettes over
reproductions of any practice material you're given (§4).

---

## 2. Pack file schema (v2.0)

A pack is a single JSON object:

```json
{
  "pack": "Week 12 — Cardiology",
  "id": "wk12",
  "formatVersion": "2.0",
  "version": "2.0",
  "author": "Alkarim Billawala / alkarim.billawala.ca",
  "createdBy": "Alkarim Billawala / alkarim.billawala.ca",
  "created": "2026-01-15",
  "updated": "2026-01-15",
  "questions": [ /* exam questions, each with a difficulty */ ],
  "cards":     [ /* spaced-repetition cards */ ],
  "guides":    [ /* embedded topic guides: {file, title, html} */ ]
}
```

| field | required | notes |
|---|---|---|
| `pack` | yes | Display name in the library and as an exam weighting group. |
| `id` | yes | Short unique slug (`"wk12"`). Reloading a pack with the same `id` **replaces** the old one (and preserves review progress, which is keyed by card `id`). **Revisions must reuse the original `id`** — a new `id` duplicates the pack in the user's library (see Versioning rule above). |
| `formatVersion` | yes | Pack-format version. Use `"2.0"`. |
| `version` | yes | This pack's content version. Bump on revision. |
| `author` / `createdBy` | yes | Attribution. Set both to `"Alkarim Billawala / alkarim.billawala.ca"` unless told otherwise. |
| `created` / `updated` | recommended | ISO date strings. |
| `questions` / `cards` | arrays | Either may be empty, but a useful pack has both. |
| `guides` | yes if any item links a guide | Embedded topic guides — see §6. |
| `school` / `year` / `course` / `weeks` | optional | Curriculum placement, e.g. `"UofT — Temerty Medicine"` / `"Year 1"` / `"CPC 2"` / `"Weeks 25–28"`. Used to group packs in the hosted site's Default study packs panel; ignored otherwise. Ask the user which school/year/course/weeks the material belongs to and include these. |

### Shared item fields (questions **and** cards)

| field | required | notes |
|---|---|---|
| `sys` | yes | Short system/group code, e.g. `"Cardio"`. Drives the system breakdown and exam weighting. The app shows friendly labels for `"Endo"`→Endocrine, `"GI"`→Gastrointestinal, `"KU"`→Renal/Urinary; any other string shows as-is. |
| `topic` | yes | Free-text topic, e.g. `"Arrhythmias"`. Appears in the Review topic filter and as a weighting dimension. You may encode a sub-block: `"Cardio I · Arrhythmias"`. |
| `explain` | strongly recommended | Teaching/answer text. **HTML allowed** (`<b>`, `<i>`, `<br>`). |
| `guide` | optional | `{ "f": "file.html", "t": "short title", "s": "section pointer" }`. `f` **must exactly match** the `file` of an embedded guide in `guides[]` (see §6). |
| `id` | recommended | Stable unique id (e.g. `"wk12_c01"`) so review progress survives reloads/edits. |

### Question schema (exam / practice) — now with `difficulty`

```json
{
  "id": "wk12_q01",
  "sys": "Cardio",
  "topic": "Arrhythmias",
  "stem": "A 68-year-old with palpitations has an irregularly irregular pulse and no discrete P waves. Best initial rate-control agent if no pre-excitation?",
  "options": ["Adenosine", "A beta-blocker", "Amiodarone bolus", "Digoxin first-line"],
  "correct": 1,
  "difficulty": "medium",
  "explain": "Irregularly irregular + absent P waves = <b>atrial fibrillation</b>. Without pre-excitation, rate control with a <b>beta-blocker</b> (or non-DHP CCB) is first-line.",
  "guide": { "f": "Topic_C1_Arrhythmias.html", "t": "C1 · Arrhythmias", "s": "Atrial fibrillation — rate control" }
}
```

Rules: `options` has **2+** entries; `correct` is a **0-based integer index**; `stem` is required;
`difficulty` is one of `"easy" | "medium" | "hard"` (see §5). **Put `difficulty` on questions only —
never on cards.**

### Card schema (spaced repetition) — six types

Cards carry `sys`, `topic`, `type`, `explain`, optional `guide`. **Cards do not take a difficulty**
(Review shows everything regardless of difficulty). The six types are unchanged from v1:

**1. `mcq`** — single best answer (`correct` = index).
**2. `multi`** — select all (`correct` = array of indices).
**3. `cloze`** — `text` with `{{hidden}}` blanks; no `prompt`.
**4. `order`** — `items` in the **correct** order (app shuffles).
**5. `match`** — `pairs` of `[left, right]`.
**6. `qa`** — `prompt` + `answer` (+ optional `explain`).

```json
{ "sys":"Cardio","topic":"ACS","type":"cloze",
  "text":"Primary PCI for STEMI within {{90 minutes}} of first medical contact; else fibrinolysis within {{30 minutes}}.",
  "explain":"Door-to-balloon ≤90 min; door-to-needle ≤30 min.",
  "guide":{"f":"Topic_C2_ACS.html","t":"C2 · ACS","s":"Reperfusion timing"} }
```

```json
{ "sys":"Cardio","topic":"Heart Failure","type":"multi",
  "prompt":"Which reduce mortality in HFrEF? (select all)",
  "options":["ARNI/ACE inhibitor","Beta-blocker","Loop diuretic","SGLT2 inhibitor"],
  "correct":[0,1,3],
  "explain":"The pillars cut mortality; loop diuretics relieve symptoms only." }
```

(See the v1 examples for `order`, `match`, `qa`, `mcq` — their shapes are identical in v2.0.)

---

## 3. How many questions to generate — **ask, don't assume**

> **⚠ Generating packs is resource-intensive — manage scope and timing.** Building a full pack
> (novel vignettes + 40–60 cards + embedded topic guides) consumes a lot of usage. Warn users **not
> to leave it to the night before an exam and dump multiple weeks at once** — a large multi-week job
> can exhaust their available usage before it finishes, leaving them with nothing. Encourage building
> **one week at a time, well ahead of the exam**. If someone arrives with several weeks the night
> before, flag the risk up front and offer to prioritize the highest-yield week(s) first.

**Default: 10–20 clinical vignettes per week of content.** But **ask the user first** how many they
want and how many weeks this pack covers, then pick a number in range accordingly.

Rationale to share when you ask (this is the real exam pattern to calibrate against):

> Exams here are usually **block exams covering 2–4 weeks** of content, not cumulative
> multi-month finals. The format is **40 questions in one hour**. So a **2-week** block ≈ **20
> questions/week**, a **3-week** block ≈ **13/week**, a **4-week** block ≈ **10/week**. Pick the
> per-week count so the pack(s) for a block land near a realistic 40-question test.

**Cards (spaced repetition): default 40–60 per week of content.** They're for durable recall, not
exam simulation, so they're more generous than questions — but keep them tied to the same topics so
Review and Exam reinforce each other. As with questions, **ask the user** and scale to how many
weeks the pack covers (e.g. a 3-week block ≈ 120–180 cards total). Favor high-yield facts, mechanisms,
and associations over trivia.

---

## 4. Source material — use it, but **don't copy it**

The user may upload their own **WFQs (weekly feedback quizzes)** or other practice questions. You
**may** use these as *input* — to learn the topic emphasis, the level, and the style — but you must
**not rely solely on them**, and you must **not reproduce them**.

> Real exams routinely contain scenarios the student has never seen. So the pack should consist
> **largely of novel questions and new clinical scenarios** that test the same concepts from fresh
> angles — different patient, different presentation, different distractors. Treat provided practice
> questions as a syllabus signal, not a question bank to echo.

When in doubt: same *concept*, new *vignette*.

---

## 5. Difficulty rating system (`easy` / `medium` / `hard`)

**Scope:** difficulty applies **only to clinical vignette questions** used in **Practice and Exam**.
It does **not** apply to review cards. The app shows each question's rating to the learner and lets
them choose a **session difficulty** that filters Practice/Exam. Since app v2.8 this is
**multi-select**: Easy/Medium/Hard toggle independently (e.g. hard-only sessions), and "All" selects
all three — which also includes any questions left unrated. This is one more reason to **rate every
question**: an unrated question disappears from any filtered session.

**Rate every question.** Compute the rating at generation time with a **semantic** judgment that
weighs four dimensions together — not a single proxy like stem length:

1. **Overall complexity** — how many reasoning steps from stem to answer.
2. **Number of topics / systems touched** — single concept vs. cross-system integration.
3. **Prerequisite knowledge** — how much underlying mechanism, interpretation, or calculation the
   reader must already hold (e.g. acid-base math, embryology, pharmacodynamics).
4. **Distractors / red flags / pitfalls** — how many options are deliberately tempting, and whether
   the correct answer is counter-intuitive.

Anchored definitions:

- **easy** — single-step recognition of a classic presentation or one well-known fact; one concept;
  distractors are not very tempting. *E.g. "ACE inhibitor is contraindicated in pregnancy."*
- **medium** — integrate a few data points, apply an algorithm/guideline, or know a specific
  mechanism or test; at least one genuinely plausible distractor. *(Most vignettes land here.)*
- **hard** — multi-step reasoning, often across systems; demands calculation/interpretation or
  embryologic/physiologic prerequisites; the right answer is subtle or counter-intuitive and the
  distractors are close. *E.g. a triple acid-base disorder requiring the delta-delta, or euglycemic
  DKA mechanism on an SGLT2 inhibitor.*

Aim for an **exam-shaped spread** — more medium than easy, fewest hard (a rough 30 / 55 / 15 split
of easy / medium / hard works well). Don't force a quota; rate honestly, but if everything comes out
"medium," push yourself to separate the genuinely simple recall from the genuinely integrative items.

---

## 6. Topic guides — **embedded in the pack** (no separate files)

Topic guides live **inside the pack** in the `guides` array; the app's **Topic Guides** reader
displays them with no external dependency. **Do not output standalone guide `.html` files** —
they are redundant now that guides ship inside the pack. (Only produce a standalone copy if the
user explicitly asks for a printable version.)

Even though no file is written, each guide still needs a **filename-style key** (e.g.
`Topic_C1_Arrhythmias.html`) — it's the identifier that links items to guides.

The `guides` array holds one object per guide:

```json
"guides": [
  {
    "file": "Topic_C1_Arrhythmias.html",
    "title": "C1 · Arrhythmias",
    "html": "<!doctype html><meta charset=\"utf-8\"><title>C1 · Arrhythmias</title> … full guide HTML … "
  }
]
```

- `file` — **must exactly equal** every `guide.f` that points to this guide. This is how the app
  resolves a link to the embedded copy (it's an identifier, not a real file).
- `title` — shown in the Topic Guides sidebar. **Name it well**: the sidebar sorts intelligently by
  parsing a leading `Week NN`, `Topic NN`, or bare `NN —` prefix (roman numerals like "KU III" also
  sort numerically), so titles like `"Week 12 — Arrhythmias"` or `"Topic 03 Adrenal"` order
  themselves correctly.
- `html` — the **complete HTML document** of the guide. It renders in a sandboxed iframe, so it
  carries its own `<style>`.

**Deep-linking (optional, encouraged):** the app does a best-effort scroll to the section named in
`guide.s` by matching a heading inside the embedded guide, and honors `guide.f = "File.html#anchor"`
when a heading carries `id="anchor"`. Add `id="..."` to the guide's `<h2>`/`<h3>` headings so links
can jump straight to them.

### Theming contract — IMPORTANT for colors

The app re-themes every guide at runtime so it matches the user's chosen appearance (Light / Dark /
Black, and the Navy or Warm color family). It does this by **overriding a fixed set of CSS variable
names** inside the guide. So: **drive every color off these variables** (with the light values as
fallbacks) — do **not** hardcode hex colors for text, backgrounds, borders, or accents, or the guide
will look wrong (e.g. a white page) in dark mode.

Canonical variable names the app overrides (use these exact names):
`--paper` (page background), `--ink` (text), `--soft` (muted text), `--line` (borders/rules),
`--accent` (primary accent), `--accent2` (secondary accent), `--green` / `--blue` / `--gold`
(status colors), `--hi` (highlight/important), `--lo` (secondary highlight). Define them in `:root`
with sensible **light** defaults; the app swaps them per theme automatically.

### Guide HTML template (goes in the `html` value)

```html
<!doctype html><meta charset="utf-8">
<title>C1 · Arrhythmias</title>
<style>
  :root{--paper:#f3efe6;--ink:#1d1b16;--soft:#56524a;--line:#cfc8b6;--accent:#7c2d2d;--accent2:#9a5a2a;--green:#2f6b4f;--blue:#2d5a6b;--gold:#8a6d1f;--hi:#a23b2e;--lo:#2d5a6b}
  body{max-width:820px;margin:40px auto;padding:0 22px;font:18px/1.6 Georgia,serif;color:var(--ink);background:var(--paper)}
  h1{font-size:30px;margin:0 0 4px}
  h2{font-size:13px;letter-spacing:.1em;text-transform:uppercase;color:var(--accent);margin:26px 0 6px}
  td,th{border:1px solid var(--line);padding:7px 10px;text-align:left;font-size:15px}
  .key{border-left:3px solid var(--accent);background:var(--paper);padding:10px 14px;margin:10px 0}
</style>
<h1>C1 · Arrhythmias</h1>
<p>One-line orientation.</p>
<h2 id="atrial-fibrillation">Atrial fibrillation — rate control</h2>
<div class="key">Irregularly irregular, no P waves → <b>beta-blocker</b> or non-DHP CCB first-line.</div>
```

Embed the full HTML (escaped for JSON) as the `html` value of the matching `guides[]` entry.

---

## 7. Validation checklist (every item must pass)

**Pack:** JSON object with `id`, `pack`, `formatVersion`, `version`, `author`; `questions` and/or
`cards` arrays; a `guides` array if any item carries a `guide`.

**Each question:** `sys`, `topic`, `stem`; `options.length ≥ 2`; `correct` integer in
`0…options.length-1`; **`difficulty` ∈ {easy, medium, hard}**.

**Each card:** `sys`, `topic`, valid `type`; and by type — `mcq` (`prompt`, `options` ≥2, `correct`
in range) · `multi` (`prompt`, `options`, `correct` = array of in-range indices) · `cloze` (`text`
with ≥1 `{{…}}`) · `order` (`prompt`, `items` ≥2, in correct order) · `match` (`prompt`, `pairs` of
2-element arrays) · `qa` (`prompt`, `answer`). All non-`cloze` cards need a `prompt`.

**Guides:** every `guide.f` used anywhere **resolves to a `guides[]` entry whose `file` matches**;
every `guides[]` entry has non-empty `html`.

**JSON hygiene:** 0-based `correct`; straight quotes; HTML inside strings must keep the JSON valid
(escape inner double quotes, or use single quotes inside the HTML).

---

## 8. Output instructions

When you finish, produce **one downloadable file** (not just code in chat):

1. The **pack `.json`**, **with the topic guides embedded** in `guides[]`. This single file is the
   complete deliverable — the embedded guides make it fully self-sufficient (Topic Guides reader
   included). **Name it `Pack_<Name>.json`** (e.g. `Pack_Week09_Pediatrics_v2.0.json`) so it is
   picked up as a default pack — see the rule below.
2. A short report in chat: how many questions (and the easy/medium/hard split), how many cards, and
   which guides are embedded.

**Default packs by default.** Unless the user explicitly says a pack is private/custom-only, every
pack you create is meant to ship in the hosted site's **Default study packs** panel. The deploy step
(`Refresh Web Deploy.command`) builds that panel's `packs-manifest.json` by globbing
**`Study Packs/Pack_*.json`** — so a default pack's filename **must start with `Pack_`** to be listed
(combined `Weeks_*` packs are deployed but intentionally unlisted to avoid duplicate-card loading).
Save the file into the `Study Packs/` folder and remind the user to run `Refresh Web Deploy.command`
to publish it to https://studysuite.app. The `school` / `year` / `course` / `weeks` fields (§2) then
group it correctly in the panel.

Do **not** emit standalone guide `.html` files unless the user asks for printable copies.

---

## 9. How the human uses the output (for your closing summary)

1. Open the app at **https://studysuite.app** (nothing to install; a local copy of the
   app `.html` works identically).
2. Drag the new `.json` pack onto the drop zone — it persists and appears in the **Content
   library**, toggleable on/off. (The site also offers the author's default packs with one-click
   "+ Add to library".)
3. Pick a **Session difficulty** — Easy/Medium/Hard are multi-select toggles, "All" selects all
   three — for Practice and Exam; **Review** ignores difficulty. Open **Topic Guides** to read the
   bundled guides.
4. **Backup all** periodically — one file with packs, progress, and settings, restorable anywhere.

Load multiple weeks and toggle to whatever's being studied. Add weeks anytime by generating more
packs with this guide. Bump each pack's `version`/`updated` when you revise it.
