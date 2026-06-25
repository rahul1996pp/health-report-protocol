# Proposal — "More Suggestions & Research": deepening the protocol

**Date:** 2026-06-25
**Status:** P0 items + the comprehension/accessibility feature set IMPLEMENTED in `CLAUDE.md` +
`SKILL.md` on 2026-06-26 (censored-value handling, full-text proofread, comprehensive whole-body
coverage grid, follow-up reconciliation, deeper Hidden-areas, author attribution, and the
research-backed comprehension features). **Still open:** rebuilding the synthetic sample dashboard to
*demonstrate* the new features, and the P1/P2 menu below.
**Scope chosen by user:** *Both — broad sweep* (improve the protocol overall **and** deepen the
Suggestions feature), delivered as a **written, cited proposal first**.
**Touches if approved:** `CLAUDE.md` + `plugins/health-report-analysis/skills/health-report-analysis/SKILL.md`
(identical bodies), `examples/sample-output/*`, `README.md`, `docs/screenshots/*`.

---

## 0. Method & how to read this

I ran four parallel research threads (public, authoritative sources only — nothing patient-specific,
fully consistent with the offline/privacy rule, since this is *domain* research that informs the spec,
not anything baked into a user's dashboard at runtime):

1. **Competitive landscape** — consumer lab-interpretation tools & health dashboards.
2. **Evidence-based lifestyle/diet guidance** per common abnormal marker (for the Suggestions feature).
3. **Adult screening & follow-up-test guidelines** per body system (for the Doctors tab).
4. **Health-literacy, risk-communication, accessibility & offline-HTML best practices** (for UX/build).

Every recommendation below is tagged with **where it lands** (Phase / Tab + which files), a **priority**
(P0 highest), a rough **effort**, and at least one **source**. Magnitude figures (e.g. "LDL ~−5%") are
presented as *"research suggests,"* never as guarantees — matching the protocol's no-promises rule.

**Important framing — this proposal does not break the "build only what the spec says" rule.**
Several ideas are *new* features. The protocol forbids adding UI the spec doesn't describe. So the
intended flow is: you approve items here → we **edit the spec (`CLAUDE.md`/`SKILL.md`) first** → then the
dashboard implements them. Nothing gets built ahead of the spec. New-feature items are clearly marked
**[SPEC-ADD]**; content-only enrichments that fit existing tabs are marked **[ENRICH]**.

**Guardrail re-affirmation (every item below stays inside these):** no diagnosis, no medicines, no doses;
lifestyle + "discuss with your doctor" only; never assume age/sex/location; offline & private; never
invent values or probabilities; honest coverage; calm, non-alarming tone.

---

## 1. Headline recommendations (the short list)

If you only do five things, do these — they are highest impact-per-effort and lowest risk:

| # | Recommendation | Lands in | Type | Priority |
|---|---|---|---|---|
| 1 | Ship a **cited per-marker lifestyle library** (12 markers × food/movement/sleep/alcohol + the *why*) the Suggestions tab and Full Map draw from | Phase 2/5/7 · Suggestions + Full Map | ENRICH | **P0** |
| 2 | Broaden **suggested tests** with a **per-system catalog** that carries each test's **guideline stance** (recommended / shared-decision / not-routine) so we don't over-medicalize | Phase 6/7 · Doctors | ENRICH | **P0** |
| 3 | Add **"flag, don't alarm" evaluative cues** beside mildly out-of-range values, and a **three-tier status** (normal / watch / out-of-range) that surfaces in-range-but-drifting markers | Phase 4/7 · My Numbers + Trends | SPEC-ADD (small) | **P0** |
| 4 | Add **non-food suggestion categories** (sleep, stress, smoking, alcohol, sedentary breaks, sun-for-vit-D, dental/vision, vaccinations) — each evidence-anchored and findings-gated | Phase 7 · Suggestions | ENRICH | **P1** |
| 5 | Tighten **risk-communication** in *What's Ahead* (frequencies not lone probabilities, ranges not point estimates, one number format) | Phase 4/5/7 · What's Ahead | ENRICH | **P1** |

The rest of this doc details these and adds a fuller menu (P1–P2) you can pick from.

**Added after your review (also P0):** a **comprehensive whole-body coverage grid** that shows *all* body
systems including the not-assessed ones (§3.3); a **follow-up checklist that reconciles against new reports**
so re-runs auto-close completed tests (§3.4); and a **deeper "Hidden areas" card that also suggests tests**
(§3.5); plus a **data-accuracy hardening pass** that handles censored lab values like B12 `>2000` and
re-checks *all* generated text, not just a sample (§5A).

---

## 2. Deepen the **Suggestions** feature (the user's explicit "more suggestions" ask)

### 2.1 [ENRICH · P0] A cited per-marker lifestyle library
Today the Suggestions tab is strong on **location-aware food**, but the per-marker *"why this helps your
specific number"* is generated ad hoc. Bake in a reusable reference library so every run gives the same
depth and the same plain-language mechanism. Proposed source-of-truth content is in **Appendix A** — one
row per common finding with food-to-favor/limit, movement, alcohol, sleep/weight/hydration, **each with a
one-line plain "why,"** plus authoritative source URLs. Markers covered: high triglycerides; high
LDL/total cholesterol; pre-diabetic HbA1c/high fasting glucose; fatty liver (ALT/AST/GGT); high uric acid;
low vitamin D; low B12; anemia/low ferritin; high BP; raised creatinine/low eGFR; abnormal TSH; high CRP.

*Why:* the dominant criticism of every paid tool researched (Function Health, InsideTracker) is
**"data-dump anxiety" with thin or generic actions** — a concrete, mechanism-first action per finding is
the antidote. Sources: NHS, Mayo, Cleveland Clinic, AHA/NHLBI (DASH), Harvard Health, NIH ODS (see Appendix A).
*Caveat baked in:* lifestyle only; magnitude figures as "research suggests"; never supplements/doses
(Function Health's supplement upsell is explicitly the thing to avoid).

### 2.2 [ENRICH · P1] Suggestion categories beyond food & exercise
The tab is food-heavy. Add **findings-gated** cards for the levers below — each only shown when the data
supports it, each with the evidence "why" and "discuss with your doctor" framing:

- **Sleep hygiene** — poor/short sleep raises inflammation & cardiometabolic risk (Harvard Health).
- **Stress / mind** (breathing, mindfulness) — among non-drug ways to lower BP (Mayo).
- **Smoking cessation** — CRP/inflammation drops "within weeks"; primary CV/kidney protector (Harvard).
- **Alcohol moderation** — lowers BP, triglycerides, uric acid, liver/kidney burden (AHA, Mayo gout diet).
- **Sedentary-time / sitting breaks** — light movement every ~30 min improves glucose/TG/BP (AHA, CDC).
- **Safe sun for vitamin D** — main vit-D source; balance against skin-cancer sun safety (NHS).
- **Routine dental & vision care** — oral health named in inflammation control; eye checks matter for sugar/BP (Harvard).
- **Vaccination reminders** — neutral "ask your doctor which apply" nudge (CDC adult schedule) — *age/sex-gated.*
- **Appointment/medication-adherence reminders** — "keep follow-ups & take prescribed items as your doctor directs" — **no doses**.

*Why:* rounds the tab from "diet sheet" into a genuine lifestyle companion while staying lifestyle-only.
(Full source list in Appendix A §2.)

### 2.3 [ENRICH · P1] Make the food guidance lists richer and reaffirm the no-shame frame
Extend the existing "can enjoy vs have rarely" lists with the marker-specific *why* from Appendix A
(e.g. high uric acid → fructose/beer specifically, because fructose breaks down into uric acid and alcohol
blocks its kidney clearance). Keep the three-list split (Foods / Fruits / Drinks) and the local-swap rule
exactly as specced. No new structure — just deeper, better-cited content.

---

## 3. Broaden **suggested tests & screening** (Doctors tab)

### 3.1 [ENRICH · P0] Per-system test catalog with guideline stance
Replace ad-hoc test suggestions with the **per-system catalog in Appendix B**, which adds, for each
system: the **important follow-up tests** for an abnormal finding (e.g. raised ALT → **FIB-4 then
FibroScan/VCTE**; pre-diabetic HbA1c → **OGTT, fasting insulin/HOMA-IR**; rising lipids → **Lp(a), ApoB,
hs-CRP, CAC score**; abnormal TSH → **free T4, anti-TPO**; low Hb → **ferritin/iron studies, B12/folate**),
plus the **general screening** test for that system.

**New, protocol-strengthening nuance — carry each test's guideline stance.** The research surfaced that
several "obvious" screens are **not** routinely recommended in low-risk adults:
- routine resting **ECG** — *not* recommended for low-risk asymptomatic adults (USPSTF);
- routine whole-body **skin-cancer** screening — *insufficient evidence* / I-statement (USPSTF);
- routine **vitamin-D** screening in healthy adults — *advised against* (Endocrine Society 2024).

So each suggested test should show a small stance label — **recommended · shared-decision · not routinely
recommended (ask your doctor)** — instead of implying "more tests = better." This keeps us honest and
**reduces over-medicalization**, which is itself a safety property. (Sources in Appendix B.)

### 3.2 [ENRICH · P1] Age/sex-gated cancer screening, with country caveat
Keep the existing age/sex gate, and populate it from Appendix B §13 with **typical ages** and an explicit
**"thresholds vary by country"** note (US/USPSTF vs NHS vs India differ — e.g. mammogram USPSTF 40–74 vs
NHS 50–71; colorectal start 45; PSA 55–69 shared-decision; DEXA women ≥65; AAA ultrasound once for men
65–75 who ever smoked; lung CT 50–80 with ≥20 pack-years). Render **only when age/sex confirmed**;
otherwise the neutral note — unchanged from current spec.

### 3.3 [SPEC-ADD · P0] Make the body-coverage grid comprehensive (whole-body)
*(from review feedback — the grid was missing whole body parts)*

The current canonical list is ~12 systems. But the grid's whole job is to show the **full body picture —
including systems that have NOT been assessed** — so the user can see the gaps. The fix is to expand the
canonical master list to a comprehensive whole-body set, each tile rendered with the existing honest status
(✅ checked / 🟡 partly / ⭕ not assessed), accessible icon+label, and links to findings or the suggested
test. Proposed master list (grouped to stay digestible):

**A. From bloodwork (systemic):** 1) Blood sugar / metabolic · 2) Cholesterol / heart-risk (lipids) ·
3) Thyroid · 4) Liver · 5) Kidney & urinary · 6) Blood count / anemia · 7) Iron & vitamins (ferritin, B12,
folate, vit D) · 8) Inflammation / immune (CRP, ESR) · 9) Electrolytes & minerals (sodium, potassium,
calcium, magnesium) · 10) Hormones / reproductive bloods (sex hormones, cortisol, prolactin — symptom-driven).

