<!-- Study Suite — Content Authoring Guide · v2.29 · [Alkarim Billawala / alkarim.billawala.ca] -->

# Study Suite — Content Authoring Guide (v2.29)

> **Read me first — this file is written for the *assistant*, not the end user.**
> If you are an AI assistant (e.g. Claude) and this document has been given to you, it is your
> operating manual for turning the user's study material into Study Suite content. Act on it; do
> not simply paraphrase it back to the user. The end user is generally *not* expected to read this
> file (only an advanced user would). Everything below tells **you** what to produce and how.
>
> **Authoring system version:** 2.29 · **Pairs with:** Study Suite app v0.4.4+ (source switches need v0.6.6+, Extras v0.7.1+, image cards v0.8.1+, interactive figures v0.9.0+, atlas packs v0.10.0+ — links and read-more v0.10.9–v0.10.10), pack `formatVersion` 2.0
> **What changed in guide v2.29:** **atlas packs.** New **§6f**: the `atlas` widget type — a reference pack of drawn sections and views with a shared structure registry, labels that light on tap, territories with a legend, pathways, course-image references, locator maps and read-more links — and the two guide hooks any pack can use: `data-ssw-state` on a figure and `data-atlas` links. The Brain (`neuro_brain`) is the reference implementation; the checks (`packbuilder.py`, `pack_checks.py`) know the type.
> **What changed in guide v2.28 (previous):** **interactive figures and drawn schematics.** New **§6e**: a pack may carry
> `widgets` — data for five interactive figure types the app draws and drives (slice scroller, layered figure,
> tap-the-spot, lesion localiser, switchable views) — placed in guides as `<figure class="fig ssw" data-ssw="…">` with a
> still poster, and used by two new card types, **`spot`** (tap the answer on the figure, auto-graded) and **`widget`**
> (the figure in a set state, self-graded). Packs still carry no script. **§6c** now asks for **custom-drawn schematics**
> of the structure or mechanism itself (a simplified anatomical shape, a few colour-coded parts, leader-line labels with
> a bold name and a short gloss), not only charts and slide crops. App v0.9.0+ runs them; older apps show the posters and
> skip the two card types. The §7a validator accepts `spot` and `widget`. No `formatVersion` change.
> **What changed in guide v2.27:** **images in questions and cards.** New **§6d**: an image may anchor a question or
> card (a data: `<img>` in a stem, prompt, answer or explain), and a new card type **`label`** shows a figure with its
> printed labels masked; the learner names each one, taps a box to check it, and self-grades as for `qa`. Data: URIs only
> (WebP, ~700 px wide, quality ~60, ~25 KB per image, ~1 MB of card images per pack), no identifiable patient, and a stem
> still stands alone in words. App v0.8.1+ shows them; older apps skip label cards. The §7a validator accepts `label`
> and checks item images. No `formatVersion` change.
> **What changed in guide v2.26:** **figures in topic guides.** New **§6c**: a guide may carry figures, slide or
> course images cropped to the figure and drawn diagrams written as inline SVG, where a picture does work the text
> can't (anatomy, imaging, circuits, curves, timelines, cycles), and nowhere else. No photo of an identifiable patient.
> Captions restate the guide's own text, with a short credit. The §7a validator measures guide length on the text only
> (figures excluded) and errors on a script, an inline event handler or any outside resource in a guide. No schema change.
> **What changed in guide v2.25:** **option length is not a cue.** A student's feedback (2026-09-30): "for a lot of the
> questions, the longest answer is the right one" — and it was, in 70–79% of the core items of four packs (chance is
> 25%). New **§3b rule 8**: distractors carry the key's specificity and length, an over-long key is trimmed, and across a
> pack the key is the single longest option in about a quarter of core items — never ≥ 1.5× its longest distractor, and
> not never-longest either. §7 checks it; the §7a validator warns on the pack share and lists lopsided items. No schema
> change.
> **What changed in guide v2.24:** a pack with no source table of its own can still use Extras: its `sources` may hold
> only `"EXTRA": "Extras"`, and then only the extra items carry `src` (§2 "Extras"). The §7a validator no longer asks
> for `src` on every item in that case. No other change.
> **What changed in guide v2.23:** **size by the material, one item per fact, and Extras.** §3's "targets are floors"
> line is gone: a pack is as large as the week's *examinable* material, and each fact gets the number of items its
> emphasis earns — usually one question and at most one card, more only for a fact the course stresses repeatedly. New
> **§3b** sets out the rules an audit of four oversized packs produced (2026-09-27): no card that restates a question, no
> second item on a fact already tested (across guides too), case and review guides *apply* concepts rather than re-ask
> them, and exact figures, dates, names, case narrative, source structure and common sense are not core items. Items
> that are correct and still useful but lower-yield take a reserved source code, **`EXTRA`** (§2 "Extras"): they ship
> in the pack, the app (v0.7.1+) keeps them off by default and out of every count, and one switch per pack turns them
> on. Exact duplicates and absurd, ambiguous or incorrect items are not shipped at all. The §7a validator lints Extras.
> **What changed in guide v2.22:** **content sources** (additive, optional; no `formatVersion` change). A pack may carry a
> top-level **`sources`** table — `{code: label}`, one entry per course material the pack was built from (a lecture, a
> module, a case, a quiz, a pre-reading) — and every question, card, guide and drug then carries **`src`: [codes]**, the
> sources it draws on. The app (v0.6.6+) shows the table as per-pack on/off switches in the library; an item is hidden only
> when **every** code in its `src` is off, so a synthesis item stays while any of its sources is on. Headings inside a
> guide may carry **`data-src="CODE"`** (one or more codes) to hide just that section when its sources are off — use it
> only where a section genuinely comes from a subset of the guide's sources; untagged sections always show with the
> guide. Packs without `sources` are unchanged. The §7a validator lints missing / unknown codes. See §2 "Content sources".
> **What changed in guide v2.21:** no schema change. A drug's `ref.topic` (§6b) is shown as a small source tag on
> every line of that drug in the Pharmacology view, so it must be **one short topic name (40 characters or fewer)** — the
> topic the drug is mainly taught under — never a list of every topic it appears in joined together. The §7a validator
> warns on long or joined topics.
> **What changed in guide v2.20:** no schema or rule change — §0's overview of the app (what you tell the user) now
> describes the current app: setup screens, question history and fresh exams, topics, the Pharmacology index, and
> optional encrypted sync. Nothing about how packs are built has changed since v2.19.
> **What changed in guide v2.19:** **topics are real topics.** An item's `topic` is the concept-level group it belongs
> to — chosen from a top-level view of the *whole* pack's material during synthesis — not a per-item label. A dense week
> lands at roughly **8–20 topics, each spanning several items (≥3)**. Topics and guides are independent: one guide can
> span several topics, and a topic can draw on several guides. Declare it with a new top-level field
> **`"groupBy": "topic"`** (§2). The app groups Review's topic picker, the Practice/Exam topic filters and the exam
> "Topic" weighting by it. Without `groupBy` — or if most topics still hold a single item — the app groups by the
> **linked guide** instead, so every item should carry a `guide` pointer. Packs built before v2.19 are not reworked:
> the app groups them by guide automatically. The §7a validator warns on fragmented topics.
> **What changed in guide v2.18:** no schema change. §4a's ban on **source reportage** is now spelled out as a
> list of banned sentence frames ("the module says", "the lecture's deck…", "as taught", "in the slide's order")
> with rewrites, and the §7a validator lints for them. The only reader-facing place a source may be named is a
> **Sources differ** callout — and conflicts between sources remain *required* there, adjudicated against a named
> external reference (unchanged from v2.17). Reason: packs built under v2.17 still carried hundreds of
> "the module states…" sentences because only tags and locators were being checked.
> **What changed in guide v2.17:** quality rules distilled from building large packs from full lecture
> transcripts, modules and case sessions. No schema change. (1) **Topic guides synthesize by concept**
> (§6, §7): a guide gathers everything the week says about one concept wherever it lives — a lecture
> segment, a module, a case, a practice quiz — never one guide per lecture or module. The old "6–10
> narrow guides" rule of thumb is replaced by "as many guides as the week has concepts" with a
> **10,000–20,000-character body** as the working band. (2) **Plain-text fields carry real characters,
> never HTML entities** (§2, §7): the app renders HTML only in guide `html` and in `explain`; a `&middot;`
> in a stem, option, topic, card field or guide title shows literally to the learner. (3) **The pack is
> study material, not a report on the sources** (new §4a): no source tags, timestamps, slide numbers,
> "the module says" reportage or working-notes markers in any reader-facing text; **declarative voice**, no
> reader address or coaching. **Conflicts between sources are called out, not smoothed over**: each one gets
> a short "Sources differ" callout, adjudicated against an external reference. (4) **Larger packs are welcome** (§3): the volume targets are floors that
> scale with the material, not ceilings. (5) The §7a validator gains lints for entities in plain fields,
> leaked source apparatus, reader address and guide size.
> **What changed in guide v2.16:** packs may now carry an **optional `drugs[]` array** that powers the app's new
> **Pharmacology** view — a cross-week drug index built from every active pack (new §6b). It's additive and optional
> (packs without it are unaffected); when present, the app merges drug records across enabled packs by a stable
> `rx:` id, groups them by class, and renders faceted fields (use / mechanism / dosing / cautions / adverse /
> monitoring / interactions) with source links back to the originating week's guide. §2 schema + §7 checklist updated.
> **What changed in guide v2.15:** **every pack now ends with a one-page CRAM SHEET** as its final
> `guides[]` entry (new §6a) — a dense, print-friendly "night-before" rapid review of the pack's week(s):
> a do-not-miss/emergency strip, then compact topic cards of *cue → answer* rows. It uses the same
> dark-mode-safe CSS contract as the other guides, and links from items are optional (it's a review
> surface, not a deep-link target). §7 checklist updated to expect it. No schema change (the cram sheet is
> just another guide).
> **What changed in guide v2.14:** kept the option-shuffle guidance in lockstep with app behaviour. The
> app (v0.2.63) now **anchors** "All/None of the above" / "…of these" options — it pins them to their
> authored slot and shuffles only the other options around them — so those are **fine to use** (no longer
> "avoid"). What remains forbidden is **position-REFERENCING** options ("A and B", "both of the above",
> "the first option", "Options 1 and 3"): they reference *specific* other options, so they can't be
> safely shuffled OR anchored. §2/§3a/§7/§7a updated accordingly; the §7a validator now flags only the
> position-referencing kind. No schema change.
> **What changed in guide v2.13:** quality refinements from a side-by-side audit of an outside-LLM pack
> vs. the maintainer pack (same week, same source). No schema change. (1) **Answer-option order no longer
> matters** (§2 question schema): the app **shuffles options at render time**, so place `correct`
> at any index (first is fine for readability) — but **avoid position-dependent options** like "all of
> the above" / "A and B". (2) **Card-type mix** (§3a): target a varied spread and don't go cloze-heavy;
> every `multi` needs ≥1 false option (no "all correct" or "all-but-last" patterns). (3) **Guide
> granularity** (§6): one dense week ≈ **6–10 narrow guides**, not 3–4 broad ones. (4) **`guide.s` is a
> best-effort deep-link + a displayed label** (§6): make it a short heading-like cue. (5) **Source
> practice questions** (§4): if the upload contains "Question 1/Answer 1…", name them as source and write
> **novel** vignettes by default. (6) **Placement example** (§2): `course` is the shared block (`CPC 1`),
> not a broad code (`MED120`). (7) The §7a reference validator gains positional-option + card-mix lints.
> **What changed in guide v2.12:** **build the pack programmatically and validate before delivering**
> (new §7a) — if you can execute code, don't hand-write the JSON: assemble the pack as a data structure,
> run it through a validator that enforces the §7 checklist + the §8 output-hygiene scan, **pretty-print**
> it, and **refuse to emit on any failure**. A copy-pasteable reference validator is included. This
> directly eliminates the most common import-breaking failures (minified JSON, injected citation/grounding
> markers, broken `correct` indices, unbalanced cloze, unrated questions). It does *not* substitute for
> content depth (§3/§6) — a validator checks structure, not teaching quality. No schema change.
> **What changed in guide v2.11:** **file-naming convention** (§8) — name the pack file after its
> placement, `<Year>_<Course>_Week<NN>_<Subject>_v<ver>.json`, so a learner's packs stay organized and
> sort in a folder (the app still identifies/sorts by fields, not the name). No schema change.
> **What changed in guide v2.10:** quality hardening based on observed failure modes — no schema
> change. (1) **Output hygiene** (§8): the deliverable is *pure, valid JSON only* — no prose, no
> markdown fences, and **strip any citation/grounding/footnote markers your tooling injects** (a single
> stray token makes the whole file fail to import); **parse-test before delivering**. (2) **Guides must
> be substantive** (§6): the template is a *minimum skeleton*, not the goal — produce one real guide
> per topic block, with sections/tables, not a single sparse page. (3) **Link items to guides** (§6):
> most questions/cards should carry a `guide` pointer so the Topic Guides reader is actually used.
> (4) **Hit and scale volume** (§3): 10–20 questions and **40–60 cards per week**, multiplied by the
> number of weeks the pack covers. (5) **Fill placement; `course` is the grouping block, not the
> week's subject** (§2). These are baked into the §7 validation checklist. (6) **Version self-check**
> (§0b): on load, the assistant fetches `https://studysuite.app/authoring-version.json` and, if a newer
> guide exists, prompts the user to grab the latest before building (best-effort; degrades gracefully
> when offline).
> **What changed in guide v2.9:** new optional pack field `term` (semester/term, e.g. `"Fall"` /
> `"Semester 2"`) — an optional level **between `year` and `course`** in the placement hierarchy. The
> app (v0.2.49+) renders it as a sub-group and sorts it chronologically, and it is **gracefully
> invisible when unset** (packs without a term group exactly as before). Also adds an **Organizational
> hierarchy & sorting** note (§2): the placement fields are ordered slots — encourage the default
> school → year → (term) → course → week, but users may repurpose them for their own equivalent
> leveling as long as the values still sort. Ask how the program is organized and whether week/topic
> numbers **reset per course** (if so, make the course labels or `weeks` values encode the order).
> **What changed in guide v2.8:** two additions, no schema change. (1) A **cost/usage warning** (§3):
> generating packs is resource-intensive — discourage users from dumping multiple weeks the night
> before an exam, or they may run out of usage mid-build. (2) **Absolute vs. relative difficulty and
> customization** (§5a): the easy/medium/hard tags are *relative* and must always be applied so the
> app can filter; the pack's *absolute* level is customizable (e.g. shift up for a resident sitting a
> Royal College exam) and auto-scales from school/year/field and the difficulty of the supplied
> materials. Question *format* is likewise customizable.
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
   - The app then runs three modes over it: **Review** (spaced repetition), **Practice** (one-at-a-time with instant answers — new questions first, prior answers viewable on repeats), and **Exam** (timed, scored, built from fresh questions and weighted by system, pack or topic; resumable). All three can be narrowed to chosen **topics**; Practice and Exam also by difficulty. It also has a **Topic Guides** reader for the guides bundled in the pack, a **Pharmacology** index built from the packs' drug records (§6b), and an optional account that syncs progress between devices, end-to-end encrypted.
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

