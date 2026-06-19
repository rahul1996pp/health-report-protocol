# CLAUDE.md — Health Report Analysis Protocol

A reusable instruction set for analyzing a folder of personal medical reports
(blood tests, imaging, ECG/Echo/TMT, prescriptions, eye/skin/urine reports, etc.).

Drop this file into any folder of health documents. Claude Code should follow this
protocol **without making assumptions about the patient** — every fact must come from
the documents themselves, or from an answer the user explicitly gives when asked.

**TL;DR pipeline:** load memory → discover every file → confirm renames + body-system
folders → extract every value → **ask about anything unclear inline** → trends → interlinks
→ **what's-ahead projections (from their own data, clearly caveated)** → Markdown synthesis →
self-explaining HTML dashboard → verify → update
the memory cache. Never diagnose; never invent; always explain in plain language.

**Scope rule:** the dashboard contains **only what this protocol specifies** — build nothing
extra, and if the user drops a feature, remove it from the spec *and* the dashboard so the two
always match.

**Phase index:** 0 Discovery · 1 Rename & Organize · 2 Extraction · 3 Resolve Unclear (inline)
· 4 Trends · 5 Interlinks · 6 Synthesis report · 7 HTML dashboard · 8 Verification — plus
Memory & Incremental Re-runs, What NOT to do, Working Style.

---

## ⚠️ Ground Rules (read first, never skip)

1. **This is informational organization, NOT medical diagnosis.** State this clearly in
   every report you produce. The user must confirm all findings with a qualified doctor.
2. **Never invent or guess a value.** If a number, unit, date, or word is unclear in a
   scan or photo, write `[unclear — please verify]`. Do not fill gaps from memory or
   from "typical" values.
3. **Ask, don't assume — and ask in the flow.** The moment you hit anything unreadable,
   ambiguous, or contradictory, **ask the user right then** (inline, during whatever phase
   you're in) and wait for their reply before relying on it — do **not** save questions for a
   single late batch. Record each answer and tag it **"confirmed by user (not from
   document)"** so provenance stays honest. Only fall back to `[unclear — please verify]` for
   items the user explicitly leaves unresolved (answers "skip" / "leave it").
4. **Do not assume patient identity, age, sex, or history.** Extract these only if the
   documents state them. If absent or contradictory, mark as `not stated in documents`
   and ask the user. **If the folder holds reports for more than one person** (differing
   names / IDs), stop and ask — then keep each person's data separate; never merge two people
   into one analysis or dashboard.
5. **Do not diagnose, prescribe, or recommend medication doses.** You may explain what a
   marker means and what ranges are typical, but treatment decisions belong to a doctor.
6. **Preserve the originals.** Never delete a source file. Renames must be logged and
   reversible.
7. **Flag, don't alarm — but don't miss the urgent.** Report findings factually; avoid
   catastrophizing. If the lab itself marks a value **critical / "panic"** (or it sits far
   outside range), surface it **calmly and prominently** with "please contact your doctor
   promptly" — without diagnosing. Always include the line: *this is not for emergencies; for
   severe symptoms call your local emergency number.*
8. **Cite the source for every fact.** Either the source file name, or "confirmed by user."
9. **Explain everything in plain language — err toward MORE detail.** Assume a non-medical
   reader with zero background. Every marker, finding, scan, chart, link and recommendation must
   come with a clear "what this is / why it matters / what your result means / what to do"
   explanation in everyday words, with analogies where they help. When in doubt, over-explain
   rather than assume knowledge. Define every abbreviation on first use.
10. **Remember between runs (to save tokens).** Maintain a folder-local memory cache,
    `health_memory.json`, holding the distilled inventory, extracted data, resolved answers,
    trends, and links. On every run, **load it first** and re-process **only new or changed
    files** — never re-read unchanged sources. Update it at the end. See
    "Memory & Incremental Re-runs."
11. **Privacy first.** This is sensitive personal health data — keep it all on the user's
    machine; never upload it or send it to any external service. Embed all data locally and
    never fetch or transmit anything at runtime.

---

## Phase 0 — Discovery (read-only)

**Load memory first.** If `health_memory.json` exists, read it and treat its contents as
already-known. List the folder and **diff it against the cached manifest** (relative path +
size + modified date — paths matter once files live in body-system subfolders). Only **new or
changed** files need full inspection in the steps below;
unchanged files reuse their cached extraction — this is what keeps re-runs fast and cheap. If
there is no memory file, this is a first run: process everything. Tell the user briefly what
you loaded from memory and which files are new/changed.

1. List every file in the folder **and its subfolders** (the tree may already be organised by
   body system) with its type and size.
2. Open and inspect **every** file — PDFs page by page, images visually. Do not analyze
   filenames alone; the content is what matters. **If a file isn't a medical document** (selfie,
   screenshot, unrelated PDF), list it under **"skipped — not a health record"** and leave it
   untouched — never force it into a category or a folder.
3. Build an inventory table: `file -> document type -> date found inside -> patient name if any`.
4. If the same report spans multiple images/pages, group them and note the grouping. If two
   files are the **same** report (a duplicate scan/photo), flag it and **count it once** — never
   double-count its values.
5. Report the inventory to the user and confirm understanding before proceeding.
6. **Collect the patient's basic profile — ask ONE question at a time** (wait for each answer
   before asking the next): full name · date of birth · age · sex · **blood group** ·
   (optionally height, weight, known allergies/conditions) · **home region / country (or the
   cuisine they usually eat) and any dietary pattern** (e.g. vegetarian / vegan / eggetarian /
   halal / no beef / no pork / Jain). The region & cuisine let the **food guidance name
   realistic local dishes and locally-available healthier swaps** (Phase 7, Suggestions)
   instead of generic advice; the dietary pattern & allergies keep every food suggestion safe
   and culturally appropriate. Pre-fill from the documents where possible and just ask to
   confirm; ask outright for anything not in the documents (blood group and location are rarely
   printed — **never infer location from a clinic name, the report's language, or a surname;
   ask**). Tag answers **"confirmed by user"**, save them to `health_memory.json` (so you never
   re-ask), and show them in the report and the dashboard header. If the user **declines** to
   give a location, keep the food guidance **generic** and note that sharing a region would
   make it local. Also ask the user's **preferred language** for the dashboard — if they want a
   regional language, write the plain-language explanations in it (or **bilingually**, English +
   local), keeping medical terms with a plain gloss and everything **offline** (no translation
   service). **Ask the language — never assume it from the reports.**

---

## Phase 1 — Rename & Organize into Folders (only after user confirms)

**Goal:** meaningful, sortable, consistent filenames **and** a tidy folder tree organised by
body system, so the folder is self-explanatory at a glance.

Format:
```
YYYY-MM-DD_category_short-description.ext
```

Examples:
```
2025-01-02_blood_full-body-checkup.pdf
2026-06-08_ultrasound_thyroid-neck.jpg
2026-06-08_ecg_resting-12-lead.jpg
2026-06-05_prescription_cardiology.jpg
2026-03-06_eye_lensmeter.jpg
2026-06-08_skin_lesion-left-leg.jpg
2026-06-08_urine_routine-microscopy.jpg
```

Rules:
- Use the **date printed inside the report**, not the file's scan/creation date. If no
  internal date exists, use the scan date and append `-scandate`.