**B. Organs / functional / imaging:** 11) Heart & circulation (ECG/Echo/TMT/BP) · 12) Lungs / respiratory
(chest X-ray, spirometry, SpO₂) · 13) Brain & nervous system · 14) Digestive system / gut (stomach,
intestine; endoscopy, H. pylori, celiac) · 15) Abdominal organs (gallbladder, pancreas, spleen — imaging) ·
16) Bones & joints (DEXA, X-ray) · 17) Eyes / vision · 18) Ears / hearing (ENT, audiometry) · 19) Skin ·
20) Dental / oral · 21) Mental / emotional wellbeing · 22) Body measurements (BMI, waist, weight, BP).

**C. Age/sex-gated (render only when age/sex confirmed):** breast (mammogram) · cervical (Pap/HPV) ·
prostate (PSA discussion) · colorectal (colonoscopy/FIT) · bone density (DEXA) · AAA ultrasound · lung LDCT
for smokers.

Rules unchanged and reaffirmed: **status honest — never inferred** (a system is ✅ only if real extracted
data maps to it, 🟡 if seen only indirectly via bloods, ⭕ otherwise); group the ~22 into the A/B/gated
clusters so the grid doesn't overwhelm; mental wellbeing is **sensitive — frame gently** ("talking to a
professional helps — ask your doctor"), never inferred from any data, never alarming, no diagnosis.
New systems' tests are catalogued in **Appendix B**.