## 0b. First, check you have the latest version of this guide

This guide is **versioned** (its version is shown in the header at the top of this file). The authoring system evolves, so
before you build, make a quick, one-time check that the user isn't working from an outdated copy:

1. **If you can browse the web**, fetch the small public pointer file at
   **`https://studysuite.app/authoring-version.json`** (this is the project's GitHub Pages repo,
   served on its custom domain). It returns something like
   `{ "authoringGuideVersion": "<latest>", "guideUrl": "https://studysuite.app/Study_Suite_Authoring_Guide_v<latest>.md", "updated": "<date>" }`.
2. **Compare the versions numerically** — treat the version as `major.minor`, so `2.10` is *newer*
   than `2.9` (don't compare as plain text). If the published `authoringGuideVersion` is higher than
   the version in this file's header, tell the user:
   > *"Heads up — you're using authoring guide v&lt;yours&gt;, but v&lt;latest&gt; is now available.
   > For best results, download the latest from &lt;guideUrl&gt; and re-upload it before we build."*
   Then **proceed with the copy you have** — this is a courtesy, not a gate.
3. **If you can't browse the web**, don't fail or stall. Just tell the user which version you're
   working from and that they can check for a newer one at
   `https://studysuite.app/authoring-version.json` (or the project site) and re-upload if needed.

Do this check **once, at the start**, then carry on with the workflow below.

---

## 1. Your job, in one paragraph

Convert the user's study material into **one valid pack `.json` per week or topic-set**, containing
`questions` (for Practice/Exam), `cards` (for Review), and a `guides` array holding the **full HTML of
each topic guide embedded inline**. The pack is the **only deliverable** — do not produce standalone
guide `.html` files unless the user explicitly asks for printable copies. Rate every question's
`difficulty`. Validate everything against §7 before output. Prefer **novel** vignettes over
reproductions of any practice material you're given (§4). Write the pack as **study material, not a
report on the sources** (§4a) — calling out and adjudicating any conflicts between them — and keep
plain-text fields free of HTML entities (§2).

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
  "guides":    [ /* embedded topic guides: {file, title, html} */ ],
  "drugs":     [ /* OPTIONAL — pharmacology records for the Pharmacology view; see §6b */ ],
  "sources":   { /* OPTIONAL (v2.22) — {code: label} table behind the app's per-pack source switches; see "Content sources" below */ }
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
| `groupBy` | yes (new packs and updates) | `"topic"` — declares that items' `topic` values are **real topics** (v2.19; see the `topic` field below). Omit it only for a pack whose topics can't be grouped; the app then groups by each item's linked guide. |
| `drugs` | optional | Pharmacology records that feed the app's cross-week **Pharmacology** view. Additive/optional — omit it and nothing changes. See **§6b**. |
| `sources` | optional (v2.22; recommended for new packs and updates) | `{code: label}` — one entry per source the pack was built from, e.g. `"L1-L2": "L1–L2 · Schizophrenia (Wong)"`, `"CBL42": "CBL 42 · Psychosis cases"`. The app lists them as on/off switches under the pack in the library. Codes are short, stable and unique within the pack; labels are what the learner reads. See **Content sources** below. |
| `school` / `year` / `term` / `course` / `weeks` | optional | Curriculum placement, e.g. `"UofT — Temerty Medicine"` / `"Year 1"` / `"Fall"` (term — optional) / `"CPC 2"` / `"Weeks 25–28"`. Used to **group and chronologically sort** packs in the hosted site's Default study packs panel and the user's Content library; ignored otherwise. `term` sits **between `year` and `course`** and is omitted by most packs (harmless when absent — the level simply doesn't render). Ask the user how their program is organized — see *Organizational hierarchy & sorting* below — and include the fields that apply. |

### Organizational hierarchy & sorting

The placement fields form an **ordered hierarchy** the app uses to group and chronologically sort
packs in both the Default study packs panel and the user's Content library:

> **school → year → `term` (optional) → course → week / unit / topic**

> **Common mistake — keep `course` and the week's subject separate.** `course` is the **grouping level
> that several weeks share** (e.g. `"CPC 1"`, `"Cardiology block"`), so packs nest under it. The week's
> actual subject goes in `weeks` (e.g. `"Week 11 · Immunology II"`) and/or the `pack` display name —
> **not** in `course`. Putting the topic in `course` (e.g. `course:"Immunology II"`) gives every week
> its own one-pack "course" and defeats the grouping; leaving placement fields null drops the pack into
> an "Other" bucket. Fill them all, and ask the user if you don't know their structure.
>
> **Also avoid the opposite mistake — a broad administrative code.** Use the curriculum **block the
> learner thinks in** (e.g. `"CPC 1"`), not a registrar's course code like `"MED120"`, unless the user
> explicitly wants to group by that code. The block is what the user recognizes and what keeps related
> weeks together. (Likewise, prefer a short stable `id` such as `"wk16"` and sequential item ids
> `"wk16_q01"` / `"wk16_c01"`, with guide keys like `"W16_01_<Topic>.html"`.)

Two things to know — and to **ask the user** about:

1. **Encourage the default, but allow custom leveling.** The default meaning (school / year / term /
   course / week) fits most curricula, so present it as the recommended default. But the fields are
   really *generic ordered slots* — a learner whose program isn't shaped that way may repurpose them
   (e.g. `year:"PGY-2"`, `course:"Cardiology block"`), **as long as the values still sort into the
   intended order.** The app orders each level by the **first number found in the pack's `weeks`
   field**, falling back to a numeric-aware natural compare of the label (so `"CPC 1"` precedes
   `"CPC 2"`, and `"Block 10"` follows `"Block 9"`). Whatever labels the user picks, make sure they
   sort.

2. **Document the chronology — especially when numbers reset per course.** Some programs number weeks
   or topics **1, 2, 3… within each course**, so the same "Week 1" recurs across courses. Because the
   app sorts a level by its earliest `weeks` number, a global reset can make courses tie. To keep the
   order right, either (a) give `weeks` a **globally increasing** range across the year (the UofT
   default runs Weeks 1–35 across ITM → CPC 1 → CPC 2), or (b) ensure the **course labels themselves
   sort** (numbered blocks/units), and/or (c) use the optional **`term`** field to separate them (e.g.
   Fall vs Winter). Ask the user up front how their weeks/topics are numbered so the pack encodes a
   sortable chronology.

The `term` level renders **only when a pack actually sets it** — packs that omit it group exactly as
if the level weren't there, so leaving it out costs nothing.

### Shared item fields (questions **and** cards)

| field | required | notes |
|---|---|---|
| `sys` | yes | Short system/group code, e.g. `"Cardio"`. Drives the system breakdown and exam weighting. The app shows friendly labels for `"Endo"`→Endocrine, `"GI"`→Gastrointestinal, `"KU"`→Renal/Urinary; any other string shows as-is. |
| `topic` | yes | A **real topic** — the concept-level group the item belongs to, e.g. `"Arrhythmias"`, `"Heart failure"`. Decide the pack's topic list **once, from the whole pack's material** (during synthesis, before writing items), then assign every item to one of them: roughly **8–20 topics for a dense week, each spanning several items (≥3)**. It is *not* a per-item label (`"Symmetry"`, `"Bayes & serology"` are sub-points, not topics). Topics are independent of guides — a guide may span several topics. The topic is shown as the item's tag, drives Review's topic picker and the Practice/Exam topic filters, and is the exam's "Topic" weighting (with `groupBy:"topic"`). |
| `explain` | strongly recommended | Teaching/answer text. **HTML allowed** (`<b>`, `<i>`, `<br>`). |
| `guide` | strongly recommended | `{ "f": "file.html", "t": "short title", "s": "section pointer" }`. `f` **must exactly match** the `file` of an embedded guide in `guides[]` (see §6). Also the app's **grouping fallback** when a pack's topics aren't real topics. |
| `id` | recommended | Stable unique id (e.g. `"wk12_c01"`) so review progress survives reloads/edits. |
| `src` | yes when the pack has `sources` (v2.22) | `["L1-L2", "CBL42"]` — every source code the item draws on, each present in the pack's `sources`. A synthesis item that could be answered from any of several materials lists all of them. The app hides the item only when **all** of them are off. Guides and drugs carry the same field. |

> **Plain text vs HTML — know which fields render markup.** The app renders HTML in exactly two places:
> a guide's `html` and an item's `explain`. **Everything else is plain text**: question `stem` and
> `options`, `topic`, every card field (`prompt`, `answer`, `text`, `items`, `pairs`, `options`), guide
> `title`, and the placement fields. In plain-text fields write **real characters** — `·`, `—`, `≥`, `×`,
> `→` — **never HTML entities** (`&middot;`, `&ge;`, `&rarr;`): an entity there is shown literally to the
> learner. Entities and tags are fine inside `html` and `explain`. (The §7a validator flags entities in
> plain fields.)

### Content sources (`sources` · `src` · `data-src`) — v2.22

A pack can tell the learner **where each piece of content came from** and let them **set a source aside**
(a lecture they've already mastered, a module they haven't done yet, a pre-reading that isn't examinable).
Three pieces, all optional and additive:

1. **`sources`** (top level): `{code: label}`. Codes are short and stable (`"L3"`, `"SLM02"`, `"CBL42"`,
   `"ICE"`, `"WFQ"`); labels are reader-facing (`"L3 · Patient Interviews (Agrawal)"`). One entry per
   course material the pack was built from — the same set the master guide's source ledger lists, at the
   granularity the learner would switch (a lecture, a module, a case session, a quiz), not per file.
2. **`src`** on every question, card, guide and drug: the list of codes it draws on. Rules:
   - A **synthesis item** lists every source that teaches the fact (it stays visible while any one is on).
   - A **single-source item** (a quiz reproduction, a case detail, a module exercise) lists just that code.
   - A **guide** lists every source it synthesizes; the **cram sheet** and any integrated review list all of them.
   - A **drug** lists the sources its facets came from.
   - Every code used must exist in `sources`; every item should carry `src` when the pack has `sources`
     (untagged items are simply never hidden — the validator warns).
3. **`data-src`** on a heading inside a guide's `html` (`<h2 data-src="HSR">`, or several codes separated
   by spaces): the section — that heading and everything up to the next heading of the same or higher level —
   is hidden when **all** its codes are off, and the guide shows a note saying so. Tag a section **only when it
   genuinely comes from a subset of the guide's sources** (an appraisal exercise inside a clinical-skills guide,
   a lab-session block inside an anatomy guide, a case walkthrough inside a concept guide). Sections of a
   concept guide that blend the week's sources stay untagged — tagging them with every code hides nothing,
   and tagging them with one is wrong. Codes in `data-src` must be a subset of the guide's own `src`.

The app never filters a pack without `sources`, and filtering is by item — review progress, question history
and running sessions are untouched when a source is switched off.

**Extras (`EXTRA`) — v2.23.** One code is reserved: **`"EXTRA"`**, listed in `sources` as `"EXTRA": "Extras"`. Add it
to a question's or card's `src` *alongside its real source codes* to mark the item as lower-yield. The app (v0.7.1+)
hides extras by default, leaves them out of every count and total, and gives the pack one **Extras** switch, separate
from the source switches ("All on" does not touch it). With Extras on, an extra behaves like any other item, so it
still needs one of its real sources on. Use it for items that are correct and could help a keen learner but would not
earn a place in a block exam: peripheral detail, exact figures, historical or case narrative, and cards that mirror a
question in another format (§3b). Only questions and cards take `EXTRA`; guides, guide sections and drugs never do.
An item that should not exist — an exact duplicate, a joke or giveaway option, a disputable key, a wrong fact — is
removed or rewritten, never parked in Extras.
A pack built before source tables existed (no real codes) may still use Extras (v2.24): its `sources` is just
`{"EXTRA": "Extras"}`, only the extra items carry `src: ["EXTRA"]`, and every untagged item stays visible.

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

> **Option order does not matter — the app SHUFFLES options at render time** (app v0.2.62+), for both
> Practice/Exam questions and `mcq`/`multi` cards. So you may put the `correct` option at **any** index
> (placing it first is perfectly fine for readability); the learner never sees a fixed "always-A" pattern.
>
> **"All of the above" / "None of the above" are fine** (app v0.2.63+ **anchors** them — it pins them to
> their authored slot and shuffles only the other options around them, so they always read correctly; the
> "…of these" variants too). **What you must NOT write are position-REFERENCING options** — "A and B",
> "both of the above", "the first option", "Options 1 and 3" — because they point at *specific* other
> options, which move when the rest shuffle. Those can't be safely shuffled or anchored. Write such an
> option as a self-contained answer instead. (The §7a validator flags the position-referencing kind.)

### Card schema (spaced repetition) — six types, plus `label` (v2.27)

Cards carry `sys`, `topic`, `type`, `explain`, optional `guide`. **Cards do not take a difficulty**
(Review shows everything regardless of difficulty). The six types are unchanged from v1; a seventh,
**`label`** (an image with its printed labels masked), was added in v2.27 — see §6d:

**1. `mcq`** — single best answer (`correct` = index).
**2. `multi`** — select all (`correct` = array of indices).
**3. `cloze`** — `text` with `{{hidden}}` blanks; no `prompt`.
**4. `order`** — `items` in the **correct** order (app shuffles).
**5. `match`** — `pairs` of `[left, right]`.
**6. `qa`** — `prompt` + `answer` (+ optional `explain`).
**7. `label`** (v2.27, app v0.8.1+) — `prompt` + `image` (a data: URI) + `labels` = `[{x,y,w,h,t}]` (+ optional `explain`). §6d.

**8. `spot`** (v2.28, app v0.9.0+) — `prompt` + `widget` (a `widgets` id of type spot or lesion) + `target` (one of its region
ids) (+ optional `explain`): the learner taps the answer on the figure; auto-graded. §6e.

**9. `widget`** (v2.28, app v0.9.0+) — `prompt` + `widget` (any `widgets` id) + `answer` (+ optional `state`, `explain`): the
figure shown in a set state, self-graded like `qa`. §6e.

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

> **These are targets to actually hit — and they multiply by the weeks covered.** A common failure is
> shipping a thin pack (e.g. 15–20 cards) for a **multi-week** unit. If a pack spans 2 weeks, that's
> roughly **2× the questions and 2× the 40–60 cards** of a single week; for 3 weeks, 3×. Count what
> you produced against the weeks before delivering (the §7 checklist asks you to confirm this). A
> two-week pack with one tiny guide and 15 cards is not a complete pack.

> **Size by the material, not by a target (v2.23).** The numbers above are a starting point for an ordinary week,
> not a floor to beat. A dense week (lectures, several modules, a case session and a practice quiz) earns more — but
> only as many items as it has **examinable facts and skills**, weighted by how much the course stresses each (§3b).
> Never grow a pack to match an earlier pack's size, and never add items to fill out a guide: a dense week covered by
> ~100 questions and ~150 cards is better than 240 and 500 that test the same facts three ways. Items that are still
> worth keeping but lower-yield go to Extras (§2), outside the core the learner sees by default.

---

## 3a. Card-type mix — vary the retrieval, don't go cloze-heavy

The six card types exist so Review *mixes* recall styles. A common outside-LLM failure is making the deck
**mostly cloze** (fill-in-the-blank), which turns Review into rote text completion. Aim for a spread
roughly like:

- **qa** ~25–30% — short "what's the key distinction / why" prompts.
- **mcq** ~15–25% — single high-yield associations.
- **cloze** ~15–25% — definitions, numeric thresholds, criteria (**not** the majority of the deck).
- **multi** ~10–15% — "select all"; **every multi card needs at least one FALSE option** and a real
  discriminating choice. Do **not** mark all options correct, and do **not** fall into an "all true
  except the last one" habit — vary which/how many are correct.
- **order** ~10–15% — best for **algorithms / workups / sequences** (e.g. febrile-neutropenia steps,
  an approach to an abnormal CBC, a diagnostic pathway).
- **match** ~10–15% — for clean associations: disease→clue, drug→toxicity, disease→treatment,
  framework→purpose.

These are guides, not quotas — but if your deck is >35% cloze or has multi cards with no false option,
rebalance. (`mcq`/`multi` options are shuffled at render, so author them in any order; "all/none of the
above" are anchored and fine, but never use position-*referencing* options like "both of the above" — see §2.)

---

## 3b. One item per fact — curation rules (v2.23; rule 8 added v2.25)

These rules come from an audit of four week packs (2026-09-27) that had grown to two to five times the size of
earlier weeks. Their questions were mostly sound; about two thirds of their cards were not. Apply the rules while
writing, and check against them before shipping:

1. **One item per fact, weighted by emphasis.** A fact gets one question, and at most one card in a *different*
   retrieval style. A fact the course stresses repeatedly (the lecture, the module, the case and the quiz all return to
   it) can earn more — a second vignette from a different angle, a harder application — but a fact mentioned once gets
   one item. This is a judgement, not a count: how likely is it to be examined, and how much is it stressed?
2. **No mirror cards.** A card that restates a question's stem and answer in another format is not a new item. If the
   second format still helps, the card goes to Extras.
3. **No repeats across guides.** Case guides (CBL, ICE), integrated reviews and the cram sheet **apply** the week's
   concepts to the case or pull them together; they do not re-ask the concept guides' items. Before writing an item,
   check whether the pack already tests that fact.
4. **Not core items:** exact percentages, rates, hazard ratios and dose schedules where only the direction or rough
   size is examinable; dates, names, eponyms and history; a particular case patient's details (vitals, timeline) rather
   than the transferable concept; how a lecture, module or quiz is structured; common sense a medical student already
   has; soft-skill platitudes. Keep what aids understanding in the guide; as items these belong in Extras at most.
5. **Never ship:** exact duplicates (keep the better-formed copy), joke or giveaway options, keys a careful reader could
   dispute, `order` cards for things that are not a sequence, `match` cards with two identical answers or ambiguous
   pairs, cloze blanks on non-key words, and `qa` answers too long to recall. Rewrite them or leave them out.
6. **Correct over faithful.** When a slide or lecturer states something standard references contradict, the item
   teaches the standard answer and the guide carries a "Sources differ" callout (§4a); an item never keys the
   non-standard claim as the only right answer.
7. **Updates retire as well as add.** When new material arrives, check new items against the existing ones and move or
   remove the weaker copy instead of appending a second.
8. **Option length is not a cue (v2.25).** The right answer tends to be written with its qualifier ("…, plus at least one
   month of worry about further attacks") while the wrong ones are bare labels — so the longest option is the key, and a
   student who notices scores without knowing the material. Write every distractor at the key's level of specificity and
   length: give it its own qualifier, mechanism or consequence (plausible-sounding, still wrong per the explanation), and
   trim a key that over-explains (the explanation carries the detail). Vary which option is longest. Across a pack the key
   is the single longest option in about a quarter of core items — chance — never ≥ 1.5× its longest distractor, and not
   never-longest either, which is a cue the other way. Extend distractors rather than replacing them, so the explanation's
   "Not the others" stays true; the key's index, the concept and the explanation do not change.

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

> **If the upload contains numbered practice questions** (a study guide with "Question 1 … Answer 1 …",
> a quiz, a WFQ), **explicitly recognize them as source practice questions**, tell the user you're using
> them for topic emphasis/level/style, and then write **novel** vignettes that test the same concepts
> from new angles. Do **not** silently reproduce or lightly paraphrase them. The only exception is when
> the user *explicitly* asks for a faithful conversion of an existing question set — then say so and
> convert. Default = novel.

---

## 4a. The pack is study material, not a report on the sources

Everything the learner reads — guide bodies, `explain` text, stems, cards, the cram sheet — is **teaching
text**, written as if by the course. The sources shape the content; their **apparatus stays out**:

- **No source tags or locators.** No `[Lecture 2 · 14:45]`, `[Module 3 §6]`, `[std]`, `[WFQ Q7 key]`,
  no `slide 12`, no `m:ss` timestamps, no "tx" / transcript references. If you keep such tags in your own
  working notes while you read, strip them before anything goes into the pack.
- **No source reportage.** Not "the module states", "according to the lecture", "the key says", "keyed
  as". State the material: *"Lithium's therapeutic window is 0.6–1.2 mmol/L"*, not *"the module says the
  window is…"*. **Attribution only where it genuinely helps the learner**, in plain words and sparingly
  (a few per guide at most): *"Dr. Byrne stressed…"*, *"the practice quiz keys this as…"* — lecturer
  emphasis is worth passing on; provenance is not.
  **The frames that count as reportage** (v2.18) — a source as the subject or the frame of a sentence, in a
  body, an `explain`, a stem or a card: *the/this lecture, lecturer, deck, slide(s), module, transcript,
  handout, session, workshop, panel, quiz, manual* + *says, states, gives, lists, prints, keys, names, calls,
  puts, teaches, describes, defines, own, version, order, script, example*; *"as taught"*, *"as printed"*,
  *"verbatim"*, *"aloud"*, *"Dr. X's deck"*. Rewrites: *"The lecture rejects the category"* → *"The
  typical/atypical category does not hold up"*; *"What does the module say about cannabis?"* → *"What is the
  effect of cannabis in ADHD?"*; *"Put the strategies in the module's order"* → *"Put the strategies in order,
  from least to most disruptive"*; *"The lecture's deck turns that into a four-box algorithm"* → *"For
  refractory illness the algorithm has four steps."* The one exception is the **Sources differ** callout
  below, where sources are named by design.
- **Conflicts between sources are called out, not hidden — and adjudicated.** Lecturers misspeak, slides
  lag guidelines, module text and quiz keys disagree. When two sources differ on an **examinable fact** (a
  threshold, dose, duration, first-line choice, criterion count), the pack says so **explicitly**, in a
  short callout of a consistent form that opens with **Sources differ:** and gives (a) what each source
  says, (b) which is correct, **checked against an external reference and naming it** — DSM-5-TR, a current
  guideline, a standard textbook, a drug monograph — and (c) what to expect on the exam (usually the slide
  or key value, since that is what the examiner wrote). Example: *"Sources differ: the lecture gave 48 h;
  the slides and the CPS monograph give 12–36 h. Use 12–36 h; expect the slide value."* Do the external
  check before adjudicating; if nothing settles it, say both values remain in play. Questions and cards test
  the adjudicated value, and `explain` may carry the same one-line note. **What stays out is the
  working-notes apparatus** — no ⚠, no "CONFLICT" tags, no "source caution" labels, no "(standard
  knowledge, not in the module)"; the callout replaces them. If a standard fact is worth teaching, teach
  it plainly.
- **No meta text.** No orientation paragraphs ("this guide covers…", "how to use this guide"), no
  verification or "not captured / gaps in the export" blocks, no notes to the maintainer. Open with the
  content.
- **Declarative voice.** Statements of fact, as a textbook writes them. No reader address (*you, your, we,
  let's*) outside quoted interview or patient lines, no coaching imperatives (*"learn this", "don't be
  thrown", "worth memorizing"*), no rhetorical questions. Checklists and procedures may use the imperative
  mood (*"Assess airway first"*). Use the learner's spelling convention (Canadian for the default packs).
- **Warning boxes are for real clinical traps and "Sources differ" callouts** — *"don't confuse X with
  Y"*, or a genuine conflict adjudicated as above — a handful per guide, not decoration.

Before delivering, **scan for leaks** (the §7a validator does this): timestamps, bracketed tags, slide
references, "standard knowledge", "CONFLICT" and ⚠ should all be absent from reader-facing text — the
adjudicated "Sources differ" callouts are the only trace a conflict leaves.

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

## 5a. Absolute vs. relative difficulty — and customizing for the learner

The `easy`/`medium`/`hard` ratings in §5 are **relative**: they rank questions *within a pack* so the
app can filter Practice/Exam by difficulty. **Always apply them, on every question, for every learner**
— the app reads these tags, and an unrated question silently drops out of any filtered session.

Distinct from that is the pack's **absolute** difficulty — the overall level the whole easy→hard band
is pitched at. The defaults in this guide are tuned for **medical students**. Two controls sit on top
of that default:

**1. The learner can dial absolute difficulty up or down.** Ask who the pack is for. A resident
preparing for a **Royal College** exam (or any graduate/board exam) should get a pack shifted
**upward** — harder stems, more cross-system integration, subtler distractors, less hand-holding —
while a pre-clerkship student gets the default band. Honour an explicit request ("make these harder,
I'm studying for the Royal College"). **Crucially, still rate every question `easy`/`medium`/`hard`
relative to that shifted band**, so filtering keeps working: a "hard" item in a resident pack is
simply harder in absolute terms than a "hard" in a med-student pack. The rating is always *within-pack
relative*; the band it sits on is what moves.

**2. Auto-scale absolute difficulty from context** — even when not explicitly asked:

- **School / year / field** (the `school` / `year` / `course` fields, §2). Higher training levels are
  harder. In the default packs there is a deliberate **step up from Year 1 to Year 2**; a
  residency/fellowship context steps up further again.
- **The supplied materials themselves.** Mirror the level of the user's notes, slides, and especially
  any **example questions or past quizzes** they share — treat the difficulty of those items as a
  direct indicator of the absolute level to target (while still writing *novel* questions per §4).

**Format is customizable too.** The default is single-best-answer clinical vignettes, but the learner
can ask for a different mix — more cloze/short-answer, more "select all," image-anchored stems, longer
or shorter stems, etc. Honour the request while keeping each item valid per §7 and rated per §5.

When you're unsure where to pitch a pack, **ask**: *"Who's this for — what year/level — and how hard do
you want it?"*

---

## 6. Topic guides — **embedded in the pack** (no separate files)

Topic guides live **inside the pack** in the `guides` array; the app's **Topic Guides** reader
displays them with no external dependency. **Do not output standalone guide `.html` files** —
they are redundant now that guides ship inside the pack. (Only produce a standalone copy if the
user explicitly asks for a printable version.)

Even though no file is written, each guide still needs a **filename-style key** (e.g.
`Topic_C1_Arrhythmias.html`) — it's the identifier that links items to guides.

> **Guides must be substantive — one real guide per topic block.** The template below is a **minimum
> skeleton, not the target.** A guide that is one heading and a sentence (or a single sparse page for a
> whole week) wastes the feature and short-changes the learner. Aim for:
> - **One guide per concept, synthesized across sources.** A topic guide takes the high-level view of the
>   whole week and gathers **everything the week says about one concept, wherever it lives** — a segment
>   of a lecture, a self-learning module, the case session, the practice quiz — into one place. It is
>   **never a source unit under a topic title**: not one guide per lecture, per module or per session
>   (the two exceptions are clinical-skills sessions and pure anatomy, which stand alone naturally). Split
>   unrelated concepts rather than stapling them together (e.g. "Lymphadenopathy & Lymphoma" and
>   "Plasma-cell disorders" are two guides, not one "everything-lymphoid" guide), and give each a tight
>   title. **The count follows the week's concepts** — a dense one-week medical pack commonly lands at
>   8–15 guides; there is no cap, and fewer than ~6 usually means concepts have been merged.
> - **Real teaching content in each:** multiple `<h2>`/`<h3>` sections, at least one or two `<table>`s
>   or key-point boxes, covering the high-yield facts, mechanisms, associations, and "do-not-miss"
>   items of that concept. Match the depth of the source material; a guide should stand on its own as a
>   revision sheet. **Working band: 10,000–20,000 characters of HTML body per guide** (the maintainer's
>   packs average ~14k). Under ~5,000 is thin; if the material would run past ~20,000, select the
>   examinable content and split by concept rather than transcribing the source.
> - **Open with the content.** A one-sentence lede stating the topic is fine; orientation paragraphs and
>   reading instructions are not (§4a). Close with a take-home box (`key`) and, where real traps exist, a
>   short discriminators / exam-traps section.
>
> **Link items to their guide.** Most questions and cards should carry a `guide` pointer
> (`{ "f": …, "t": …, "s": … }`) into the relevant embedded guide so the app can deep-link from an
> item to the right section. A pack where few or no items reference a guide leaves the Topic Guides
> reader disconnected — wire them up as you write each item.

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

**What `guide.s` is — a label AND a best-effort deep-link.** The app does two things with `guide.s`:
it **displays** it to the learner ("Deeper detail: …") next to the Open-guide link, and it tries to
**scroll** the opened guide to the matching spot. The scroll is best-effort, in this order: (1) an
`id` anchor if you wrote `guide.f = "File.html#anchor"`; (2) otherwise the first heading whose text
**contains** `guide.s` (case-insensitive substring); (3) otherwise a text search/highlight of `guide.s`
in the body. So `guide.s` does **not** have to exactly equal a heading — but you get the best result by
making it a **short, heading-like cue** that actually appears in that guide (e.g. `"5 · HIT"` when the
guide has an `<h2>HIT</h2>`), rather than a long abstract micro-objective that matches nothing. Adding
`id="..."` to your `<h2>`/`<h3>` headings (and pointing `guide.f` at them) makes the jump exact.

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
<p>One-sentence lede stating the topic — content, not reading instructions.</p>
<h2 id="atrial-fibrillation">Atrial fibrillation — rate control</h2>
<div class="key">Irregularly irregular, no P waves → <b>beta-blocker</b> or non-DHP CCB first-line.</div>
```

Embed the full HTML (escaped for JSON) as the `html` value of the matching `guides[]` entry.

> **Sources inside a guide (v2.22).** Give every guide a `src` list (the sources it synthesizes). Where a section
> comes from a subset of them, put `data-src="CODE"` on its heading (`<h2 data-src="ICE">`, or `data-src="HSR CASP"`
> for several) so the app can hide just that section when those sources are switched off. Leave blended sections
> untagged. See §2 "Content sources".

---

## 6a. The cram sheet — the LAST guide in every pack

**Every pack ends with a one-page cram sheet** as the **final `guides[]` entry**: a dense, print-friendly
"night-before" rapid review of the pack's week(s). It is a regular guide (same `{file,title,html}` shape,
same dark-mode-safe CSS contract from §6) — just purpose-built as a single high-density revision page.

- **Title it** so it sorts last and reads clearly, e.g. `"Week 16 · 9 Cram Sheet"` (use the next number
  after your last topic guide). `file` key e.g. `"W16_09_Cram_Sheet.html"`.
- **Scope = this pack's week(s) only.** One sheet per pack (per week, or per the pack's week-range) — not
  one giant sheet for a whole course.
- **Structure** (mirror `Night-Before_Cram_Sheet.html`):
  - a top **"do-not-miss / emergency" strip** — the can't-miss diagnoses, red flags, and first actions;
  - then **compact multi-column topic cards**, each a tight list of **cue → answer** rows (the single
    highest-yield fact per line: classic clue → diagnosis/next step/drug). Think "everything you'd want
    on one page the night before," not prose.
- **CSS:** dark-mode-safe, variables only (§6) — the legacy standalone Night-Before sheet hardcodes hex;
  the embedded version must theme cleanly. A multi-column print layout (`column-count`) and a print button
  are encouraged but optional.
- **Linking:** items generally do **not** point their `guide` at the cram sheet (it's a review surface,
  not a section target) — keep wiring questions/cards to the substantive topic guides.

> Density over completeness: the cram sheet distills, it doesn't re-teach. If it reads like a paragraph,
> tighten it into cue → answer rows.

## 6c. Figures in topic guides (v2.26)

A guide may carry figures **only where a picture does work the text can't**: anatomy and its maps (dermatomes, the
skull base, the ventricles), imaging (what a finding looks like), circuits and pathways, curves (dose–effect, onset by
age), timelines and cycles. Clinical prose guides (interviewing, diagnosis, ethics, case write-ups) usually need none,
and a comparison the guide already makes in a table does not get a picture of the same table. Anatomy-heavy guides may
run well past a handful of figures; there is no fixed count, only "is this figure earning its place".

**Three kinds.**
- **Course images**: cropped from the week's slides, lab manual or handouts (allowed in packs), tight to the figure and
  its labels, never the slide title or footer. WebP at about 1,100 px wide, quality ~72 (typically 20–80 KB), embedded
  in the guide HTML as `<img src="data:image/webp;base64,…" alt="…">`. Re-crop or mask anything that doesn't belong
  (another figure's corner, a stray inset). Skip an image too low-resolution to read; draw it instead.
- **Drawn diagrams**: inline `<svg>` written by the author from the guide's own facts: real `<text>` labels, no
  embedded raster, colours from the guide's CSS variables (`var(--ink)`, `--soft`, `--line`, `--accent`, `--blue`,
  `--gold`, `--paper`) so they follow the app theme. Check them at phone width (~375 px): no overlapping labels, text
  no smaller than ~10 px at that width.
- **Drawn schematics** (v2.28, Alkarim 2026-10-02): a custom drawing of the **structure or mechanism itself**, not a chart
  about it — the medulla seen from behind with its tubercles, a hemisphere with the arterial territories and the body
  parts they serve, the visual pathway with numbered lesion sites beside the field each loses. Style: a simplified
  anatomical shape with smooth outlines, a few muted colour-coded parts, leader-line labels with a **bold name and a
  short gloss**, a one-line caption on how to read it, and numbered sites tied to a table where a lesion maps to a
  deficit. Look for these in every anatomy, pathway and mechanism section; they are also the best bases for §6e widgets.
  Reference examples: Drive `Claude/StudySuite/interactive-figures-2026-10-02/style-examples/`.

**Rules.**
1. **No identifiable patient.** No faces or names; crop out a patient photo that sits beside a figure. Anonymised
   scans and diagrams are fine.
2. **Every figure is a `<figure class="fig">` with a `<figcaption>`.** The caption restates what *this guide* already
   says (bold lead phrase, then one or two sentences) and ends with a one-line credit in a `<span class="cr">`
   (e.g. "Slide: Neuroanatomy I (Lisk)"). A caption never teaches a new fact and never reports the source (§4a), so
   no "the slide says"; a slide that prints an error is covered by the guide's "Sources differ" callout, not the caption.
3. **Self-contained and inert.** No `<script>`, no `on…=` handlers, no `src`/`href` to anything outside the pack.
   Images are `data:` URIs; links inside an SVG point only to `#ids` in the same document.
4. **Placement:** directly after the paragraph or table the figure illustrates.
5. **Size budget:** a figure-heavy week adds about 0.5–1 MB to its pack, which is a one-time download per device (packs never
   go through sync). Above ~1.5 MB of images in one pack, prefer drawn diagrams or fewer images.
6. **Items are untouched.** Adding figures to an existing pack is a version bump that changes guide HTML only;
   question and card ids stay as they are.

The figure CSS (frame, two-up `.pair` grid that stacks on phones, caption and SVG text classes) travels in a
`<style>` block at the top of the guide body. Reference implementation and crop tools: Drive
`Claude/StudySuite/images-pilot-2026-10-01/` (`figlib.py`, `autocrop.py`, `figs_all.py`, `figs_round2.py`).

---

## 6d. Images in questions and cards (v2.27)

Two ways an image can carry an item, both shown by app v0.8.1+.

**1. Image-anchored questions and cards.** A question `stem`, or a card `prompt`, `answer` or `explain`, may include
one image: `<img src="data:image/webp;base64,…" alt="Axial MRI at the level of the basal ganglia">`. The app fits it to
the card (full width, centred, capped in height on phones). Use it where the image *is* the question: "what does this
finding look like", "which structure is arrowed", a curve or an ECG strip. Rules:
- **The stem still stands alone in words.** Say what the image is ("An axial T2 MRI at the level of the basal
  ganglia shows a lesion at the arrow."); a reader without the picture, or with an older app, can still follow the item.
- **Data: URIs only** — no links to images anywhere else. WebP at about **700 px wide, quality ~60** (typically
  10–30 KB). Crop tight to what the item needs.
- **No identifiable patient** (as §6c): no faces or names; anonymised scans and diagrams are fine.
- Put the image in the field where it does its work: in the stem or prompt when it is the question, in the answer or
  explain when it shows the answer.
- Options stay text.

**2. The `label` card (label this structure).** A figure with its own printed labels covered by numbered masks. The
learner names each masked label, taps a mask to check that one, then **Show answer** uncovers all of them and lists the
labels in order; the card is self-graded like `qa` (Again / Hard / Good / Easy).

```json
{ "type":"label", "sys":"Neuro", "topic":"Basal ganglia",
  "prompt":"Name the labelled structures on this axial section.",
  "image":"data:image/webp;base64,…",
  "labels":[{"x":0.12,"y":0.30,"w":0.18,"h":0.05,"t":"Caudate nucleus (head)"},
            {"x":0.66,"y":0.41,"w":0.16,"h":0.05,"t":"Putamen"}],
  "explain":"The internal capsule separates the caudate from the lentiform nucleus.",
  "guide":{"f":"…","t":"…","s":"…"}, "src":["…"] }
```

- `x`, `y`, `w`, `h` are **fractions (0–1) of the image's width and height** for the box that covers the label
  **printed on the image** (its text, not the structure); `t` is that label's text. Boxes must stay on the image
  (x+w ≤ 1, y+h ≤ 1). The masks are drawn in percentages, so they stay on their labels at any screen size.
- Use a figure that already carries printed labels (a slide or atlas figure); the masks hide those labels. Pad each
  box slightly so no letter shows round the edge.
- **3–8 labels per card, one concept per card** (one section, one pathway, one region); a busy figure becomes two
  cards, not one card with fifteen masks.
- `t` is plain text with real characters, as in every plain field (§2). `explain` is optional.
- Same image rules as above: data: URI, ~700 px wide WebP ~q60, no identifiable patient.
- **Budget:** about **25 KB per image** and **about 1 MB of card images per pack**; the check warns above that.
- **Older apps (before v0.8.1) skip label cards** when they load the pack; nothing else in the pack is affected.

## 6e. Interactive figures (`widgets`) — v2.28

Where moving through, switching or tapping a figure teaches more than a still one, the pack carries the figure's
**data** in a top-level `widgets` object and the app (v0.9.0+) draws and drives it — in the guide (inside the
script-free guide frame) and in cards. **A pack never carries a script**: every SVG string is inert (no `<script>`, no
`on…=` handler, no `<foreignObject>`, href/src only to `#id` or a data:image URI).

```json
"widgets": {
  "w44_cordlevels": {"type":"stack", "vb":[410,186], "alt":"…", "axis":"Cord level",
                     "frames":[{"img":"data:image/webp;base64,…", "label":"Cervical cord", "note":"…", "mask":[[x,y,w,h]]}, …]},
  "w44_pathways":   {"type":"layers", "vb":[380,332], "alt":"…", "base":"<svg markup>",
                     "layers":[{"name":"DCML", "color":"var(--gold)", "svg":"…", "note":"…", "on":true}, …]},
  "w44_bgspot":     {"type":"spot", "vb":[400,320], "alt":"…", "base":"data:image/webp;base64,…",
                     "regions":[{"id":"put", "name":"Putamen", "info":"…", "d":"M…Z"}, …]},
  "w44_lesion":     {"type":"lesion", "vb":[380,400], "alt":"…", "base":"…",
                     "regions":[{"id":"mm", "name":"Left medial medulla", "circle":[124,202,6.5], "deficit":"…", "show":"<svg overlay>"}, …]},
  "w44_tremor":     {"type":"curves", "vb":[380,204], "alt":"…", "base":"…", "all":true,
                     "views":[{"name":"Rest tremor", "color":"var(--accent)", "svg":"…", "note":"…"}, …]}
}
```

| Type | What the learner does | Use it for |
|---|---|---|
| `stack` (slice scroller) | slider, swipe or ‹ › through frames; Labels switch hides printed labels via `mask` boxes | levels: cord or brainstem levels, an imaging series, a rotation |
| `layers` (layered figure) | switches each layer on and off; the last one switched on shows its `note` | pathways, territories, drug sites on one diagram |
| `spot` (tap-the-spot) | taps a region for its `name` + `info`; **Quiz me** asks for a region | structures on a section or a drawn schematic |
| `lesion` (lesion localiser) | taps a site for its `deficit` and an overlay (`show`) on a body or map; Quiz me gives the deficit, asks for the site | lesion → deficit, generator → sign, region → disorder |
| `curves` (switchable views) | segmented buttons between views; `all` adds an overlay of every view | before/after, normal vs disease, stages, step-throughs |

**Rules.**
1. **Coordinates:** every widget has `vb` `[W, H]`; all SVG, regions (`d` path, `poly` points or `circle` `[cx,cy,r]`) and
   masks use it. Draw at ~380 wide so 11.5 px text stays ≥ 10 px on a phone. Colour with the guide variables
   (`--ink`, `--soft`, `--accent`, `--gold`, `--green`, `--blue`, `--paper`) and the §6c drawing classes (`bx`, `t`,
   `ts`, `la`, `tint-a` …), which the app supplies in cards too. **Accent and blue are near-identical in Navy dark**:
   pair accent with gold or green, not blue. Check in both themes and at phone width.
2. **In a guide:** `<figure class="fig ssw" data-ssw="ID">` holding a still **poster** (`<img class="sswposter">` or
   `<svg class="sswposter">`, the default state) and a `<figcaption>` that restates the guide, says what to do with it,
   and carries the §6c credit. Older apps and print show the poster. A widget may appear in more than one guide.
3. **In cards:** `spot` for "tap the …" (structure, site, system); `widget` with a `state` for "what is this / what does
   this cause" — `state` may set `frame` (stack), `on` (layers, indices), `view` (curves), `region` (spot/lesion,
   a preset site), `labels:false` (hide printed labels), `hideName:true` (keep the frame/view/layer name hidden until
   the answer), `outlines`. The prompt still stands alone in words. Optional widget fields: `title`, `labels` (an SVG
   label overlay the Labels switch hides), `masks`, `maskFill`, `outlines`, `lead` / `ask` (lesion wording, e.g. a
   generator rather than a lesion).
4. **Facts** in names, notes, info and deficits restate the guide, in the reader-facing register (§4a): no source apparatus.
5. **Size:** a figure-heavy week's widgets typically come to 100–300 KB; the checks warn above 300 KB per widget or
   1.5 MB in total (then tap-to-load is the next step, not a smaller figure).
6. **App first:** a pack's first widget needs app v0.9.0 live before the pack is deployed (memory.md §1f). Older apps
   skip `spot` and `widget` cards and show the posters.

Reference implementation: Drive `Claude/StudySuite/interactive-figures-2026-10-02/` (`ssw_engine.js`, the test pack) and
`Claude/StudySuite/wk44-interactive-2026-10-02/` (Week 44: `w44_part1.py`, `w44_part2.py` with the smooth-outline
helper `S()`, `place_widgets.py`, `build_widgets.py`; cards in `wk44_content/frag_20.py`).

---

## 6f. Atlas packs (`atlas` widgets) — v2.29

An **atlas pack** is a reference pack, not an exam bank: one `atlas` widget (planes → levels → regions), a shared structure
registry, a few short reading guides and spot / widget cards; no questions, no drugs. The app (v0.10.0+) draws it as its
own full-width view (planes, a level slider with zones, tap a structure to name it, search, Labels / Territories /
Reference / Outlines / Anatomical view switches, pathway pills, Quiz me, a locator map, pinch zoom), as a launcher figure
in guides and as the widget behind cards. The reference implementation is **The Brain** (`neuro_brain`): builder and
drawing modules in Drive `Claude/StudySuite/brain-atlas/` (`build_atlas_test.py`, `atlas_draw.py`, `slices/`, `atlas_guides.py`,
`atlas_cards.py`, `HANDOFF.md`), engine `atlas_engine.js` (verbatim in the app). A cardiac or renal atlas follows the same data.

```json
"widgets": {"atlas": {"type":"atlas", "vb":[480,380], "title":"The Brain", "alt":"…",
  "systems":  {"bg":{"name":"Basal ganglia","color":"var(--gold)"}, …},
  "structures":{"put":{"name":"Putamen","gloss":"lateral part of the lentiform nucleus","sys":"bg","src":["MAPS-L2"],"weeks":[43],
                       "ref":{"f":"W43_20_….html","s":"The basal nuclei"}}, …},
  "pathways": {"cst":{"name":"Corticospinal","color":"var(--gold)","note":"…","src":["MAPS-L3"]}, …},
  "maps":     {"spine":{"vb":[240,400],"svg":"…","name":"Side view, head to sacrum"}, "axial":{…}},
  "planes":   [{"id":"ax","name":"Axial","zones":[{"name":"Whole head","from":0},{"name":"Brainstem","from":5}],
               "levels":[{"id":"ax02","label":"Frontal horns and thalami","note":"…","conv":"rad","marks":{"t":"ANTERIOR","b":"POSTERIOR"},
                          "svg":"…", "regions":[{"id":"put","d":"M…Z"}, …],
                          "labels":[{"x":112,"y":153,"a":"end","n":"Head of caudate","g":null,"t":[227,126],"s":"caudh"}, …],
                          "terr":"…", "terrKey":[["Anterior cerebral","green"],["Middle cerebral","gold"], …],
                          "paths":{"cst":"…"}, "ref":{"img":"data:image/webp;base64,…","credit":"Lab 11 (Week 43) p2 — … — course material","conv":"rad","box":[94,98,291,153],"printedLabels":false},
                          "loc":{"map":"spine","line":[24,100,196,100],"pov":"s"}, "src":["MAPS-L2","MAPS-Lab11"]}, …]}, …]}}
```

**Rules.**
1. **One id per structure across the whole atlas**, every region id in `structures`, every `label.s` a region of its level.
   The app cycles a structure through every slice it appears on ("on N slices"), so a duplicate id per zone breaks that.
   Names are unified across modules; the builder's lints list duplicate-name groups and regions no label is akin to (how
   four wrong-name collisions were found in v1.5).
2. **Every label names its region** (`s`); a structure drawn only as a line (a sulcus, nerve, artery, root) gets a
   tappable **hit ribbon** (`hit()` in `atlas_draw.py`: a closed path either side of the polyline, drawn invisible) so it can
   be tapped and its label lights. The app (v0.10.7) lights a label when its region is tapped, picked or quiz-revealed.
3. **Conventions:** `conv` is `rad` (radiological; the app can mirror it to anatomical and swaps the side marks) or `none`
   (surface views and the sagittal: the `marks` banner states the orientation on every width). Symmetric course drawings
   used as references get `conv: "rad"` so the app never mirrors them against the drawing.
4. **References are course images only** (§6c), cropped tight, printed labels masked or `printedLabels: true`, each with a
   credit that names the source and says *approximate* when the figure is a different cut or an oblique view; the `box`
   is the viewBox rectangle the drawing was traced in (one transform per view = its box, so drawing and figure cannot
   drift). Max side 540–640 px, WebP q 60–64: a 26-level atlas with every reference is ~1.4 MB and the line is 1.5 MB;
   write the pack compact (no indent).
5. **Territories** (`terr`, an SVG string) come with **`terrKey`** `[[name, colourVar], …]` — the app shows it as a legend
   while Territories is on. One convention per atlas (The Brain: ACA green · MCA gold · PCA accent · lenticulostriate red ·
   anterior choroidal blue · paramedian accent · circumferential gold · posterior spinal green).
6. **Locator:** every level has `loc = {map, line|dot, pov}` on one of the pack's `maps` (dedicated drawings, so slices can
   be redrawn without moving a beacon); `pov` (n/s/e/w) is the side the viewer stands on and draws the eye and field-of-view
   cone. Keep a zone's beacons in order and at the heights of the levels they contain.
7. **Read more:** a structure's `ref {f, s}` names the week-guide file and a substring of the section heading that teaches
   it; the app (v0.10.10) adds "Read: Week 43 · 20 — The basal nuclei ›" to the readout and opens that section across packs
   (plain text when that week's pack is not in the library). Verify every section string against the live guide headings.
8. **Guides inside the atlas pack** are short reading guides (3–6k chars) that restate the week guides and never add
   facts; their figures `<figure class="fig ssw" data-ssw="atlas" data-ssw-state='{"plane":"ax","level":"ax08","terr":true}'>`
   open the atlas at that state (app v0.10.8; a `#ssw=atlas:plane:level` anchor on a card's `guide.f` still wins). The
   pack's `sources` table must cover every `src` code used by structures, levels, guides and cards.
9. **Links from any pack's guide:** `<a href="#" data-atlas="ax:ax08">See it in the atlas: … →</a>` opens the atlas at
   that state (flags joined by `+`: `terr`, `nolab`, `ref`, `ana`, pathway ids — `ax:ax12:cst+dcml`; or a JSON state);
   app v0.10.9. When the atlas pack is not in the library the link offers to add it and then opens the view; outside the
   user's codes it says so. Put one line at the end of the sections that teach what a slice shows; nothing else changes.
10. **Cards:** `spot` with `state {plane, level}` and a `target` that is a region of that level; `widget` with
    `state {plane, level, labels:false, hideName:true}` for "which level is this?", `{region, labels:false}` for "what is
    marked?", `{terr:true, region, labels:false}` for "which artery?"; `conv:"ana"`, `paths:[…]`, `ref:1|2` as needed. Ids
    never change (review progress is keyed by them). About two per section level plus the level and territory sets.
11. **Checks:** `packbuilder.py` knows the type (shape, registered ids, per-level regions for cards, `label.s` on the level,
    `loc`, `conv`); `pack_checks.py` strips `#ssw=` anchors and allows an atlas widget up to 1.5 MB; the atlas builder adds
    its own lints (unresolved labels, akin-less regions, long labels > 26 chars, > 14 labels per gutter side, territories
    without a legend) and hard asserts (version, conv, loc, ids) before writing. Verify each zone against its references
    (render with the real engine over the reference at its box) and have an independent verifier read the overlays
    against the named course figures before a zone is frozen; Alkarim reviews by use.
12. **App first** (memory.md §1f): a pack feature that needs engine support ships after the app version that reads it;
    older apps ignore unknown fields (`s`, `terrKey`, `ref`, `data-ssw-state`, `data-atlas`) harmlessly.

---

## 6b. Pharmacology data (`drugs[]`) — optional, powers the Pharmacology view

A pack **may** include a top-level **`drugs[]`** array. It is **optional and additive** — leave it out and nothing changes; include it and the app's **Pharmacology** view builds a cross-week **drug index** from every *enabled* pack: it merges your records (and those from other packs) by a stable id, groups them by drug class, and shows the faceted fields with a link back to the week/guide each fact came from. This is content the learner reads alongside the topic guides — only add it when your source material actually covers the drug.

**One record per drug, per pack:**

```json
{
  "id": "rx:metformin",
  "name": "Metformin",
  "class": "rx:biguanide",
  "facets": {
    "use": "First-line type 2 diabetes",
    "mechanism": "↓ hepatic gluconeogenesis; ↑ peripheral glucose uptake",
    "adverse": "GI upset, B12 malabsorption; rare lactic acidosis",
    "contra": "eGFR < 30",
    "monitoring": "Renal function, B12"
  },
  "ref": { "week": "Week 25 · Endocrine I", "topic": "Biguanide", "guide": "W25_02_Diabetes_Pharmacology.html" }
}
```

| field | required | notes |
|---|---|---|
| `id` | yes | A **stable identifier**, `rx:<slug>`, where `<slug>` is the lowercase generic name, hyphenated — e.g. `rx:metformin`, `rx:atorvastatin`, `rx:piperacillin-tazobactam`. **Use the SAME id for the same drug in every pack** — that is exactly how the app merges, say, a statin mentioned in three weeks into ONE growing entry instead of three duplicates. Don't mint a new id for a drug that plausibly already has one. |
| `name` | yes | Human-readable display name (e.g. `"Metformin"`). |
| `class` | optional | A class id, `rx:<class-slug>` (e.g. `rx:statin`, `rx:ace-inhibitor`, `rx:sglt2-inhibitor`). The view groups drugs under their class heading. Use a consistent slug across packs; omit if no sensible class. |
| `facets` | yes | An object holding **only the facets your source covers**, drawn from exactly these keys: `use`, `mechanism`, `dosing`, `contra` (cautions/contraindications), `adverse` (adverse effects), `monitoring`, `interactions`. Each value is a **short phrase, not prose** — it renders as a faceted line. Include 2–5 of them; never invent keys. |
| `ref` | yes | `{ week, topic, guide }` locating the source so the view can link back. `week` = this pack's `weeks` string (or just the week label); `topic` = the **one** topic/section it's mainly taught under — **short (≤40 characters), never several topics joined with " / "** (it shows as a small source tag on the drug's lines); **`guide` MUST equal the `file` of a real `guides[]` entry** in this pack — the app deep-links to it. |

**Rules of thumb:**

- **Reuse ids; that's the whole point.** Consistent `rx:` ids across weeks are what let the index consolidate a drug; inconsistent ids produce duplicates. Pick the obvious generic-name slug.
- **One record per drug per pack** — don't repeat the same drug twice in one pack's `drugs[]`. (Across *different* packs is expected and is what merges.)
- **Short facet values.** Aim for the one high-yield phrase per facet, like the cram sheet's cue→answer density — not a paragraph.
- **`src` (v2.22).** When the pack has `sources`, each drug lists the codes its facets came from (`"src": ["L1-L2", "ICE"]`); the Pharmacology view hides a drug only when all of them are off.
- **One short `ref.topic`.** A drug drawn from several topics still names just the main one — e.g. `"GAD"`, not
  `"Social anxiety disorder / GAD / OCD / PTSD / Depression"`. The tag sits beside every facet line; a long one crowds
  the card, especially on a phone.
- **Point `ref.guide` at the guide that teaches the drug** (often a pharmacology or therapeutics guide). If you have no matching guide, you can still include the drug, but a valid `guide` file makes the back-link work.
- **It's optional.** If the week isn't drug-heavy, skip `drugs[]` entirely — an empty or absent array is fine and never fails validation.

---

## 7. Validation checklist (every item must pass)

**Pack:** JSON object with `id`, `pack`, `formatVersion`, `version`, `author`; `questions` and/or
`cards` arrays; a `guides` array if any item carries a `guide`.

**Each question:** `sys`, `topic`, `stem`; `options.length ≥ 2`; `correct` integer in
`0…options.length-1`; **`difficulty` ∈ {easy, medium, hard}**.

**Each card:** `sys`, `topic`, valid `type`; and by type — `mcq` (`prompt`, `options` ≥2, `correct`
in range) · `multi` (`prompt`, `options`, `correct` = array of in-range indices) · `cloze` (`text`
with ≥1 `{{…}}`) · `order` (`prompt`, `items` ≥2, in correct order) · `match` (`prompt`, `pairs` of
2-element arrays) · `qa` (`prompt`, `answer`) · `label` (`prompt`, `image` = data:image URI, `labels` = non-empty
`[{x,y,w,h,t}]` with x, y, w, h in 0–1, x+w ≤ 1, y+h ≤ 1, non-empty `t`; §6d). All non-`cloze` cards need a `prompt`.

**Item images (v2.27):** only `data:image/(webp|jpeg|png);base64` sources in a stem, prompt, answer or explain; no
script, handler or outside resource; the stem reads on its own; ~25 KB per image, ~1 MB of card images per pack.

**Guides:** every `guide.f` used anywhere **resolves to a `guides[]` entry whose `file` matches**;
every `guides[]` entry has non-empty `html`; **the LAST guide is the pack's cram sheet (§6a)**.

**JSON hygiene:** 0-based `correct`; straight quotes; HTML inside strings must keep the JSON valid
(escape inner double quotes, or use single quotes inside the HTML).

**Parses cleanly (do this last):** the whole file is valid JSON with **no prose, code fences, or
citation/grounding/footnote markers** anywhere (see §8). Run it through a strict JSON parser — if it
doesn't parse, it won't import.

**Substance & coverage:** **topic guides synthesized by concept (§6) — typically 8–15 for a dense one-week
pack, each 10,000–20,000 characters of body** (not a single sparse page, not one guide per lecture),
**`groupBy:"topic"` with real topics — ~8–20 for a dense week, each spanning ≥3 items (§2, v2.19)**,
each with real sections/tables, **plus a cram sheet as the final guide (§6a)**; **most questions
and cards carry a `guide` pointer**; counts are **sized by the examinable material, scaled to the weeks covered and curated per §3b** (one item
per fact weighted by emphasis, no mirror cards, no repeats across guides, lower-yield items in Extras); difficulty lands near the **~30 / 55 / 15** easy/medium/hard
spread, with **hard** items testing contraindications, emergencies, or multi-step reasoning (not just
longer stems).

**Options & card mix:** **option length is not a cue (§3b.8):** distractors as specific and as long as the key, the key
the single longest option in ~25% of core items and never ≥ 1.5× its longest distractor; **no position-REFERENCING options** anywhere (`"A and B"`, `"both of the above"`,
`"the first option"`) — options are shuffled at render (§2). ("All/None of the above" are fine — the app
anchors them.) Card types are **varied, not cloze-heavy** (cloze ≲ 35% of the deck); **every `multi` card
has ≥1 false option** (never all-correct, never "all-but-the-last").

**Reader-facing text & plain fields (§2, §4a):** no HTML entities in any plain-text field (stems, options,
topics, card fields, guide titles); no source tags, timestamps, slide numbers, reportage ("the module
says"), working-notes conflict markers or meta/orientation text anywhere the learner reads; declarative
voice with no reader address outside quoted dialogue. **Every conflict between sources on an examinable
fact is called out in a "Sources differ" callout, adjudicated against a named external reference (§4a).**
Run the §7a leak and entity lints and clear them.

**Placement filled:** `school` / `year` / (`term`) / `course` / `weeks` are present (ask if unknown,
don't leave null), and **`course` is the grouping block shared across weeks (e.g. a course/block name),
not the week's subject.**

**Extras (v2.23):** lower-yield questions and cards carry `EXTRA` beside their real codes and the pack lists
`"EXTRA": "Extras"`; no guide, section or drug carries it; no exact duplicate, joke option or disputable key is in the
pack at all, in the core or in Extras (§3b).

**Figures (v2.26):** every figure has a caption restating the guide and a credit; no identifiable patient; no script,
handler or outside resource in any guide; guide length is judged on the text without figures; drawn diagrams checked at
phone width.

**Content sources (v2.22):** if the pack has `sources`, every question, card, guide and drug carries `src` with
codes from that table; `data-src` headings use only codes from their guide's `src`; the cram sheet and any
integrated review carry every code. Run the §7a source lints and clear them.

---

## 7a. Build it programmatically and validate — don't hand-write the JSON

**If you can execute code (a Python sandbox, code interpreter, notebook, etc.), build the pack with a
script instead of typing JSON by hand.** Hand-authored JSON is where the import-breaking failures come
from — and they are *silent*: the user drags in the file and nothing appears, with no error. The most
common ones are mechanical and 100% catchable:

- **Minified / single-line JSON** that's unreadable and hard to diff → fixed by pretty-printing.
- **Citation / grounding / footnote markers your tooling injects** — `start_span`/`end_span`, `【…】`,
  ` ``` ` fences, `[1]`-style cites — **a single one makes the whole file fail to parse** (see §8).
- **Broken `correct` indices, unbalanced `{{…}}` cloze blanks, a `guide.f` that matches no guide, a
  question with no `difficulty`** — each silently breaks or degrades the pack.

The workflow that prevents all of the above:

1. **Assemble** the pack as a native data structure (lists/dicts), not a hand-typed string.
2. **Validate** it against the §7 checklist *and* scan for the §8 artifacts.
3. **Pretty-print** with 1- or 2-space indentation.
4. **Refuse to write/deliver** if validation fails — fix, then re-run.

> A validator checks **structure, not substance.** It cannot tell you a guide is too thin or a question
> is one shallow line — that's on the content instructions in §3 (volume) and §6 (substantive guides).
> Treat the heuristic warnings below (guide length, explanation length, counts) as nudges, not a pass.

### Reference validator (Python — copy, adapt, run)

```python
import json, re
from collections import Counter

CARD_TYPES = {"mcq", "multi", "cloze", "order", "match", "qa", "label", "spot", "widget"}   # label: v2.27 (§6d); spot, widget: v2.28 (§6e)
ARTIFACTS  = ["start_span", "end_span", "【", "】", "```"]  # any of these breaks the import
# §2: these fields are rendered as PLAIN TEXT — an HTML entity in them shows literally to the learner.
ENTITY     = re.compile(r"&(#\d+|#x[0-9a-fA-F]+|[a-zA-Z]+);")
# §6c (v2.26): figures are measured out of the guide length and must be inert and self-contained.
FIGS       = re.compile(r"<figure\b.*?</figure>|<svg\b.*?</svg>|<style\b.*?</style>", re.S)
EXT_SRC    = re.compile(r"""(?<![\w:-])(?:src|href|xlink:href)\s*=\s*["'](?!data:image/(?:webp|jpeg|png|svg\+xml);base64,|#)[^"']+["']""", re.I)
# §6d (v2.27): images inside items — data:image webp/jpeg/png only; the base64 is not text, so it is stripped
# before the entity and leak checks.
ITEM_IMG   = re.compile(r"<img\b[^>]*>", re.I)
DATA_IMG   = re.compile(r"^data:image/(?:webp|jpeg|png);base64,[A-Za-z0-9+/=]+$")
IMG_FIELDS = ("stem", "prompt", "answer", "explain")
PLAIN_Q    = ("stem", "topic")                       # + every string in options[]
PLAIN_C    = ("prompt", "answer", "text", "topic")   # + options[], items[], pairs[][]
# §4a: source apparatus that must not reach reader-facing text (warn — a clinical "14:00" can be legit).
LEAK       = re.compile(r"\b\d{1,2}:\d{2}\b|\bslides? \d+\b|\[[A-Z][^\]]{1,40}\]|standard knowledge|"
                        r"(?-i:CONFLICT|⚠)|\bkeyed as\b|\bas (taught|printed)\b|"
                        # v2.18: source reportage frames (§4a) — a "Sources differ" callout is the only legitimate hit
                        r"\b(the|this) (module|lecture|lecturer|deck|slides?|transcript|handout|session|workshop|panel|quiz|manual)(?:'s)? "
                        r"(says|states|keys|gives|lists|prints|calls|names|puts|adds|teaches|describes|presents|defines|own|version|answer|order|script|example|feedback|quiz)\b", re.I)
# ("CONFLICT" stays case-sensitive: lower-case "conflict" is ordinary prose, e.g. intrapsychic conflict.)
# §4a: reader address / coaching outside quoted dialogue (warn when frequent). Guides that quote interview
# scripts legitimately trip this — read the hits before rewriting.
ADDRESS    = re.compile(r"\b(you|your|you're|let's|we|we're|our)\b", re.I)

def _strings(v):
    if isinstance(v, str): yield ITEM_IMG.sub(" ", v)   # §6d: an item image is not text
    elif isinstance(v, list):
        for x in v: yield from _strings(x)

def _item_img_errs(xid, x):
    """§6d (v2.27): inline images in stem/prompt/answer/explain — data: webp/jpeg/png only, inert."""
    out = []
    for fld in IMG_FIELDS:
        s = x.get(fld)
        if not isinstance(s, str) or "<" not in s: continue
        if re.search(r"<script\b", s, re.I) or re.search(r"\son[a-z]+\s*=", s, re.I):
            out.append(f"{xid}: script or inline handler in {fld!r} (§6d)")
        if EXT_SRC.search(s): out.append(f"{xid}: {fld!r} loads an outside resource (§6d)")
        for tag in ITEM_IMG.findall(s):
            m = re.search(r"""\bsrc\s*=\s*["']([^"']*)["']""", tag, re.I)
            if not m or not DATA_IMG.match(m.group(1)): out.append(f"{xid}: <img> in {fld!r} must be a data:image webp/jpeg/png (§6d)")
    return out

def _label_errs(cid, c):
    """§6d (v2.27): label card — image is a data: URI; labels are boxes on the image (fractions) with text."""
    out = []
    if not (isinstance(c.get("image"), str) and DATA_IMG.match(c["image"])):
        out.append(f"{cid}: label image must be a data:image webp/jpeg/png URI (§6d)")
    labels = c.get("labels")
    if not isinstance(labels, list) or not labels:
        return out + [f"{cid}: label needs a non-empty labels[] (§6d)"]
    num = lambda v: isinstance(v, (int, float)) and not isinstance(v, bool) and 0 <= v <= 1
    for i, l in enumerate(labels, 1):
        if not isinstance(l, dict) or not all(num(l.get(k)) for k in ("x", "y", "w", "h")):
            out.append(f"{cid}: label {i} needs numeric x,y,w,h in [0,1] (§6d)"); continue
        if l["x"] + l["w"] > 1.001 or l["y"] + l["h"] > 1.001: out.append(f"{cid}: label {i} box runs off the image (§6d)")
        if not (isinstance(l.get("t"), str) and l["t"].strip()): out.append(f"{cid}: label {i} needs text t (§6d)")
        elif ENTITY.search(l["t"]): out.append(f"{cid}: HTML entity in label {i} text — use real characters (§2)")
    return out
# Options that REFERENCE specific other options break when the app shuffles. ("All/None of the above|
# these" are NOT flagged — the app v0.2.63+ anchors them to their slot.) Conservative on purpose, so it
# won't false-positive on legit answers like "Hepatitis A and B" or "Vitamin A and D".
POSITIONAL = re.compile(r"\b(both of the above|neither of the above|both of these|"
                        r"(option|answer)s? [a-e0-9]\b|"
                        r"the (first|second|third|fourth|last) (option|answer|choice))", re.I)

def validate_pack(pack):
    errs, warns = [], []
    for k in ("pack", "id", "formatVersion", "version"):
        if not pack.get(k): errs.append(f"missing top-level field: {k}")
    qs, cs, gs = pack.get("questions", []), pack.get("cards", []), pack.get("guides", [])
    gfiles = {g.get("file") for g in gs}

    ids = [x.get("id") for x in qs + cs if x.get("id")]
    for i, n in Counter(ids).items():
        if n > 1: errs.append(f"duplicate id: {i} (x{n})")

    for q in qs:
        qid = q.get("id", "?")
        if not all(q.get(k) for k in ("sys", "topic", "stem")): errs.append(f"{qid}: missing sys/topic/stem")
        opts = q.get("options", [])
        if len(opts) < 2 or not isinstance(q.get("correct"), int) or not (0 <= q.get("correct", -1) < len(opts)):
            errs.append(f"{qid}: bad options/correct")
        if q.get("difficulty") not in ("easy", "medium", "hard"):
            errs.append(f"{qid}: difficulty must be easy/medium/hard")
        if q.get("guide", {}).get("f") and q["guide"]["f"] not in gfiles:
            errs.append(f"{qid}: guide.f '{q['guide']['f']}' matches no guide")
        if len(q.get("explain", "")) < 80: warns.append(f"{qid}: explanation looks thin")
        for fld in PLAIN_Q + ("options",):
            for s in _strings(q.get(fld)):
                if ENTITY.search(s): errs.append(f"{qid}: HTML entity in plain-text field {fld!r} — use real characters (§2)")
        for fld in ("stem", "explain", "options"):
            for s in _strings(q.get(fld)):
                m = LEAK.search(s)
                if m: warns.append(f"{qid}: source apparatus in {fld!r}: {m.group(0)!r} (§4a)")
        for o in opts:
            if isinstance(o, str) and POSITIONAL.search(o):
                warns.append(f"{qid}: position-dependent option {o!r} — reword (options are shuffled)")
        errs += _item_img_errs(qid, q)

    for c in cs:
        cid, t = c.get("id", "?"), c.get("type")
        if t not in CARD_TYPES: errs.append(f"{cid}: bad card type {t!r}"); continue
        if "difficulty" in c: errs.append(f"{cid}: cards must NOT carry difficulty")
        if t in {"mcq", "multi", "order", "match", "qa", "label"} and not c.get("prompt"):
            errs.append(f"{cid}: {t} needs a prompt")
        if t == "mcq" and not (0 <= c.get("correct", -1) < len(c.get("options", []))):
            errs.append(f"{cid}: bad mcq correct")
        if t == "multi" and not (isinstance(c.get("correct"), list)
                                 and all(0 <= i < len(c.get("options", [])) for i in c.get("correct", []))):
            errs.append(f"{cid}: bad multi correct")
        if t == "multi" and isinstance(c.get("correct"), list) and len(c["correct"]) >= len(c.get("options", [])):
            warns.append(f"{cid}: multi marks ALL options correct — add a false option")
        if t in {"mcq", "multi"}:
            for o in c.get("options", []):
                if isinstance(o, str) and POSITIONAL.search(o):
                    warns.append(f"{cid}: position-dependent option {o!r} — reword (options are shuffled)")
        if t == "cloze":
            txt = c.get("text", "")
            if txt.count("{{") != txt.count("}}") or txt.count("{{") == 0:
                errs.append(f"{cid}: unbalanced/empty cloze blanks")
        if t == "qa" and not c.get("answer"): errs.append(f"{cid}: qa needs an answer")
        if t == "label": errs += _label_errs(cid, c)
        if t in {"spot", "widget"}:  # v2.28 (§6e): the figure must be in the pack; packbuilder checks the rest
            w = (pack.get("widgets") or {}).get(c.get("widget"))
            if not c.get("prompt"): errs.append(f"{cid}: {t} needs a prompt")
            if not w: errs.append(f"{cid}: {t} names widget {c.get('widget')!r}, which is not in the pack's widgets (§6e)")
            elif t == "spot" and c.get("target") not in {r.get("id") for r in w.get("regions", [])}:
                errs.append(f"{cid}: spot target {c.get('target')!r} is not a region of {c.get('widget')!r} (§6e)")
            if t == "widget" and not c.get("answer"): errs.append(f"{cid}: widget card needs an answer (§6e)")
        errs += _item_img_errs(cid, c)
        if t == "match" and (not c.get("pairs") or any(len(p) != 2 for p in c.get("pairs", []))):
            errs.append(f"{cid}: match pairs must be 2-element arrays")
        if c.get("guide", {}).get("f") and c["guide"]["f"] not in gfiles:
            errs.append(f"{cid}: guide.f '{c['guide']['f']}' matches no guide")
        for fld in PLAIN_C + ("options", "items", "pairs"):
            for s in _strings(c.get(fld)):
                if ENTITY.search(s): errs.append(f"{cid}: HTML entity in plain-text field {fld!r} — use real characters (§2)")
                m = LEAK.search(s)
                if m: warns.append(f"{cid}: source apparatus in {fld!r}: {m.group(0)!r} (§4a)")

    for g in gs:
        gid, h = g.get("file"), g.get("html") or ""
        if ENTITY.search(g.get("title") or ""): errs.append(f"guide {gid}: HTML entity in title — titles are plain text (§2)")
        if not h: errs.append(f"guide {gid}: empty html"); continue
        is_cram = "cram" in (g.get("title", "") or "").lower()
        fbody = re.sub(r"^.*?</style>", "", h, count=1, flags=re.S)        # skip the template head (font @import)
        if re.search(r"<script\b", fbody, re.I) or re.search(r"\son[a-z]+\s*=", fbody, re.I):
            errs.append(f"guide {gid}: script or inline handler in the guide (§6c)")
        if EXT_SRC.search(fbody): errs.append(f"guide {gid}: loads an outside resource (§6c)")
        h = FIGS.sub("", h)                                                  # length and leak checks read text only
        if len(h) < 1500: warns.append(f"guide {gid}: looks thin (<1500 chars)")
        elif len(h) < 5000 and not is_cram: warns.append(f"guide {gid}: thin ({len(h)} chars; working band 10–20k, §6)")
        elif len(h) > 22000 and not is_cram: warns.append(f"guide {gid}: very long ({len(h)} chars) — select examinable content or split by concept (§6)")
        body = re.sub(r"<style>.*?</style>", "", h, flags=re.S)
        leaks = [m.group(0) for m in LEAK.finditer(body)]
        if leaks: warns.append(f"guide {gid}: source apparatus leaked: {sorted(set(leaks))[:6]} (§4a)")
        text = re.sub(r"<[^>]+>", " ", body)
        words = max(1, len(text.split()))
        addr = len(ADDRESS.findall(text))
        if addr / words > 0.004: warns.append(f"guide {gid}: reader address {addr}x in {words} words — declarative voice (§4a)")
    if gs and not any("cram" in (g.get("title","") or "").lower() for g in gs):
        warns.append("no cram-sheet guide — every pack should end with a one-page cram sheet (§6a)")

    blob = json.dumps(pack, ensure_ascii=False)
    hit = [m for m in ARTIFACTS if m in blob]
    if hit: errs.append(f"citation/markdown artifacts present: {hit}")

    types = Counter(c.get("type") for c in cs)
    if cs and types.get("cloze", 0) > 0.35 * len(cs):
        warns.append(f"cloze cards {types['cloze']}/{len(cs)} (>35%) — vary the card mix (more qa/mcq/order)")
    if len(gs) < 6:
        warns.append(f"only {len(gs)} guide(s) — a dense one-week pack usually has 8–15 concept guides (§6); check for merged concepts")

    # §2 (v2.19): topics are real topics. Fragmented topics (mostly one item each) defeat the topic picker and weighting.
    tc = Counter(x.get("topic") for x in qs + cs if x.get("topic"))
    if pack.get("groupBy") != "topic":
        warns.append("no groupBy:'topic' — new packs and updates declare real topics (§2, v2.19); the app will group by guide")
    elif tc and sum(1 for v in tc.values() if v == 1) / len(tc) > 0.5:
        warns.append(f"groupBy 'topic' but {sum(1 for v in tc.values() if v == 1)}/{len(tc)} topics hold one item — "
                     "consolidate into real topics (§2); the app falls back to guide grouping")

    # §6b (v2.21): a drug's ref.topic is a small source tag — one short topic, never a joined list.
    for d in pack.get("drugs", []) or []:
        t = ((d.get("ref") or {}).get("topic") or "")
        if len(t) > 40 or " / " in t:
            warns.append(f"drug {d.get('id')}: ref.topic too long or joined ({len(t)} chars) — name the one main topic (§6b)")

    # §2 (v2.22): content sources — src codes on items/guides/drugs, data-src on guide headings.
    srcs = pack.get("sources") or {}
    if srcs:
        ds = pack.get("drugs", []) or []
        nosrc = [x.get("id") or x.get("file") for x in qs + cs + gs + ds if not x.get("src")]
        if set(srcs) == {"EXTRA"}: nosrc = []  # v2.24: a pack without source codes may carry Extras only
        if nosrc: warns.append(f"{len(nosrc)} items/guides/drugs carry no src (first: {nosrc[:3]}) — tag every one (§2)")
        unknown = sorted({c for x in qs + cs + gs + ds for c in (x.get("src") or []) if c not in srcs})
        if unknown: errs.append(f"src codes not in sources: {unknown}")
        for g in gs:
            codes = {c for m in re.finditer(r'<h[1-6][^>]*\sdata-src="([^"]*)"', g.get("html", "")) for c in m.group(1).split()}
            bad = sorted(codes - set(g.get("src") or []))
            if bad: warns.append(f"guide {g.get('file')}: data-src codes outside the guide's src: {bad}")
        # §2 / §3b (v2.23): Extras — the reserved code EXTRA marks lower-yield questions and cards.
        if "EXTRA" in srcs:
            xs = [x for x in qs + cs if "EXTRA" in (x.get("src") or [])]
            only = [] if set(srcs) == {"EXTRA"} else [x.get("id") for x in xs if not [c for c in x.get("src") if c != "EXTRA"]]
            if only: warns.append(f"{len(only)} items carry only EXTRA (first: {only[:3]}) — keep their real source codes too (§2)")
            xgd = [x.get("id") or x.get("file") for x in gs + ds if "EXTRA" in (x.get("src") or [])]
            if xgd: warns.append(f"guides/drugs carry EXTRA {xgd[:3]} — Extras are for questions and cards only (§2)")
            if qs + cs and len(xs) > 0.6 * len(qs + cs):
                warns.append(f"{len(xs)} of {len(qs + cs)} items are Extras — cut the pack rather than park most of it (§3b)")
    elif any(x.get("src") for x in qs + cs + gs):
        warns.append("items carry src but the pack has no sources table — add sources {code: label} (§2, v2.22)")

    # §6d (v2.27): card-image budget — about 1 MB of images in questions + cards per pack.
    ib = sum(len(m) for x in qs + cs for fld in IMG_FIELDS + ("image",) if isinstance(x.get(fld), str)
             for m in re.findall(r"data:image/[a-z+]+;base64,[A-Za-z0-9+/=]+", x[fld]))
    if ib > 1_000_000: warns.append(f"card images total {ib // 1024} KB (> ~1 MB) — shrink or drop some (§6d)")

    diffs = Counter(q.get("difficulty") for q in qs)
    print(f"questions={len(qs)} cards={len(cs)} guides={len(gs)} "
          f"difficulty={dict(diffs)} cardtypes={dict(types)}")
    # §3b.8 (v2.25): option length must not be a cue. Core (non-EXTRA) questions + mcq cards: the key is the single longest
    # option in about a quarter of items (chance); flag a share above 40% and any key ≥ 1.5× its longest distractor.
    core = [x for x in qs + [c for c in cs if c.get("type") == "mcq"] if "EXTRA" not in (x.get("src") or [])]
    longest, lop = 0, []
    for x in core:
        L = [len(o) for o in x.get("options", [])]; k = x.get("correct", -1)
        if not (isinstance(k, int) and 0 <= k < len(L)) or len(L) < 2: continue
        rest = max(v for i, v in enumerate(L) if i != k)
        if L[k] > rest: longest += 1
        if L[k] >= 1.5 * rest: lop.append(x.get("id"))
    if core and longest / len(core) > 0.40:
        warns.append(f"key is the longest option in {round(100 * longest / len(core))}% of {len(core)} core items (chance ~25%) — rebalance the options (§3b.8)")
    if lop: warns.append(f"{len(lop)} lopsided item(s), key ≥ 1.5× its longest distractor (first: {lop[:3]}) — give the distractors the key's length (§3b.8)")
    return errs, warns

# usage:
# errs, warns = validate_pack(pack)
# for w in warns: print("warn:", w)
# if errs:
#     for e in errs: print("ERROR:", e)
#     raise SystemExit("NOT WRITTEN — fix the errors above.")
# with open(out_name, "w", encoding="utf-8") as f:
#     json.dump(pack, f, ensure_ascii=False, indent=1)   # pretty-printed, never minified
```

**If you cannot run code,** you can't skip validation — you just do it by hand: re-read §7 item by item,
then paste the finished text into a strict JSON parser (per §8) and confirm it parses *before* delivering.

---

## 8. Output instructions

> **⚠ Output hygiene — the #1 cause of a pack that won't import.** The pack file must be **pure,
> valid JSON and nothing else.** Before you deliver:
> - **No prose, no explanations, and no Markdown code fences** inside the file — the file is JSON from
>   the first `{` to the last `}`.
> - **Strip every citation / grounding / source / footnote marker your tooling may inject** — e.g.
>   bracketed span tags like `[span_0](start_span)…[span_0](end_span)`, `【…】`, superscript reference
>   numbers, or `[1]`-style cites. **A single stray marker anywhere makes `JSON.parse` reject the
>   entire file**, so the user sees nothing import.
> - **Parse-test the final text with a strict JSON parser** (it either parses cleanly or it doesn't).
>   Also re-run the §7 checklist. Do not hand over a pack you have not verified parses. **If you can run
>   code, do all of this with the §7a validator rather than by eye — it catches every artifact above.**

When you finish, produce **one downloadable file** (not just code in chat):

1. The **pack `.json`**, **with the topic guides embedded** in `guides[]`. This single file is the
   complete deliverable — the embedded guides make it fully self-sufficient (Topic Guides reader
   included). **Name the file to mirror the pack's placement (§2)** so the learner's packs stay
   organized and sort sensibly in a folder: **`<Year>_<Course>_Week<NN>_<Subject>_v<ver>.json`** —
   e.g. `Year1_CPC1_Week09_Pediatrics_v2.0.json`, or `Year1_Cardiology_Week12_Arrhythmias_v1.0.json`.
   Drop any level the learner doesn't use (a single-week pack with no course is just
   `Week12_Cardiology_v1.0.json`), and use the same week number(s) as the `weeks` field so name and
   metadata agree. The filename is for the learner's convenience only — inside the app, packs are
   identified by `id` and sorted by their `school`/`year`/`course`/`weeks` fields, not the filename.
2. A short report in chat: how many questions (and the easy/medium/hard split), how many cards, and
   which guides are embedded.

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
