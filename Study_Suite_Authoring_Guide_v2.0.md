<!-- Study Suite — Content Authoring Guide · v2.0 · [Alkarim Billawala / alkarim.billawala.ca] -->

# Study Suite — Content Authoring Guide (v2.0)

> **Read me first — this file is written for the *assistant*, not the end user.**
> If you are an AI assistant (e.g. Claude) and this document has been given to you, it is your
> operating manual for turning the user's study material into Study Suite content. Act on it; do
> not simply paraphrase it back to the user. The end user is generally *not* expected to read this
> file (only an advanced user would). Everything below tells **you** what to produce and how.
>
> **Authoring system version:** 2.0 · **Pairs with:** Study Suite app v2.0, pack `formatVersion` 2.0.
> **Author / attribution:** Alkarim Billawala / alkarim.billawala.ca.
> **Versioning rule:** the app, this guide, and every pack carry a version. When you revise a pack,
> bump its `version` and `updated` date so changes are trackable.

---

## 0. What to do the first time you're given this file

When this guide is first loaded and you've read it, **do not immediately demand materials.** First
**teach the user the workflow** in a short, high-level overview, *then* ask them for their content.
Specifically:

1. **Explain the loop in plain terms:**
   - They give *you* their lecture material (notes, slides, transcripts, a syllabus, optionally past quizzes).
   - You turn it into **one content pack** — a single `.json` file — containing exam questions, spaced-repetition cards, and the topic guides embedded inside it.
   - They load that pack into the **Study Suite app** (`Study_Suite_v2.0.html`) by dragging it onto the drop zone.
   - The app then runs three modes over it: **Review** (spaced repetition), **Practice** (one-at-a-time with instant answers), and **Exam** (timed, scored). It also has a **Content Reviewer** that reads the topic guides bundled in the pack.
2. **Tell them what you need from them and what they'll get back:** their materials in → one `.json` pack (plus standalone `.html` guides) out.
3. **Then prompt them to upload** the lecture material they want turned into a pack, and ask the **scope question** in §3 (how many weeks the pack covers / how many questions they want).

Keep this overview brief — a few sentences per point. The goal is orientation, not a lecture.

---

## 1. Your job, in one paragraph

Convert the user's study material into **one valid pack `.json` per week or topic-set**, containing
`questions` (for Practice/Exam), `cards` (for Review), and a `guides` array holding the **full HTML of
each topic guide embedded inline**. Also produce those same topic guides as **standalone `.html`
files**. Rate every question's `difficulty`. Validate everything against §7 before output. Prefer
**novel** vignettes over reproductions of any practice material you're given (§4).

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
| `id` | yes | Short unique slug (`"wk12"`). Reloading a pack with the same `id` **replaces** the old one (and preserves review progress, which is keyed by card `id`). |
| `formatVersion` | yes | Pack-format version. Use `"2.0"`. |
| `version` | yes | This pack's content version. Bump on revision. |
| `author` / `createdBy` | yes | Attribution. Set both to `"Alkarim Billawala / alkarim.billawala.ca"` unless told otherwise. |
| `created` / `updated` | recommended | ISO date strings. |
| `questions` / `cards` | arrays | Either may be empty, but a useful pack has both. |
| `guides` | yes if any item links a guide | Embedded topic guides — see §6. |

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

**Default: 10–20 clinical vignettes per week of content.** But **ask the user first** how many they
want and how many weeks this pack covers, then pick a number in range accordingly.

Rationale to share when you ask (this is the real exam pattern to calibrate against):

> Exams here are usually **block exams covering 2–4 weeks** of content, not cumulative
> multi-month finals. The format is **40 questions in one hour**. So a **2-week** block ≈ **20
> questions/week**, a **3-week** block ≈ **13/week**, a **4-week** block ≈ **10/week**. Pick the
> per-week count so the pack(s) for a block land near a realistic 40-question test.