- **Resolve date format carefully.** Many reports use DD/MM/YY; some use MM/DD. Prefer the
  report's unambiguous/long-form date. **Auto-resolve where only one reading is possible** (e.g.
  `15/06` can only be DD/MM — there is no 15th month). Only **ask** when a date is genuinely
  ambiguous (e.g. `06/07`), before committing it to a filename or a trend.
- Allowed categories: `blood`, `urine`, `ultrasound`, `xray`, `ct`, `mri`, `ecg`, `echo`,
  `tmt`, `prescription`, `discharge`, `eye`, `skin`, `dental`, `pathology`, `other`.
- Lowercase, hyphen-separated descriptions. No spaces, no special characters.
- If two files would collide, append `-1`, `-2`.

Process:
1. Present the full `old name -> new name` mapping as a table.
2. **Wait for explicit user confirmation.** Do not rename before approval.
3. After approval, rename and write `rename_log.txt` (old -> new, timestamped) so every
   change is reversible.
4. **Then organise into folders** (below), and make sure any dashboard link to a source file
   points at the **final path** — `folder/new-name.ext` — so it opens.

### Folder layout (organise by body system)
Group the renamed files into subfolders so the folder explains itself. **Create only the
folders that will actually hold files — never an empty folder.** Recommended tree:

```
<health folder>/
├─ heart/            ECG, Echo, TMT, cardiology OPD notes
├─ thyroid-neck/     thyroid / neck ultrasound
├─ liver-abdomen/    abdomen & pelvis ultrasound
├─ kidney-urinary/   dedicated kidney / urinary scans (kidney lab markers also live in blood-labs)
├─ eyes/             eye exam, refraction, lensmeter
├─ ent/              ENT notes / scopes
├─ skin/             dermatology            (add only if such reports exist)
├─ dental/           dental                 (add only if such reports exist)
├─ bones-joints/     ortho / bone density   (add only if such reports exist)
├─ blood-labs/       whole-body blood/urine lab panels (systemic — not one organ)
├─ prescriptions/    general clinical notes / advice not tied to one organ
└─ other/            anything that fits nowhere above
```

**Category → folder** (use judgement by what the document concerns):

| Filename category | Folder |
|---|---|
| `ecg`, `echo`, `tmt` | `heart/` |
| `ultrasound`, `xray`, `ct`, `mri` | by region: `thyroid-neck/`, `liver-abdomen/`, `kidney-urinary/`, … |
| `blood`, `urine`, `pathology` | `blood-labs/` |
| `eye` / `dental` / `skin` | `eyes/` / `dental/` / `skin/` |
| `prescription`, `discharge` | `prescriptions/` (or the relevant organ folder, e.g. ENT → `ent/`) |
| `other` | `other/` |

- A whole-body **blood/urine panel is systemic** — keep it in `blood-labs/`, not under one organ.
- Keep multi-page sets (report + image sheets) together in the same folder.
- **Keep generated files at the folder root** — `CLAUDE.md`, `health_memory.json`,
  `extracted_data.md`, `health_analysis_*.md`, `health_dashboard_*.html`, `rename_log.txt`,
  `move_log.txt`. Never move these into subfolders.

Process for moving:
1. Present the full `old path -> new path` mapping (folder + name) as a table.
2. **Wait for explicit user confirmation** — moving is a structural change; never move first.
3. After approval, move the files and write `move_log.txt` (old -> new, timestamped). Together
   with `rename_log.txt`, this makes every change fully reversible.
4. Update `health_memory.json` and every dashboard link to the new relative paths.

---

## Phase 2 — Extraction (structured data)

For **lab/blood/urine reports**, extract into a structured table per report:

| Marker | Value | Unit | Reference range | In/Out of range | Source file |
|--------|-------|------|-----------------|-----------------|-------------|

- Capture **every** marker, not just abnormal ones.
- Keep the lab's own reference range exactly as printed (ranges vary by lab/age/sex).
- **Ranges can change between reports.** Record each report's own range and flag each value
  against the range valid on *that* date; when a lab changes a marker's range over time, note
  it so an in-range value isn't falsely flagged.