### 3.4 [SPEC-ADD · P0] Follow-up checklist that reconciles after new reports
*(from review feedback — the checklist never updated when new reports arrived)*

Today the follow-up checklist is **in-memory only and resets on reload** — it never closes the loop when new
reports come in. Make it **data-reconciled via `health_memory.json`** so a re-run actually updates it:

- Store a `followups` list in the cache — each suggested test/scan/referral as
  `{id, system, what, why, status: pending|done, suggested_date, completed_date, result_value, source_file}`.
- **On each re-run, diff new reports against the pending follow-ups.** When a matching result appears
  (e.g. you suggested an HbA1c recheck and a new panel has HbA1c; or you suggested a thyroid ultrasound and a
  neck-US report drops in), **auto-mark it done**, capturing the new value + date + source file.
- The regenerated checklist renders **auto-done items pre-checked** — "✓ done on \<date\> — result: \<value\>
  (from \<file\>)" — and pending items unchecked. Manual tapping still works in-memory for non-lab items
  (e.g. "saw cardiologist"); the **data-driven done-state is re-derived every run**, so it survives reloads
  **without any browser storage** (doesn't break the no-storage rule).
- Surface newly-closed follow-ups in the Overview **"What changed since last visit"** card, and add any
  **new** pending follow-ups the new findings generate.

Net effect: drop in your latest report → the dashboard tells you which recommended tests you've now done and
what they showed, and what's still outstanding. This is what makes the incremental re-run genuinely useful.

### 3.5 [ENRICH · P0] "Hidden areas" card — more detail + suggested tests
*(from review feedback — Hidden areas was thin and suggested no tests)*

The Full Map "Hidden areas" card (quiet, in-range-but-notable findings) is currently shallow. Deepen it so
**each** quiet finding carries three things, not just a label:

1. **A fuller plain-language explanation** — *what it is* and *why it's quiet-but-worth-knowing* (e.g. a value
   technically in range but trending toward its limit; a borderline ratio; a single low-normal that pairs
   meaningfully with another finding).