Cards (spaced repetition) can be more generous than questions — they're for durable recall, not
exam simulation — but keep them tied to the same topics so Review and Exam reinforce each other.

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
them choose a **session difficulty** that filters Practice/Exam **cumulatively**: Easy → easy only;
Medium → easy + medium; Hard → all rated; All → everything (including anything left unrated).

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

## 6. Topic guides — produced **twice**: standalone **and** embedded

Every topic guide must exist in **both** forms:

1. **A standalone `.html` file** (`Topic_<code>_<Name>.html`) — openable/printable on its own, and the
   fallback target for `guide.f` links when a pack isn't loaded.
2. **Embedded inside the pack** in the `guides` array, so the app's **Content Reviewer** can display
   it with no same-folder dependency.

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
  resolves a link to the embedded copy.
- `title` — shown in the Content Reviewer's chip list.
- `html` — the **complete HTML document** of the guide (the same content as the standalone file).
  It renders in a sandboxed iframe, so it can carry its own `<style>`.

**Deep-linking (optional, encouraged):** the app does a best-effort scroll to the section named in
`guide.s` by matching a heading inside the embedded guide, and honors `guide.f = "File.html#anchor"`
when a heading carries `id="anchor"`. Because the embedded copy can be **richer** than the printable
standalone, add `id="..."` to the guide's `<h2>`/`<h3>` headings and you can jump straight to them.

### Standalone guide template

```html
<!doctype html><meta charset="utf-8">
<title>C1 · Arrhythmias</title>
<style>
  body{max-width:820px;margin:40px auto;padding:0 22px;font:18px/1.6 Georgia,serif;color:#1d1b16;background:#f3efe6}
  h1{font-size:30px;margin:0 0 4px}
  h2{font-size:13px;letter-spacing:.1em;text-transform:uppercase;color:#7c2d2d;margin:26px 0 6px}
  td,th{border:1px solid #cfc8b6;padding:7px 10px;text-align:left;font-size:15px}
  .key{border-left:3px solid #7c2d2d;background:#fff;padding:10px 14px;margin:10px 0}
</style>
<h1>C1 · Arrhythmias</h1>
<p>One-line orientation.</p>
<h2 id="atrial-fibrillation">Atrial fibrillation — rate control</h2>
<div class="key">Irregularly irregular, no P waves → <b>beta-blocker</b> or non-DHP CCB first-line.</div>
```

Embed this exact HTML as the `html` value of the matching `guides[]` entry.

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

## 8. Output instructions — and handling a one-file-per-turn limit

When you finish, produce **downloadable files** (not just code in chat):

1. The **pack `.json`**, named for the week/topic (e.g. `Week_12_Cardiology.json`), **with the topic
   guides embedded** in `guides[]`.
2. Each topic guide as a **standalone `.html`** file, filenames matching every `guide.f`.
3. A short report: how many questions (and the easy/medium/hard split), how many cards, and which
   guide files you produced.

**If your environment only lets you emit one file per turn:** the embedded guides make the pack
**self-sufficient** for studying, so **generate the `.json` pack first** (it works fully in the app,
Content Reviewer included). Then tell the user you'll provide the **standalone `.html` guides next** —
and produce them in the following turn(s). Never hold back the pack waiting on the guides.

---

## 9. How the human uses the output (for your closing summary)

1. Put `Study_Suite_v2.0.html`, the pack `.json` file(s), and the standalone `Topic_*.html` guides in **one folder** (the standalone guides are optional once guides are embedded, but handy for printing/direct opening).
2. Open the app (double-click locally, or host the folder on any static web host to share).
3. Drag the `.json` pack(s) onto the drop zone — they persist and appear in the **Content library**, each toggleable on/off.
4. Pick a **Session difficulty** (Easy/Medium/Hard/All) for Practice and Exam; **Review** ignores difficulty. Open **Content Reviewer** to read the bundled guides.
5. **Backup all** periodically — one file with packs, progress, and settings, restorable anywhere.

Load multiple weeks and toggle to whatever's being studied. Add weeks anytime by generating more
packs with this guide. Bump each pack's `version`/`updated` when you revise it.