- Mark each as `LOW`, `HIGH`, `NORMAL`, or `BORDERLINE` strictly against the printed range.
- Preserve units exactly. Do not convert silently; if you convert, show both.
- For each marker also capture a one-line **plain-language meaning** ("what it is / why it
  matters") to reuse in the dashboard's explanations.
- **Re-verify a sample (accuracy second pass).** After extracting, spot-check a random handful
  of values — and **every flagged / critical one** — back against the original scan or PDF, to
  catch transcription or OCR slips *before* anything is trended, charted, or shown.

For **imaging / ECG / Echo / TMT / eye / prescriptions**, extract:
- Date, performing facility/doctor (if stated), and the key findings/impression verbatim
  where possible, then a plain-language restatement.
- The **body region / organ** the document concerns (used to group findings by system).
- For prescriptions: list each item (drug/supplement/test/advice) as written, plus a plain
  explanation of what each is for. Do **not** alter or recommend doses.

**Non-English / regional-language text:** transcribe it verbatim, then add an English
translation tagged "(translated)"; mark anything uncertain `[unclear — please verify]` and ask.
Never silently drop text you cannot read.

Save extraction as `extracted_data.md` (or `.csv` for the lab tables if the user prefers).

---

## Phase 3 — Resolve Unclear Items (ask the user, ONE question at a time)

**Unclear items are questions to ask, not footnotes to leave behind.** Whenever a value, date,
unit, range, drug/supplement name, dose, or identity detail is unreadable, ambiguous, or
contradictory, **ask the user about it — one question at a time**: ask a single question, wait
for the answer, fold it in, then ask the next. Work all the way through the list this way. **Do
not leave items sitting as "unclear" without having actually asked.** An item only stays
`[unclear — please verify]` after the user has been asked and replies **"skip" / "leave it"**
(or genuinely doesn't know).

- **One at a time, short and specific.** Don't dump a long batch the user must wade through.
  Prefer the structured question tool for choices (e.g. "Is the age 28 or 29 here?"); ask
  free-text for things only the user knows (e.g. "what does this handwritten drug name say?").
  Asking *as you read* is ideal, but it's fine to gather any **leftover** questions into one
  focused round **before** building the dashboard — still one question at a time — so the build
  is never blocked or based on guesses.
- The user may answer, correct you, or say **"skip" / "leave it"** / tell you what to do —
  honor that. Only items they leave unresolved stay `[unclear — please verify]`.
- **Incorporate every answer immediately** into the working extraction and tag it
  **"confirmed by user (not from document)"** so it is never confused with a printed fact.
  Also store it in `health_memory.json` so you never have to ask again.
- **If a new answer contradicts something already in `resolved`** (the cache), update it to the
  new value and briefly note the change — never silently keep the old one.
- **Never fabricate** — record only what the user explicitly provides.
- Keep a running tally; before the Synthesis report (Phase 6), do a quick final sweep to
  confirm nothing was missed.

The dashboard's Unclear tab (Phase 7) then shows two groups: **Resolved (user-confirmed)** and
**Still unverified**.

---

## Phase 4 — Trend Analysis (across time)

If multiple dated reports exist:

1. Build a **trend table** for every marker that appears in more than one report:

| Marker | Date 1 | Date 2 | Date 3 | Latest | Direction | Reference |
|--------|--------|--------|--------|--------|-----------|-----------|

2. Mark direction as `rising`, `falling`, `stable`, or `fluctuating`.
3. **Flag in-range markers that are trending toward a limit** — direction matters even
   when the value is still "normal."
4. Note any marker measured only once (no trend possible).
5. Call out internal consistency checks (e.g., a derived ratio vs its components).
6. When a marker's printed reference range **differs across reports**, show each range and
   flag each value against its own report's range — note the range change rather than implying
   the value itself moved.
7. **Check unit consistency before trending.** Different labs may report the same marker in
   different units (e.g. cholesterol mg/dL vs mmol/L; glucose mg/dL vs mmol/L). Confirm all
   dates share one unit; if not, **convert to a single unit, show it, and note the conversion** —
   never plot mixed units on one chart.
8. **Match marker synonyms** so one marker trends as one line, not two — e.g. SGPT = ALT,
   SGOT = AST, FBS = fasting glucose, Vit D = 25-OH Vitamin D. Map name variants to a single
   canonical marker before building the trend.
9. **Project the trend forward — carefully, and only where it's honest (feeds the "What's
   Ahead" tab, Phase 7).** For a marker with **≥3 real timepoints** and a steady (non-erratic)
   direction, you may extrapolate where it is heading *if the current trend continues* — a
   simple, plain projection, never a guarantee. **Do not project** a single/two-point marker, a
   fluctuating one, or one with mixed units. Every projected value is an **estimate derived
   here, not a measured result** — label it as such, and remember it **may well be wrong** (real
   life, treatment, and lifestyle change the curve). Never invent a number that the data
   doesn't support, and never turn a projection into a diagnosis.

---

## Phase 5 — Interlink Mapping (the "hidden findings")

Look for **relationships between findings**, not just isolated values. For each proposed
link, state the evidence and label confidence as `strong / moderate / speculative`.

Examples of the *kind* of reasoning to apply (only if the data supports it — never force a
pattern that isn't there):
- Do several markers point to one underlying mechanism?
- Does one organ-system result plausibly explain a symptom noted elsewhere?
- Are two markers that usually move together actually moving together here?
- Is a derived index (ratio/score) telling a different story than the raw values?
- Are there "quiet" findings — values technically in range but notable in context or trend?

Present this as a clear map: `Finding A -> plausible link -> Finding B (evidence, confidence)`.

Explicitly separate:
- **Supported by the data in these files**, vs
- **General medical association (would need confirmation/testing)**.

Never present a speculative link as established fact.

For **each** link/chain, also capture **"what helps"** — the concrete lifestyle steps and the
tests/questions for a doctor that address it — so the dashboard's Full Map can show a detailed
**"what you can do about it"** block (Phase 7, Tab 5). Keep it lifestyle-level and framed as
"discuss with your doctor"; no diagnosis, no medication names or doses.

**Capture the "where this is heading" reasoning here too (feeds the "What's Ahead" tab,
Phase 7).** Where the combined findings + trends point toward a recognised concern, note —
**qualitatively only** — the *kind* of condition they point toward (e.g. "metabolic markers
trending toward the pre-diabetic picture") and the **recognised risk tests a doctor uses** to
check it. **Never attach an invented probability or percentage, and never name a future disease
as a certainty** — this is "discuss with your doctor," confidence-labelled
(strong/moderate/speculative), not a prediction of fate.

---

## Phase 6 — Synthesis Report

Produce `health_analysis_<YYYY-MM>.md` containing, in this order:

1. **Disclaimer** (informational only; confirm with a doctor; values unverified where noted).
2. **Patient context** — only what the documents state or the user confirmed; otherwise `not stated`.
3. **Document inventory** — what was reviewed.
4. **Trend tables** — all markers over time.
5. **Out-of-range & notable findings** — grouped by system (metabolic, thyroid, liver,
   kidney, cardiac, hematology, vitamins/inflammation, etc.). Add a plain-language
   explanation for each grouping.
6. **Improving vs worsening** — an honest two-column summary across the timeline.
7. **Interlink map** — Phase 5, with confidence labels.
8. **What's ahead (projections — clearly caveated)** — for markers with **≥3 timepoints** and a
   steady direction, where the trend is heading *if it continues*; the plain-language risk
   trajectory per watched marker; and the **qualitative** "conditions to watch" with the
   recognised risk tests a doctor uses. Open this section with the caveat that these are
   **rough estimates from the person's own past data, may be wrong, and are not a forecast** —
   confirm everything with a doctor. **No invented numbers/percentages, no diagnosis.** Surfaces
   in the dashboard's "What's Ahead" tab.
9. **Gaps, body-coverage & suggested tests** — first a **coverage summary**: which body systems
   the reports actually cover (`checked` / `partly` / `not assessed`), marked **only from real
   extracted data — never inferred or invented**. Then **suggested scans/tests**, each with
   **"what it would reveal,"** split into **❗ Important** (tied to an out-of-range or trending
   finding) and **➖ Optional** (general screening the current data doesn't flag). Draw suggestions
   from **two sources**: (a) the person's own findings, and (b) **general whole-body screening**.
   **Age/sex-specific screenings** (e.g. mammogram, Pap, PSA, colonoscopy, DEXA) appear **only when
   the user confirmed age/sex in Phase 0** — otherwise a neutral "ask your doctor which apply to
   you" note; **never assume age or sex to suggest one**. Everything is **"just suggestions — this
   is an AI analysis, confirm with your doctor"**; **no medicines, no doses, no diagnosis**. These
   surface in the dashboard's Doctors tab as the **coverage grid + "Scans & tests to know more."**
10. **Questions for your doctor** — a concise list the user can take to a consultation.
11. **Resolved & unclear items** — what the user confirmed (tagged) and what is still
    `[unclear — please verify]`.

Formatting: clear tables, plain language, short sentences. Define medical abbreviations on
first use. No catastrophizing. No invented numbers.

---

## Phase 7 — Visual HTML Dashboard (the headline deliverable)

Turn the analysis into a single self-contained, **richly interactive, animated, and
self-explaining** HTML file: `health_dashboard_<YYYY-MM>.html`

It should feel like a polished personal health-companion app that a non-medical person can
open and *understand on their own* — warm, calm, modern, genuinely useful — never
clinical-cold or alarming. **Teaching the user is a primary goal**, not an afterthought.

**Make the UI feel genuinely good — aim ultra-realistic and premium.** Match the craft of a
top-tier consumer app: beautiful, cohesive, smooth and delightful, with real depth (gradients,
shadows, tasteful 3D/animation) rather than flat placeholder shapes. Quality of *feel* (spacing,
type, colour, motion, hierarchy, micro-interactions) is a hard requirement, not a nice-to-have.
Give the user **rich, tasteful interactivity and more options to explore** (toggles, filters,
views) — never a bare, static page.

**Build exactly what this protocol specifies — nothing extra.** The set of tabs, sections and
features below *is* the dashboard's scope. Don't invent tabs or widgets that aren't described
here; if something isn't in this spec (or the user has asked to drop it), don't add it, and
**remove it if it's present** so the build always matches this file.

### Technical baseline
- **Fully offline, single self-contained file — ZERO network requests.** It must work with the
  internet completely off (all CSS, JS and fonts embedded; scans/PDFs are linked local files —
  see below). Inline all CSS and JS; **use a system font stack** (or inline/base64
  a font) — **no Google Fonts or any external `<link>`/`<script>`**. Nothing may be fetched at
  runtime.
- **Charts: prefer hand-built inline SVG** for the trend graphs — it is the lightest, truly
  dependency-free option and shaded reference bands are easy to draw. Only inline a charting
  library if the chart types genuinely require it, and then pick the **smallest** (e.g. uPlot or
  Chart.js — **never inline Plotly**, it is huge). A library adds ~200 KB–3 MB, so weigh it
  against the size budget below.
- **Scans & reports: show the findings as text; the files themselves are optional.** With no
  Body Map, the dashboard **does not embed or depend on any images or PDFs**. Present each
  imaging/scan result as **plain-language text** (the impression + what it means). You *may* add
  a simple link to the source file at its organised path (`folder/name.ext`) so the user can
  open the original, but that link is **optional**, and **embedding** images/PDFs into the HTML
  is **not needed**. Charts are inline SVG, so the dashboard is fully self-contained and offline
  on its own. (The original scans/PDFs are never deleted — they stay in their body-system
  folders regardless.)
- **All data embedded as JS variables.** Never send patient data to any external API.
- **No browser storage** (`localStorage`/`sessionStorage` are unsupported in some sandboxes).
  Keep all state in memory.
- **Mobile-first & responsive.** Most users open this on a phone. Tabs scroll horizontally;
  charts resize; tap targets are large.
- **Print-friendly** so it can be saved as PDF for a doctor visit — on print, expand **all tabs
  *and* every `ⓘ` detail and Full Map "what you can do" block**, so nothing useful is hidden in
  the PDF.
- **Encoding:** write the file as UTF-8; verify em-dashes, ×, µ, and emoji render correctly.
- **Stamp it & manage versions.** Show a **"Generated on &lt;date&gt;"** line plus a one-line
  **"To update: drop new reports into this folder and re-run"** note in the footer. Re-runs
  overwrite `health_dashboard_<YYYY-MM>.html`; if you want history, keep the latest as the working
  file and archive older dated copies rather than letting many pile up.

### Visual design
- Cohesive, calm palette (e.g. warm paper background, sage/clay/gold accents). Use an
  **accessible status scheme — never rely on red/green alone**; pair colour with labels,
  icons, or shapes for colour-blind readers.
- Serif display font for headings + clean sans for body, from a **system font stack** (no
  web-font CDN — see the fully-offline rule above).
- **Tasteful animation:** fade/slide on tab switch; progress bars that fill on load; charts
  that animate in; subtle hover lifts; gentle highlight when a cross-linked finding is opened.
  Keep it smooth and lightweight — never gratuitous or slow.
- Card-based layout, rounded corners, soft borders, generous spacing.

### Accessibility (build it in, don't bolt it on)
- **Keyboard-navigable:** tabs and **cross-links / interactive controls** reachable via
  Tab/Enter, with a visible focus ring.
- `alt` text on any embedded image; ARIA labels on interactive controls; semantic headings.
- Meet **WCAG AA** contrast; comfortable base font size; large tap targets.
- **Respect `prefers-reduced-motion`** — disable or greatly reduce animations when the user's
  system asks for it.
- **Charts need a non-visual fallback** — give each chart an `aria-label`/description, make data
  points keyboard-focusable, and include a visually-hidden data table (date → value) so screen
  readers and keyboard users get the numbers too.

### Explain everything ("what is what")
This is mandatory, not optional:
- **Show the full details in context — don't send the user to a separate glossary.** Every
  **marker, finding, term and chart** has an expandable detail (an `ⓘ` / "Details" toggle) that
  explains, in plain words: **what it is · why it matters · what your value means · what to do
  about it** (no diagnosis, no doses). Define every **abbreviation** in place on first use.
  **No marker may be left without its `ⓘ` detail, and no detail may be a thin one-liner** —
  write a genuinely helpful 1–3 sentence explanation for *every* marker (verify none are
  missing or generic).
- Add a short **"How to read this"** helper on the Trends tab (what the shaded band means, what
  crossing it means) and a **legend** wherever pills/arrows/colours appear.
- Keep the reading level simple: short sentences, everyday words, analogies where helpful —
  aim for about a **grade 6–8 reading level**, and define every abbreviation on first use.
- **Language:** if the user chose a regional language in Phase 0, write the explanations in that
  language (or **bilingually**, English + local), with medical terms glossed in plain words. Keep
  it embedded and offline — never call a translation service.

### Filtering & interactivity
The dashboard should feel **explorable, not static**:
- **Filters & search** on the data-heavy tabs (My Numbers, Trends): filter by **body system**,
  by **status** (high / low / normal / watch), by **"out-of-range only"**, and by **date**; plus
  a **search box** to find any marker by name. Filters update the view instantly.
- **Sort** the My Numbers tables (by name, value, status).
- **Cross-links must be correctly mapped (common bug — verify each one).** Clicking a marker
  jumps to **that marker's own chart**: switch to Trends, **scroll the exact chart into view and
  briefly highlight it** — never just open the Trends tab and leave the user hunting, and never
  land on the wrong chart. Confirm every marker→chart mapping is right. Clicking a chart legend
  toggles that series.
- **Interlink the tabs — link as much as possible so the user can reach any tab easily.** A
  finding, marker, term or action named *anywhere* should be a **clickable doorway** to where it
  lives. Aim for **maximal, consistent navigation**, not a few links:
  - Wire, at minimum: **Overview** "wins / if-you-do-3-things / at-a-glance" items, **Full Map**
    nodes *and* their "what you can do" steps, **Doctors** (incl. its follow-up checklist),
    **Suggestions**, and **Exercise** "why" lines → the relevant **Trend chart** or **My Numbers
    row**; and **My Numbers ↔ Trends** both ways.
  - **Give every tab an obvious way to reach its related tabs** — a small **"Related:" chip row**
    (or inline "→ see Exercise / Suggestions / this chart" links) so the user is never stuck. e.g.
    Full Map chain → its charts + Exercise + Suggestions; Suggestions ↔ Exercise; Exercise → the
    charts it improves; a Trend chart → "see in My Numbers"; Doctors → the marker charts it cites.
  - Make it **bidirectional wherever it makes sense**, keep one **single jump mechanism**
    (switch tab if needed → scroll to target → briefly highlight), and **verify every link lands
    on the right place** (no dead or mis-pointed links — re-check after any refactor).
  - Easiest robust implementation: a tiny tag like `data-go="c:<chartId>"` / `m:<marker>` /
    `tab:<id>` on any element + **one delegated click handler** — then adding a link anywhere is
    just adding the attribute.
- All controls (and every link) are **keyboard-accessible**, obvious, and work fully offline
  (in-memory only).

### Tabs (build as many as the data supports — these are the template)
Render a sticky top tab bar. Include the tabs that apply to the data found:

1. **Overview** — prominent disclaimer banner at the very top; **and — only if a value is
   lab-flagged critical/"panic" (or sits far outside range) — a calm but prominent "please
   contact your doctor promptly" banner just under it** (with the *not-for-emergencies* line),
   linking to that marker; show **no such banner** when nothing is urgent. Then a "wins / what's
   going well" card; an at-a-glance status list of each major finding with colour dots + labels; an "if
   you do only 3 things" highlights card; a short "how to use this dashboard" note; optional
   "your day at a glance" routine card if lifestyle advice applies. If a previous run exists in
   memory, also show a **"What changed since last visit"** card (new files, newly out-of-range
   markers, improved/worsened items).
2. **Trends / Charts** — chart the **clinically meaningful, grouped, or out-of-range** markers
   over time. **Don't auto-generate 50+ charts** for a big panel (it bloats the file and the
   page) — group related markers on shared charts (lipid panel together, thyroid together…),
   leave the rest in My Numbers, and offer an optional "show all charts" toggle. Each animated
   line chart has the **reference range shaded as a band** behind it; hover/tap tooltips show
   value + date + units; a band show/hide toggle; controls to **filter which markers / systems
   are shown**; and a one-line plain explanation. **Where a lab changed a marker's range between
   tests, show the range that applied on each date** (in the tooltip), not just the latest.
   - **Retest-targets / progress strip (when multiple timepoints exist).** Below the charts,
     show animated bars of **current value → a target to discuss with a doctor**, with
     milestones and an honest improving/worsening read. (This replaces a separate Progress tab;
     only split it into its own tab if there are many targets.)
3. **What's Ahead** — where the person's **own** numbers are heading. Built **only** from their
   extracted data + Phase 4 projections + Phase 5 risk reasoning — never from outside
   assumptions. **It opens with a calm but unmissable caveat banner** (its own card, just under
   the standard disclaimer): *"These projections use only your own past results and assume
   nothing changes. They are rough estimates, can easily be wrong, and are NOT a forecast of
   your health — always confirm with your doctor."* Plus the *not-for-emergencies* line. Then
   three sections:
   - **Projected numbers** — for each marker with **≥3 timepoints** and a steady direction, show
     where it is heading *if the current trend continues*, drawn as a clearly-styled **dotted
     "estimated" extension** of that marker's existing trend chart (visually distinct from the
     solid measured line, with an "estimate — may be wrong" label). **Never** render a projected
     point as a real measured value, and **never** project a single/two-point or fluctuating
     marker — say "not enough history to project this one" instead.
   - **Risk trajectory** — a plain-language "where this is heading" line per watched marker,
     quoting the **actual** values and dates (e.g. "HbA1c rose 5.4 → 5.7 → 5.9 over 18 months;
     at this pace it approaches the pre-diabetic band — worth asking your doctor"). Honest about
     improving trajectories too (celebrate them).
   - **Conditions to watch (qualitative only)** — the *kinds* of conditions the combined
     findings point toward, **with NO invented probability or percentage and no disease named as
     certain**, each paired with the **recognised risk tests a doctor uses** to check it
     (cross-link to the Doctors tab's "scans & tests to know more"). Confidence-labelled
     (strong/moderate/speculative) like the Full Map.
   - Every item **cross-links** to its own Trend chart / My Numbers row; include a **"Related:"**
     chip row to Doctors, Suggestions and Exercise. Honour the offline, `prefers-reduced-motion`,
     accessibility and print-expand rules like every other tab.
   - **With fewer than 2 timepoints (a single report), this tab shows "not enough history yet to
     project — re-run after your next test"** rather than empty or broken content; hide the
     dotted projections entirely.
4. **My Numbers** — grouped result tables by body system (metabolic, thyroid, liver, kidney,
   cardiac, hematology, vitamins/inflammation…). Each row: marker with an **`ⓘ` that expands a
   genuinely useful detail** (what it is / why it matters / what your value means / what to do),
   latest value **with its date** (so the user sees how recent it is), reference range, status
   pill, trend arrow, source. **Every single marker must
   have this `ⓘ` detail — none missing, none a thin stub.** Add **filters (body system, status,
   out-of-range-only), a search box, and sortable columns**; a legend explains arrows and colours.
   If the lab changed a marker's reference range over time, note that in its `ⓘ` so an older
   in-range value isn't judged against today's range.
5. **The Full Map** — render the Phase 5 interlink findings as visual "cascade" chains
   (Finding A → mechanism → Finding B …) using styled boxes/flow, plus a **"Hidden areas"** card
   (quiet, in-range-but-notable findings — formerly "hidden numbers").
   **Every chain MUST carry TWO plain-language blocks, not just the diagram:**
   - **"What's happening to you"** (1–3 sentences) — what these linked findings mean *together*,
     in everyday words, and why it matters.
   - **"What you can do about it"** (the new, required help block) — concrete, **detailed,
     step-by-step** guidance a non-medical person can actually act on, tied to *this* chain's
     findings. For **each** action say **what to do, and *why/how* it helps** (the mechanism, in
     plain words). Cover, where relevant: **food** (what to eat more / less, with realistic
     swaps — reuse the location-aware food guidance), **movement** (link to the Exercise tab),
     **hydration, sleep, weight, habits**, and the **tests/questions to raise with the doctor**.
     Give a rough sense of **what's in the user's control vs. what needs a clinician**, and — where
     the data shows a trend — an encouraging "this is reversible / improvable" note. **Frame every
     medical action as "discuss with your doctor," with no diagnosis and no medication names or
     doses** (lifestyle "how to reduce it" is fine and encouraged; prescribing is not). Don't be
     vague — give enough detail that the user understands *exactly* what to try and *why it works*.
   Don't leave the boxes to speak for themselves — the boxes are the diagram, the two narration
   blocks are the point. Keep confidence labels (strong/moderate/speculative) on every link, and
   give the **"Hidden areas"** card the same **"what this means for you" + "what you can do"** treatment.
6. **Exercise** — data-supported physical-activity guidance, presented like a friendly coach:
   - Start from what the documents actually show (e.g., measured exercise capacity / METs on
     a TMT, any "daily exercise" advice in the reports) and state it as the **baseline** —
     **never claim medical clearance**; say "your reports show X; confirm with your doctor
     before starting or changing exercise."
   - Cover **aerobic, strength, and flexibility/mobility** with a simple weekly-plan template
     and gentle progression; large, friendly cards.
   - **Show an image for each exercise / type** — a clear illustration or figure (inline SVG
     diagrams, or base64-embedded images). They **must be offline-embedded** (no external image
     URLs). Keep them simple, friendly and self-explanatory.
   - **Explain the "why" in depth, for someone with no medical background.** For each
     recommendation, say in plain words *why* it helps and *how it actually works in the body*,
     tied to this person's own findings — e.g. "brisk walking makes your muscles pull sugar and
     fat from the blood and helps insulin work, which directly targets your high triglycerides,
     pre-diabetic HbA1c and fatty liver." Use everyday language and analogies, and give enough
     detail that the user fully understands the reasoning — explanatory, not prescriptive.
   - Include **safety cautions tied to the findings** and clear "stop and seek help if…"
     red-flag guidance (chest pain, severe breathlessness, dizziness).
   - Only include guidance the data/reports support; frame everything as "discuss with your
     doctor." No exercise "prescriptions," no rehab claims, no dose-like specifics.
7. **Suggestions** — actionable, plain-language guidance grouped sensibly (food, movement,
   hydration, sleep, medication-adherence reminders). Only guidance the data/reports support;
   frame medical actions as "discuss with your doctor." Present richly (cards, do/avoid lists).
   - **Location-aware food & drink guidance — "can enjoy" vs "best to have rarely."** Make this
     the clear, headline part of the tab the user asked for: show, in **three separate paired
     lists**, what they can eat/drink freely and what to keep occasional — tied to **their
     documented findings** and named in their **own regional cuisine** (only fully local when a
     region/cuisine was confirmed in Phase 0):
     - **Foods** — a **"Can enjoy freely"** column (e.g. vegetables, whole grains, lentils/pulses,
       lean protein — whatever fits the findings & cuisine) beside a **"Best to have rarely /
       smaller portions"** column (e.g. for high triglycerides/cholesterol → fried & refined-carb
       local staples; for high uric acid → high-purine items).
     - **Fruits — broken out on their own** (people are often surprised some fruit is high-sugar):
       a **"Can enjoy"** list (e.g. berries, guava, apple, citrus — typically lower-sugar) vs a
       **"Enjoy in small portions"** list (e.g. mango, banana, chikoo, grapes, dried fruit, fruit
       juice) — especially relevant for pre-diabetic HbA1c / high triglycerides. Whole fruit over
       juice; note portion, not prohibition.
     - **Drinks** — a **"Can enjoy"** list (water, unsweetened tea/coffee, buttermilk, etc.) vs a
       **"Best to have rarely"** list (sugary drinks/sodas, sweetened juices, and — for raised
       uric acid / liver markers — **alcohol**, framed as "discuss with your doctor").
     For **each** item say in plain words **why** it helps or hurts *their specific numbers*, and
     for every "have rarely" item offer a **realistic, locally-available swap** (same cuisine,
     same shops) — not an unfamiliar import. **Respect the confirmed dietary pattern and
     allergies** (never suggest something they don't or can't eat). Keep the tone supportive —
     **never shame a food or a cuisine**; frame it as "swap more often / smaller portions," not
     "banned" or "not allowed," and always "discuss with your doctor or a dietitian." If **no
     location was given**, show the generic version plus a gentle note that confirming a region
     would make these lists local. No diagnosis, no doses, no rigid meal "prescriptions."
8. **Doctors / Next Steps** — which specialists to consider and why, in priority order. For
    **each** doctor give two clear, action-oriented lists: **What to ask** (specific questions
    for that specialist) and **What to do** (the concrete next step — e.g. which test/scan to
    request, what to bring, what to monitor, when to follow up). Plus a "what to carry to each
    visit" card. Always framed as "discuss with your doctor," never as orders or doses.
    - **Body-coverage grid (folded in here).** A grid at the top of the tab, one tile per relevant
      body system (blood sugar/metabolic · cholesterol/heart-risk bloods · thyroid · liver · kidney ·
      blood count · vitamins/inflammation · heart (ECG/Echo/TMT) · eyes · skin · bones/joints ·
      abdominal organs — show only those that apply). Each tile shows an **honest status — ✅ checked /
      🟡 partly (seen only indirectly, e.g. organ inferred from blood markers) / ⭕ not assessed** —
      marked **only from real extracted data, never inferred or invented** — with an accessible
      icon+label (never colour alone). Checked tiles link to their findings (My Numbers/Trends);
      not-assessed tiles link to the suggested test below. **This is a system checklist/grid, NOT an
      anatomical "Body Map"** (which stays excluded).
    - **"Scans & tests to know more" (folded in here).** Render as a **responsive card grid (one card
      per system, not a long single column)** — each card headed by the system name + its **coverage
      status pill** (✅ checked / 🟡 partly / ⭕ not assessed). Inside each card, suggested scans/tests
      split into **❗ Important** (tied to an out-of-range or trending finding) and **➖ Optional**
      (general screening the current data doesn't flag), each with **"what it checks / what it would
      reveal"** in plain words (e.g. anti-TPO → is the thyroid autoimmune; fasting insulin/HOMA-IR →
      quantify insulin resistance; FibroScan → grade the fatty liver), each linking to the relevant chart.
      Draw from **two sources**: (a) the person's own findings, and (b) **general whole-body
      screening** for each system. **Age/sex-specific screenings** (mammogram, Pap, PSA, colonoscopy,
      DEXA…) render **only when age/sex was confirmed in Phase 0**, labelled *"for your age/sex group,
      doctors commonly suggest…"*; if unknown, show a neutral *"some screenings depend on age & sex —
      ask your doctor which apply to you"* — **never assume age/sex**. A prominent **"these are
      general suggestions from an AI analysis, not personalised to your full history — confirm
      everything with your doctor"** caveat (+ the not-for-emergencies line) sits on this section.
      Framed as "discuss with your doctor," **no diagnosis, no medicines, no doses**. Keep it distinct
      from the checklist (gaps = *why each test matters*; checklist = *tick it off*).
    - **Follow-up checklist (folded in here).** Include an interactive, **tappable checklist**
      of the pending tests/scans and follow-ups, with an animated completion bar — in-memory
      only, so note it **resets on reload**. (This replaces a separate Checklist tab; only split
      it out if the list is long.)
9. **Unclear Items** — two groups: **Resolved (confirmed by the user)**, each tagged as
    user-provided, and **Still unverified** (`[unclear — please verify]`) to re-check against
    the originals.

*(No separate Glossary tab — every term's full meaning is shown inline, in context, per the
"Explain everything" rule above.)*

**Keep the four advice areas consistent, not repetitive.** Actionable help shows up in *four*
places — the Full Map's **"what you can do,"** **Suggestions**, **Exercise**, and **Doctors /
Next Steps**. Give each a distinct job (Full Map = the per-chain mechanism fix · Suggestions =
everyday food/habits · Exercise = how to move and why · Doctors = the clinical next step),
**cross-link them**, and make sure they **never contradict** one another.

Add or drop tabs based on the actual data. Never show an empty tab; if a region/tab has no
supporting data, say so explicitly rather than leaving it blank or inventing content. With only a
**single report** (no history), show single data points or a short note instead of trend lines,
**hide** the retest/progress strip, the dotted **What's Ahead** projections, and the "what
changed since last visit" card, and make sure nothing looks empty or broken.

### Accuracy rules (carry over from earlier phases — non-negotiable)
- Every charted point and table value must come from Phase 2 extraction (or a Phase 3
  user-confirmed answer) and trace to a source. **Never plot or display an invented or
  `[unclear]` value** — omit it and note the gap.
- A user-confirmed value must be visibly tagged as such (not passed off as document-sourced).
- **A projected / predicted value (What's Ahead tab) is never presented as a real measured
  value** — it is visually distinct (dotted/labelled "estimate, may be wrong"), only ever drawn
  where **≥3 real timepoints** support it, carries **no invented probability/percentage**, and
  names **no disease as certain**. The "may be wrong / discuss with your doctor" caveat is
  present wherever a projection appears.
- Reference ranges shown must be the lab's own, exactly as printed.
- The disclaimer must be visually prominent at the top of Overview — never hidden.
- No diagnosis, no medication-dose advice anywhere in the UI — explanations and "discuss
  with your doctor" framing only.
- **If everything is in range, present a calm, reassuring dashboard** — celebrate the normals;
  never manufacture concern to fill space.
- Any link to a source file must resolve to the correct **renamed** file at its organised
  relative path (`folder/name.ext`).

After generating, **open/scan the HTML in a real browser** to confirm it renders, every chart
is populated, reference bands show, tab navigation and cross-links work (each lands on the right
target), explanations/tooltips appear, animations run, and the numbers match the Markdown report
exactly. **Check the browser console — it must be error-free** (a `ReferenceError` means a needed
global/function was dropped; fix it before shipping).

### Build pitfalls (guards learned from real bugs — keep adding to this list)
When you hit a bug while building or verifying, **fix it *and* add a one-line guard here** so it
can never recur. Known ones:
- **Don't delete shared globals when removing a feature.** When you cut a block (e.g. the
  embedded-scans data), make sure you don't also remove unrelated declarations that happen to sit
  next to it. Real bug: removing the scan blob also deleted the chart's `LBL` (date labels) and
  `REDUCE` (reduced-motion flag) that lived between the cut markers → `ReferenceError: LBL is not
  defined` and blank charts. After any structural removal, **re-open in a browser and confirm
  charts/tabs still render with zero console errors.**
- **A link/button inside a `<label>` must not toggle the checkbox** — give it
  `preventDefault()` + `stopPropagation()` (e.g. the "view ↗" links in the follow-up checklist).
- **Verify every cross-link target still exists** after a refactor — if a tab/section is removed,
  re-point or delete links that pointed at it (don't leave a jump to a deleted target).
- **Per-group result tables must share column widths.** When My Numbers renders one `<table>` per
  body system, each table auto-sizes its own columns, so the columns don't line up between
  sections (ragged "Latest/Unit/Reference/Status/Trend"). Give every table `table-layout:fixed`
  **and** an identical `<colgroup>` (fixed % widths) so all sections align into one grid.

---

## Phase 8 — Verification Loop (before finishing)

Re-check your own output against these criteria (grouped) and fix any failures:

**Data integrity & accuracy**
- [ ] Every extracted value traces to a named source file (or is tagged "confirmed by user").
- [ ] No value was invented; all still-unreadable items are marked `[unclear]`.
- [ ] **Accuracy second pass done** — a random sample of values, plus every flagged/critical one,
      was re-checked against the original scan/PDF.
- [ ] Reference ranges are the lab's own, copied exactly; where a lab changed a marker's range,
      each value is judged against the range valid on *its* date (no false flags).
- [ ] Directions/trends arithmetically correct; **units consistent** per trended marker
      (conversions noted); **synonyms unified** (e.g. SGPT/ALT) so each trends as one line.
- [ ] Charted/table values match the Markdown trend tables exactly; no `[unclear]`/invented value
      is plotted or displayed anywhere.
- [ ] No second patient's data mixed in; ambiguous dates disambiguated; non-English text translated.
- [ ] **Phase 3 done** — unclear items asked **one-by-one** and resolved (or user said "skip"),
      none left unasked; **basic profile** (name/DOB/age/sex/blood group) collected one-at-a-time
      and shown in the report + dashboard.

**Trends, links & explanations**
- [ ] Trends charts the **meaningful/grouped** markers (not 50+); per-date ranges show in tooltips.
- [ ] **Cross-links land on the CORRECT chart** — clicking a marker scrolls to &amp; highlights
      *that marker's* trend chart (mapping verified per marker), not just the Trends tab.
- [ ] **Full Map gives BOTH blocks on every chain** — "what's happening to you" *and* a detailed,
      plain-language **"what you can do about it"** (food/movement/hydration/sleep + doctor
      questions, each with the *why*; lifestyle help fine, no diagnosis/doses).
- [ ] **Links are maximal & correct** — every finding/marker/action named in Overview / Full Map
      (incl. "what you can do") / Doctors / Suggestions / Exercise is a doorway to its chart or My
      Numbers row; each tab is reachable from context (a "Related:" chip row / inline "→ see X"
      links); My Numbers ↔ Trends both ways; **every link lands correctly** (no dead/mis-pointed).
- [ ] **Advice areas are consistent** — Full Map "what you can do" / Suggestions / Exercise /
      Doctors have distinct jobs, cross-link, and don't contradict each other.
- [ ] **Every marker/finding/term has a complete, useful `ⓘ` detail** (none missing/thin);
      abbreviations defined in place; **no separate glossary tab**.
- [ ] **Maximum plain-language detail** — markers, links, suggestions, exercise all over-explain
      for a non-medical reader.

**Exercise, Doctors & UI/UX**
- [ ] **Tabs match this spec exactly** — Overview · Trends (with retest-targets strip) · What's
      Ahead · My Numbers · Full Map (with "Hidden areas" card) · Exercise · Suggestions · Doctors
      (with "scans & tests to know more" gaps + follow-up checklist) · Unclear. **No Body Map, no
      export/"Take it with you" card, and nothing else not described here.**
- [ ] **What's Ahead tab is honest** — opens with the "estimates, may be wrong, not a forecast"
      caveat banner; projections drawn only where **≥3 real timepoints** exist and shown as
      visibly distinct dotted "estimate" lines (never as measured values); risk trajectory quotes
      real values/dates; "conditions to watch" stay **qualitative** (no invented %, no disease as
      certain) with the recognised doctor tests; every item cross-links to its chart; single
      report degrades to "not enough history yet" with projections hidden.
- [ ] Imaging/scan results shown as **plain-language text** (file link optional); **no images or
      PDFs embedded**, and the dashboard works on its own without them.
- [ ] **Exercise tab** has offline-embedded SVG/figure per exercise + in-depth "why/how"; no
      prescriptive/dose claims.
- [ ] **Doctors tab** gives per specialist **what to ask** + **what to do**, plus a "what to carry" card.
- [ ] **Body-coverage grid** present — status (✅ checked / 🟡 partly / ⭕ not assessed) **honest** (no
      system marked checked without real data), accessible (icon+label, keyboard), tiles link correctly.
- [ ] **Suggested tests** split **❗ Important / ➖ Optional**, each with "what it checks / why," from
      both the person's findings **and** general whole-body screening; **age/sex-specific screenings
      only when age/sex confirmed** (else neutral "ask your doctor" note) — **never assumed**; the
      **"AI analysis, confirm with your doctor"** caveat present; **no medicines/doses anywhere**.
- [ ] **Suggestions** food guidance shows the **"can enjoy" vs "best to have rarely"** split in
      **three separate lists — Foods, Fruits (broken out), Drinks** — each item tied to the
      findings with the *why* + a realistic local swap; **location-aware when a region/cuisine
      was confirmed**, respecting dietary pattern & allergies, **no food-shaming** (framed as
      "smaller portions/swap," never "banned/not allowed"); generic + a "share a region to
      localise" note if no location was given. Location was **asked, never inferred**.
- [ ] **Filters, search & sort** work on the data tabs; the UI **feels genuinely good** (polish,
      type, colour, motion, layout).
- [ ] Disclaimer prominent (Markdown + HTML); no diagnosis/dose advice anywhere.
- [ ] If everything is in range, the dashboard reads calm/reassuring (no manufactured concern).
- [ ] **Urgent banner** appears (calm) **only** for a critical/panic value, else absent; **single
      report** degrades gracefully (no broken trends or empty strips); **My Numbers shows each
      latest value's date**; if a **regional language** was chosen, explanations are in it /
      bilingual and fully offline.
- [ ] Responsive on mobile; **print expands all tabs + every ⓘ detail + Full Map help blocks**;
      **no empty tabs**.
- [ ] **Browser console is error-free** — opened in a real browser; charts/tabs render; no
      `ReferenceError` (i.e. no global/function was dropped in a refactor). See "Build pitfalls."
- [ ] Accessible — keyboard tabs/controls, alt text on any image, AA contrast, `prefers-reduced-motion`,
      and a **chart data-table/aria fallback** for non-visual users.

**Offline, size & privacy**
- [ ] **Fully offline — zero network requests** (fonts embedded; charts inline SVG; no images/PDFs
      embedded — scans are text findings with an optional local-file link).
- [ ] **File size small** — with no embedded scans the file should be tiny; if images were
      embedded at the user's request, downscale and keep it well under ~15 MB.
- [ ] No browser-storage APIs; no patient data sent anywhere; nothing fetched at runtime.
- [ ] Any critical/panic value surfaced calmly; emergency note present.
- [ ] Footer shows **"Generated on &lt;date&gt;"** + a **"to update: drop new reports & re-run"** note.

**Files & memory**
- [ ] Originals intact; `rename_log.txt` if renamed, `move_log.txt` if moved.
- [ ] Files organised into body-system folders (only non-empty); generated files at root; all
      dashboard links resolve; non-medical files left untouched &amp; listed "skipped — not a health record".
- [ ] `health_memory.json` loaded at start (only new/changed re-processed) and updated at end
      (manifest, data, explanations, resolved answers).

State explicitly that you ran this checklist.

---

## Memory & Incremental Re-runs

The folder-local cache `health_memory.json` is what lets a re-run cost a fraction of the first
run. Keep it accurate and up to date.

**What it stores** (compact JSON — distilled, not raw source):
- `patient` — name/sex/age/DOB/blood group, **home region / cuisine, dietary pattern,
  allergies, preferred language** (+ any basic profile), each with provenance (`document` vs
  `user_confirmed`) — so the location-aware food guidance and the chosen language are never
  re-asked and stay consistent across re-runs.
- `files` — manifest of every source file: `path` (relative, incl. body-system subfolder),
  `name`, `bytes`, `modified`, `category`, `internal_date`, `body_region`, `status: processed`.
  This is the diff key.
- `markers` — extracted lab values per date (the data behind the trend tables).
- `explanations` — the plain-language `ⓘ` detail written for each marker, **stored once and
  reused** across runs so they aren't rewritten every time (a token saver).
- `imaging` — imaging / ECG / Echo / TMT / eye findings + impressions.
- `resolved` — every user-confirmed answer from Phase 3, so you never re-ask.
- `unclear_open` — still-unverified items.
- `trends`, `interlinks` — the analysis, so it needn't be recomputed from scratch. The
  **What's Ahead projections are *derived* from these** at build time (not cached separately),
  so they always reflect the latest merged data and never drift out of sync.
- `outputs` — generated files (`extracted_data.md`, `health_analysis_*.md`,
  `health_dashboard_*.html`, `rename_log.txt`, `move_log.txt`). **Keep a single dashboard
  `.html`** — don't write companion/duplicate copies.

**Run flow:**
1. **Load** `health_memory.json` (if absent → first run: do everything, then create it).
2. **Diff** the current folder against `files`: classify each as *unchanged* (reuse cache),
   *changed* (re-extract), or *new* (extract fresh).
3. **Process only new/changed files**, asking inline (Phase 3) about anything unclear in them.
   Reuse `resolved` answers — never re-ask what the user already told you.
4. **Merge** new data into the cached data; recompute trends/interlinks across the full
   (cached + new) dataset.
5. **Regenerate** the Markdown report and HTML dashboard from the merged data.
6. **Update** `health_memory.json` (manifest + data + resolved + outputs).

**Adding files later:** the user can simply drop new reports into the folder and re-run — the
protocol uses **memory + only the new files**, so it stays fast and cheap, and the dashboard's
trends and findings extend automatically. Keep `health_memory.json` out of the rename step
(it is not a source document) and never treat it as a medical source — it is only a cache.

---

## What NOT to do

- Don't diagnose conditions or claim certainty about disease.
- Don't present a projection as a fact, invent a probability/percentage, or name a future
  disease as certain (What's Ahead tab) — projections are caveated estimates from the person's
  own data, "discuss with your doctor," and never drawn from fewer than 3 real timepoints.
- Don't recommend starting/stopping/changing medications or doses — suggest **tests and lifestyle
  only**, framed as "discuss with your doctor."
- Don't mark a body system as **checked/covered without real extracted data** (no inferring), and
  don't suggest an **age/sex-specific screening without the confirmed profile** — gate on Phase 0,
  never assume.
- Don't prescribe exercise as therapy or imply medical clearance to exercise.
- Don't guess values, dates, ranges, drug names, or doses from "typical" knowledge — **ask**.
- Don't assume the patient's age, sex, ethnicity, or history if undocumented.
- Don't assume the user's **location, cuisine, or dietary pattern** (or infer it from a clinic
  name, the report's language, or a surname) — **ask**; and never shame a food or cuisine, or
  push a diet they can't get locally or don't eat.
- Don't delete or overwrite original files.
- Don't rename or move files before the user confirms the mapping; never create empty folders,
  and never move generated/output files into subfolders.
- Don't add tabs, sections, or features the protocol doesn't describe; the dashboard must match
  this spec exactly. If the user removes a feature, delete it from here **and** the dashboard.
- Don't bury uncertainty — surface it and ask about it (inline, when it arises).
- Don't pass a user-confirmed value off as if it came from a document, or vice-versa.
- Don't re-read unchanged sources when `health_memory.json` already holds them — load the
  cache and process only new/changed files.
- Don't re-ask the user something they already answered — reuse `resolved` from the cache.
- Don't rename, delete, or treat `health_memory.json` as a medical source; it is only a cache.

---

## Working Style

Work in phases. **Load `health_memory.json` first** and only deep-process new/changed files.
**If the folder is very large** (many dozens of files), process in **batches** by date or body
system, updating `health_memory.json` after each batch so progress survives a restart and a
single run doesn't blow the context window.
Pause for confirmation after **Discovery (Phase 0)** before **Rename & Organize (Phase 1)**
(both the name mapping and the folder moves need approval). **Ask about
unclear items inline, the moment they come up (Phase 3)** — don't save them for a single late
gate. For the rest, proceed phase by phase, showing intermediate output. The Markdown report
(Phase 6) is the source of truth; the HTML dashboard (Phase 7) is its visual, self-explaining
presentation — they must agree. At the end, **update the memory cache** so the next run is
cheap. Prefer a correct, well-cited, clearly-uncertain analysis over a confident, tidy, wrong
one.

**When you hit a bug while building or verifying, fix it *and* write a one-line guard into this
protocol** (Phase 7 "Build pitfalls" / Phase 8 checklist) so it can't recur — the spec should
get a little more bug-proof every run.

**This protocol is working if:** the user ends with cleanly named files organised into
body-system folders, a fully traceable
data extraction (document-sourced vs user-confirmed clearly separated), honest trend and link
analysis, clearly separated fact vs. inference, and an interactive HTML dashboard that a
non-medical person can understand alone — reference-banded trend graphs, plain-language
explanations everywhere, an exercise guide, and imaging results written in plain words (with an
optional link to each source file) — plus a prominent reminder to verify everything with their doctor.
And a re-run after dropping in new reports is quick and cheap, because `health_memory.json`
means only the new files are read.