2. The existing **"what this means for you" + "what you can do"** lifestyle blocks (from Appendix A).
3. **A suggested test / recheck** drawn from the Appendix B catalog, **cross-linked to the Doctors tab** —
   e.g. low-normal ferritin trending down → "ask about iron studies"; in-range-but-rising creatinine →
   "repeat eGFR + urine ACR to confirm"; HbA1c near the top of normal → "recheck in 3–6 months."

Keep the confidence labels (strong/moderate/speculative) and "discuss with your doctor" framing; no
diagnosis, no doses. This turns Hidden areas into a genuine **early-warning surface** consistent with the
test catalog and the "flag, don't alarm" rule.

---

## 4. Protocol/product UX & feature improvements (broad sweep)

These are mostly **[SPEC-ADD]** — adopt only the ones you want, and we add them to the spec before building.
Each is drawn from a researched tool; cloud/account features were filtered out as incompatible with offline/privacy.

| Idea | What it does | Lands in | Type | Priority | Source / caveat |
|---|---|---|---|---|---|
| **Three-tier status** (normal / watch / out-of-range) | Surfaces in-range-but-drifting markers as "watch" (trend-based) without inventing tighter "optimal" ranges | My Numbers + Trends | SPEC-ADD | **P0** | InsideTracker/Withings tiers; *caveat:* "watch" = trend, never an invented narrower range (the thing those tools are criticized for) |
| **"Highlights since last visit" diff card** | New files, newly out-of-range, improved/worsened since last run | Overview | partly specced → finish it | **P1** | Apple Health; uses the existing memory cache, zero privacy cost |
| **Plain cross-system narrative paragraph** | One calm paragraph tying systems together at top of Overview | Overview | SPEC-ADD | **P1** | Function Health's clinician narrative; *caveat:* no diagnosis — pattern description only |
| **Pin / favorite markers** | User stars markers to surface on Overview (in-memory, resets on reload) | Overview / My Numbers | SPEC-ADD | **P2** | Apple Health/Function; in-memory only (no storage API) |
| **Selectable time windows on trends** (6mo / 1yr / all) | Readability for long histories | Trends | SPEC-ADD | **P2** | Apple Health |
| **"What's driving this" decomposition** on Full Map chains | Shows which markers feed each interlink chain | Full Map | SPEC-ADD (small) | **P2** | Oura "contributors"; reinforces the teaching goal |
| **Relatable analogies per marker** | e.g. fiber "soaks up cholesterol like a sponge" | ⓘ details everywhere | ENRICH | **P1** | Harvard/BHF analogies; *caveat:* don't compute an invented "health age" number (Withings vascular-age is a number we can't justify) |

**Explicitly rejected (kept out, consistent with protocol):** cloud accounts/sync, supplement upsells,
invented "optimal" ranges, and any AI "possible conditions with % likelihood" (Docus.ai-style) — that
crosses into diagnosis the protocol forbids. Listing these so the "what NOT to do" section can absorb them.

---

## 5. Health-literacy, accessibility & risk-communication polish

All **[ENRICH]**, low-risk, high-credibility. These sharpen rules the protocol already gestures at.

**Health literacy / plain language**
- **Verify readability to grade 6–8 with a tool**, don't just aim for it (NHS targets reading age 9–11).
  → add to Phase 7 "Explain everything." Source: NHS service manual; CDC plain-language.
- **One idea per sentence, ≤~20 words, plain term before the medical term** (CDC). → tighten the
  explanation-writing rule.
- **Pair every number with a visual range AND an evaluative cue** ("this is/ isn't usually concerning")
  beside *mildly* out-of-range values — measurably reduces false alarm (PMC 5891666). → directly powers the
  "flag, don't alarm" rule and item #3 above.

**Risk communication (What's Ahead tab)**
- **Express any risk as natural frequencies** (e.g. "about 30 in 1,000"), never a lone single-event
  probability or relative risk (Gigerenzer). → reinforces "no invented probabilities" and tells us *how* to
  phrase the qualitative trajectory.
- **Show projections as a range/scenario with explicit uncertainty**, never a single point (Spiegelhalter).
  → strengthens the existing dotted-estimate rule.
- **Keep ONE number format throughout** (don't mix %, fractions, frequencies). → small style rule.
- Optionally **self-score key messages with the CDC Clear Communication Index** as a build check.

**Accessibility (WCAG 2.2 AA — the spec currently says "AA" generally; make it concrete)**
- **Status never by color alone — color + icon + label** (SC 1.4.1; ~8% of men can't distinguish red/green).
- **Contrast 3:1 for chart lines/bands/UI, 4.5:1 text** (SC 1.4.11 / 1.4.3).
- **Every chart: aria-label + visually-hidden data table** (date→value) — already specced; keep.
- **Tap targets ≥24×24 CSS px (44 preferred)** — SC 2.5.8, *new in 2.2*; this is a phone-first dashboard.
- **Visible focus indicator not hidden by the sticky tab bar** — SC 2.4.7 / 2.4.11 *new in 2.2*.
- **Honor `prefers-reduced-motion`** with static alternatives — already specced; keep.
- **Use a colorblind-safe palette** distinct across deuteran/protan/tritan, not just "avoid red/green."

**Inline teaching**
- Keep progressive disclosure (`ⓘ` toggles) — research validates it (Nielsen/IxDF). Rule to add:
  **one focused idea per disclosure** so working memory isn't overloaded.

Sources for this whole section: NHS service manual, CDC Clear Communication / plain-language, PMC 11347896
& 5891666 (visual/evaluative cues), Gigerenzer (PMID 14512488), Spiegelhalter (Science 2011), W3C WCAG 2.2,
TPGi/Deque/Smashing on accessible charts, IxDF progressive disclosure. (Full URLs in Appendix C.)

---

## 5A. Data accuracy & full generated-text re-check (P0 — review feedback)
*(you spotted "B12 now > 2000" in a generated dashboard — two distinct problems)*

Data accuracy is non-negotiable for a health tool, so both of these are **P0**.

### (1) Censored / out-of-assay-range values ("> 2000", "< 2", "> 150")
Labs report a value as **"greater than X"** or **"less than X"** when it falls beyond what the assay can
measure — common for **B12, ferritin, D-dimer, hCG, troponin, TSH, IgE, CRP**. The protocol currently drops
the operator and treats ">2000" as the number `2000`, which is both wrong and produces broken phrasing like
*"B12 now > 2000."* Handle these as **censored values, not plain numbers**:

- **Extraction (Phase 2):** preserve the operator — store e.g. `{op:'>', limit:2000, unit:'pg/mL',
  censored:true}` — never silently store `2000`.
- **Trends/projection (Phase 4):** **do not plot, trend, or project a censored value as an exact point.**
  Show it at the limit with a distinct "≥ / actual is above the measurable limit" marker; **never extrapolate
  from it** (it can't anchor a What's-Ahead projection).
- **Display & ⓘ (Phase 7):** print it exactly as the lab did ("> 2000 pg/mL") and explain in plain words:
  *"Your lab reported this as 'greater than 2000' — it was above the highest level their test can measure, so
  the true value may be higher. For B12 this is common if you take B12 supplements or injections, and is
  usually not harmful — discuss with your doctor."* No diagnosis.
- **Status:** flag against the printed range but stay **calm**; name the usual benign cause (supplementation)
  without diagnosing.

### (2) Re-check ALL generated text, not just a sample
Today's accuracy pass spot-checks a random handful + every flagged value **against the source scans** (keep
that — re-OCR is expensive). Add a second, cheaper pass: a **full proofread of every generated sentence** in
both the Markdown and the HTML (no re-OCR needed — it's checking the generated text against the extracted
data), specifically catching:
- **malformed auto-generated phrasing** where an operator/unit got concatenated into prose ("B12 now > 2000"
  → "B12 is reported as **>2000 pg/mL**, above the lab's measurable limit");
- **Markdown ⇄ HTML parity** — every value, unit, range and sentence in the HTML must match the Markdown
  source of truth exactly (HTML is the *presentation* of the MD, never a re-derivation);
- units, censored values, dates, and that no `[unclear]`/invented value slipped into a sentence;
- grade 6–8 readability of the generated sentences.

### Build-pitfall guard to add (per the protocol's own "fix it + write a guard" rule)
> *Censored lab values ("> X" / "< X") are not numbers.* Preserve the operator on extraction; never plot,
> trend, project, or phrase them as an exact value; display "> X" verbatim with a plain-language "above/below
> the lab's measurable limit" note. After generating, **proofread every sentence** in the HTML against the
> Markdown — no concatenated operators, no number drift, no `[unclear]` in prose.

---

## 6. Build/offline technique (validates current choices, two refinements)

The research **confirms** the protocol's existing choices: inline SVG charts, system font stack, no Plotly,
no network requests. Two concrete refinements worth writing into "Build pitfalls":
- **If a chart library ever becomes necessary, use uPlot (~48KB), not Chart.js (~254KB); never Plotly** —
  put exact sizes in the spec so the tradeoff is explicit. (uPlot GitHub.)
- **If a brand font is ever truly needed, embed base64 WOFF2 via `@font-face` data-URI** (single file, no
  fetch) — otherwise stay on the system stack to avoid ~30% base64 bloat. (woff2base; HN discussion.)

---

## 7. Repo / process improvements (small, optional)

- **A `MEMORY.md`-style or `CHANGELOG.md`** at repo root tracking spec versions — the design-spec workflow
  in `docs/superpowers/specs/` is great; a changelog closes the loop for users on a versioned product.
- **A "sources" appendix inside the protocol** (or a `docs/EVIDENCE.md`) listing the authoritative bodies
  the lifestyle/screening content is grounded in — boosts trust for a health tool and makes future updates
  traceable. (This proposal's appendices can seed it.)
- **Keep the dual-source sync discipline** (`CLAUDE.md` ⇄ `SKILL.md`, README, sample, screenshots) — every
  item above must update all of them together, exactly as the prior body-coverage spec §6 already mandates.

---

## 8. Prioritized rollout (suggested order)

1. **P0 content first (lowest risk, highest value, no new UI scope):** Appendix A lifestyle library (§2.1),
   Appendix B test catalog + guideline-stance labels (§3.1), evaluative "flag-don't-alarm" cues + three-tier
   status (§4 row 1 & §5). Update spec → sample → screenshots.
2. **P1 enrichments:** non-food suggestion categories (§2.2), risk-communication wording (§5), analogies
   (§4), age/sex screening breadth (§3.2), finish the "since last visit" diff card (§4).
3. **P2 new features, à la carte:** pin/favorites, time-window toggle, narrative paragraph, Full Map
   decomposition — each gated on you wanting it, each spec-first.
4. **Accessibility/offline refinements (§5–6):** fold into the spec's existing accessibility & build-pitfalls
   lists as concrete numbers.

Each step is "done" only when `CLAUDE.md`, `SKILL.md`, `README.md`, the sample dashboard/markdown, and
screenshots are all consistent and the sample re-verifies in a browser (console-clean, links land, caveats
present) — per the established sync rule.

---

## Appendix A — Lifestyle suggestion library (cited)

> Lifestyle only. No drugs/doses/diagnosis. Magnitudes are "research suggests," not promises.
> Reframe every line as "discuss with your doctor."

| Finding | Top lifestyle levers (with the *why*) | Key sources |
|---|---|---|
| **High triglycerides** | Cut added sugar/refined carbs (turned straight into triglycerides) · limit/stop alcohol (potent TG raiser) · swap red/sat-fat for oily fish + plant fats (omega-3 lowers TG) · lose excess weight + move (surplus calories stored as TG). *Combined diet change: research suggests ~20–50% lower TG.* | AHA; Mayo Clinic triglycerides |
| **High LDL / total cholesterol** | 5–10 g/day soluble fiber — oats/beans/barley "soak up" cholesterol (~5% LDL) · cut saturated fat · Mediterranean pattern · plant sterols/stanols block absorption. *"Portfolio" diet: research suggests ~up to 30%.* | BHF; Mayo Mediterranean |
| **Pre-diabetic HbA1c / high fasting glucose** | Lose ≥5–7% body weight (ADA's biggest lever) · ≥150 min/wk moderate activity (muscles pull glucose, improve insulin sensitivity) · cut sugary drinks/refined carbs, add fiber · break up sitting. | ADA Standards; CDC Prevention |
| **Fatty liver (ALT/AST/GGT)** | Gradual 7–10% weight loss · 150 min/wk aerobic + resistance (cuts liver fat even without weight loss) · reduce fructose/refined carbs · minimize alcohol. | Mayo/NHS NAFLD; PMC 7697937 |
| **High uric acid / gout** | More veg/fruit/whole grains, less meat (beats strict low-purine) · cut alcohol esp. beer (blocks urate clearance) · avoid sugary/fructose drinks (fructose → uric acid) · low-fat dairy lowers urate · hydrate, lose weight. | Cleveland Clinic; Mayo gout diet |
| **Low vitamin D** | Sensible sun on skin (~80–90% of vit D) · oily fish/egg yolk/fortified foods · NHS: consider a supplement in autumn/winter — discuss with doctor. | NHS; NIH ODS |
| **Low B12** | Animal foods (only reliable natural source) · vegans/vegetarians: fortified cereals/plant milks/nutritional yeast · over-50s absorb food-B12 poorly → fortified foods/supplement, discuss with doctor. | NHS; NIH ODS |
| **Anemia / low ferritin** | Iron-rich foods (heme iron absorbs best; also beans/lentils/greens/fortified) · pair with vitamin C (up to ~3× absorption) · avoid tea/coffee/calcium around meals (block iron). | NHS/Mayo; NIH ODS iron |
| **High blood pressure** | DASH pattern · cut sodium toward <1,500 mg · more potassium from produce (offsets sodium) · weight loss, activity, less/no alcohol. | AHA; NHLBI DASH |
| **Raised creatinine / low eGFR** | Reduce sodium <2 g · tight BP & sugar control (DASH-style) · avoid excess protein & routine NSAID overuse · sensible hydration. *Clinician-guided.* | NIDDK CKD; PMC 9981552 |
| **Abnormal TSH** | Iodine within RDA (not mega-doses) · selenium-adequate diet · sleep, exercise, stop smoking. *Lifestyle is adjunct; thyroid needs doctor monitoring.* | Cleveland Clinic; PMC 10163981 |
| **High CRP / inflammation** | Anti-inflammatory diet (produce/legumes/whole grains/omega-3; less processed/sugar) · activity + weight loss · quit smoking · sleep · oral health. *Research suggests ~34–44% CRP drop with combined lifestyle.* | Harvard Health; Cleveland Clinic |

**§2 Non-food categories:** sleep hygiene (Harvard) · stress/mind (Mayo) · smoking cessation (Harvard) ·
alcohol moderation (AHA/Mayo) · sedentary breaks (AHA/CDC) · safe sun for vit D (NHS) · dental & vision
routine care (Harvard) · vaccination reminders, age/sex-gated (CDC) · appointment/medication-adherence,
no doses (general framing).

---

## Appendix B — Per-system test catalog (cited, with guideline stance)

> Tests/scans + "discuss with your doctor" only. Age/sex-gated items render only when age/sex confirmed.
> US/USPSTF ages cited; **thresholds vary by country.**

- **Blood sugar / metabolic** — *follow-up:* OGTT, fasting insulin/HOMA-IR, annual HbA1c recheck. *general:*
  fasting glucose + HbA1c from age 35 (earlier if overweight + risk factor). [ADA 2025/2026]
- **Cholesterol / heart-risk** — *follow-up:* Lp(a) once, ApoB, hs-CRP, lipid recheck; **CAC CT** for
  borderline/intermediate risk (men ≥40 / women ≥45) when it changes the statin decision. *general:* lipid
  panel periodically. [2026 ACC/AHA]
- **Thyroid** — *follow-up:* free T4 (±T3), anti-TPO (autoimmune cause), neck ultrasound if nodule. *general:*
  TSH first-line; **no universal screen** — test if symptomatic/risk. [AACE/ATA]
- **Liver** — *follow-up:* **FIB-4 first**, then FibroScan/VCTE or ELF if FIB-4 ≥1.3; ultrasound; hep B/C
  serology. *general:* hep C antibody once for all adults; hep B per CDC. [AASLD; CDC]
- **Kidney** — *follow-up:* repeat eGFR + urine ACR to confirm chronicity, cystatin-C eGFR, urine microscopy,
  renal ultrasound. *general:* eGFR + ACR for anyone with HTN/diabetes/nephrotoxin exposure. [KDIGO 2024]
- **Blood count** — *follow-up:* ferritin/iron studies/TIBC/transferrin sat (iron deficiency); B12/folate
  (±MMA) for macrocytic; reticulocytes, smear. *general:* CBC in routine panels. [SABM]
- **Vitamins / inflammation** — *follow-up:* 25-OH vit D only if a relevant condition (Endocrine Society 2024
  **advises against routine population screening**); B12/folate if neuro/anemia signs; hs-CRP/ESR if
  inflammation suspected. [Endocrine Society 2024]
- **Heart (ECG/Echo/TMT/BP)** — *follow-up:* echo, exercise/stress test, ambulatory BP/Holter, CAC. *general:*
  **BP screening for all adults ≥18**; **routine resting ECG NOT recommended** in low-risk asymptomatic adults.
  [USPSTF]
- **Eyes** — *follow-up (diabetes):* dilated retinal exam (T2 at dx, T1 within 3–5 yrs, then yearly). *general:*
  periodic comprehensive eye exam (vision, glaucoma/IOP). [ADA; AAO]
- **Skin** — *follow-up:* suspicious/changing lesion → derm exam ± dermoscopy ± biopsy. *general:* USPSTF
  **insufficient evidence** for routine whole-body screening — self-checks + see doctor for new/changing moles.
  [USPSTF]
- **Bones / joints** — *follow-up:* DEXA + FRAX after fragility fracture/risk factors. *age/sex-gated:* DEXA
  **women ≥65** (younger postmenopausal if high FRAX). [USPSTF]
- **Abdominal organs** — *follow-up:* abdominal/pelvic ultrasound for abnormal liver/kidney markers or pain.
  *age/sex-gated:* **AAA ultrasound once, men 65–75 who ever smoked.** [USPSTF]
- **Lungs / respiratory** — *follow-up (symptoms/abnormal film):* spirometry/PFT (breathing capacity), chest
  X-ray, pulse oximetry. *age/sex-gated:* lung LDCT **50–80, ≥20 pack-years**; no routine screen for non-smokers. [USPSTF]
- **Brain / nervous system** — *follow-up (symptoms only):* neuro exam, MRI/CT if indicated, B12 (nerve health).
  *general:* BP & blood-sugar control protect against stroke; **no routine brain imaging** in asymptomatic adults. [USPSTF]
- **Digestive system / gut** — *follow-up:* H. pylori test (dyspepsia), celiac serology, stool tests, upper
  endoscopy if indicated. *age/sex-gated:* **colorectal screening 45–75** (colonoscopy/FIT). [USPSTF]
- **Electrolytes & minerals** — *follow-up:* serum sodium/potassium/calcium/magnesium/phosphate (often already
  in a metabolic panel) — relevant to kidney, heart rhythm, bone. *general:* part of routine panels.
- **Hormones / reproductive bloods** — *follow-up (symptoms):* testosterone, FSH/LH, estradiol, prolactin,
  cortisol; PCOS workup. *general:* symptom-driven, not a routine screen; STI screening per age/risk. [CDC/USPSTF]
- **Ears / hearing** — *follow-up:* audiometry for suspected hearing loss. *general:* hearing check for older adults. [USPSTF]
- **Dental / oral** — *general:* routine dental check-up (6–12 monthly); oral health linked to inflammation/heart. [ADA/Harvard]
- **Mental / emotional wellbeing** — *general:* **depression (PHQ-2/9) and anxiety (GAD-7) screening for adults
  — USPSTF Grade B**; frame gently ("talking to a professional helps — ask your doctor"), no diagnosis. [USPSTF 2023]
- **Body measurements** — *general:* BMI, waist circumference, blood pressure — basic, central to metabolic & heart risk. [USPSTF/AHA]
- **General cancer screening (age/sex-gated; US ages, vary by country):** mammogram **40–74 q2y** (NHS 50–71);
  cervical Pap/HPV **21–65**; colorectal **45–75**; PSA **55–69 shared-decision**; lung LDCT **50–80, ≥20
  pack-years**. [USPSTF 2024]

---

## Appendix C — Source URLs

**Competitive (UX patterns only):** functionhealth.com/how-it-works · insidetracker.com · healthmatters.io ·
support.apple.com (Health records) · siphoxhealth.com · docus.ai/lab-test-interpretation · ouraring.com ·
support.withings.com (health score / vascular age). Pitfall refs: healthrx.com (invented optimal ranges);
seebeyondmedicine.com (data-dump anxiety).

**Lifestyle:** heart.org · mayoclinic.org · bhf.org.uk · diabetes.org / diabetesjournals.org · cdc.gov/diabetes ·
nhs.uk · ods.od.nih.gov (vit D / B12 / iron) · niddk.nih.gov · my.clevelandclinic.org · health.harvard.edu ·
nhlbi.nih.gov (DASH) · PMC: 11865679, 7697937, 8466505, 9981552, 10163981.

**Screening:** uspreventiveservicestaskforce.org · diabetesjournals.org (ADA Dx) · acc.org / newsroom.heart.org
(2026 lipids) · endocrinepractice.org (AACE/ATA) · aasld.org · kdigo.org (2024 CKD) · endocrine.org (2024 vit D) ·
sabm.org · aaojournal.org / PMC 5402875 (eye).

**Health literacy / accessibility / build:** service-manual.nhs.uk · cdc.gov/health-literacy ·
cdc.gov/ccindex · PMC 11347896 · PMC 5891666 · pubmed 14512488 (Gigerenzer) · science.org (Spiegelhalter 2011) ·
w3.org/WAI/WCAG22 · tpgi.com · dequeuniversity.com · smashingmagazine.com · github.com/leeoniya/uplot ·
css-tricks.com (SVG charts) · ixdf.org (progressive disclosure).

---

## Out of scope (YAGNI / keep excluded)

- No anatomical Body Map (unchanged exclusion).
- No cloud, accounts, sync, or any network feature.
- No supplement/medicine/dose suggestions; no AI "possible conditions with likelihood"; no invented
  "optimal" ranges or computed "health age" number.
- No new tab unless a P2 item explicitly warrants one (none here do — all land in existing tabs).
