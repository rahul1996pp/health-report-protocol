# Design — Body Coverage & Prioritized Test Suggestions

**Date:** 2026-06-22
**Status:** Approved design (pending written-spec review)
**Touches:** `CLAUDE.md` + `plugins/health-report-analysis/skills/health-report-analysis/SKILL.md` (the protocol, kept identical in body), and the synthetic sample (`examples/sample-output/`).

---

## 1. Goal

Help a non-medical reader see, at a glance:

1. **What's been checked** — which body systems their reports actually cover, and which haven't been looked at.
2. **What to consider next** — scans/tests worth discussing with a doctor, each labelled **❗ Important** or **➖ Optional**, with a plain "what it checks / why."

The test suggestions come from **two sources**: the person's own findings (data-driven) **and** general whole-body screening guidance. Everything is framed as *"just suggestions — this is an AI analysis, confirm with your doctor."*

This is an enhancement of the **existing** Doctors-tab section "Scans & tests to know more (gaps)" — **not** a new tab, and **not** the anatomical "Body Map" the protocol deliberately excludes (this is a *system checklist/grid*, no body image).

## 2. Hard safety constraints (non-negotiable, inherited from the protocol)

- **No medicines, no doses, no diagnosis** — suggestions are tests/scans, lifestyle, and "discuss with your doctor," never drugs or treatment.
- **Never assume age or sex.** Age/sex-specific screenings are shown **only** when the user confirmed age/sex in Phase 0; otherwise a neutral "ask your doctor which apply to you" note.
- **Honest coverage.** A system is marked "checked" only if real extracted data maps to it — never inferred, never invented.
- **Just suggestions.** General screening is broad public-health guidance, explicitly *not* personalized to full history; the doctor decides what's actually needed.
- **AI-analysis caveat** prominent on the section.

## 3. The feature

### 3a. Coverage grid ("what's been checked")
A grid at the **top of the Doctors tab**, one tile per relevant body system. Canonical, layperson-friendly systems (shown only when applicable to the data or as a standard screening area):

Blood sugar / metabolic · Cholesterol / heart-risk bloods · Thyroid · Liver · Kidney · Blood count (hematology) · Vitamins / inflammation · Heart (ECG/Echo/TMT) · Eyes · Skin · Bones / joints · Abdominal organs.

Each tile shows a **status**:
- **✅ Checked** — ≥1 real finding/value maps to it.
- **🟡 Partly** — seen only *indirectly* (e.g., liver/kidney inferred from blood markers, no dedicated scan).
- **⭕ Not assessed** — no data.

Tiles link to findings (My Numbers / Trends) when checked, or to the suggested tests below when not.

### 3b. Per-system suggested tests ("what to consider")
Under/with each system, suggested tests split into **❗ Important** vs **➖ Optional**, each with a one-line **what it checks / why**. Two sources, merged and de-duplicated:

1. **Data-driven** — tied to *their* findings (e.g., pre-diabetic HbA1c → *fasting insulin / HOMA-IR*; rising lipids → *lipid recheck in ~3 months*). These rank **Important**.
2. **General screening** — broadly recommended for that system regardless of findings (e.g., heart → BP check; eyes → vision/retina exam; bones → vitamin D, DEXA later in life). Ranked Important/Optional as general guidance.

**Profile-gating (full breadth):** age/sex-specific screenings (mammogram, Pap, PSA, colonoscopy, DEXA, etc.) are included **only when age/sex is confirmed**, labelled *"for your age/sex group, doctors commonly suggest… — confirm with yours."* When unknown: neutral *"some screenings depend on age and sex — ask your doctor which apply to you."*

### 3c. Framing
Standing caveat on the section: *"These are general suggestions from an AI analysis, not personalized to your full history — your doctor decides what you actually need. Discuss before acting."* Plus the not-for-emergencies line. No medicines/doses anywhere.

## 4. Dashboard data model (sample + future runs)

A single embedded JS structure drives the grid + tests, e.g.:

```js
// profile gate
const PROFILE = { ageKnown: true, sexKnown: true, age: 34, sex: 'male' }; // from Phase 0; flags drive age/sex-specific items

// one entry per system
const COVERAGE = [
  { sys:'Blood sugar / metabolic', status:'checked', go:'c:a1c',
    important:[['Fasting insulin / HOMA-IR','quantifies insulin resistance behind a rising HbA1c']],
    optional:[['Repeat HbA1c in 3–6 mo','confirms the trend']] },
  { sys:'Heart (ECG/Echo)', status:'none',
    important:[['Blood-pressure check','BP was never recorded; central to heart risk']],
    optional:[['Resting ECG','baseline heart rhythm']] },
  // ...
];
// age/sex-specific items carry a flag so they render only when PROFILE.ageKnown/sexKnown
```

Rendering: reuse existing card/pill/golink styles; status uses the accessible colour+icon+label scheme already in the dashboard (never colour alone). Every test item and tile is keyboard-reachable and cross-links via the existing `data-go` mechanism.

## 5. Protocol changes (spec edits)

- **Phase 0 (Discovery/profile):** note that confirmed age/sex feed the screening gate; preferred language still applies to the new text.
- **Phase 6 (Synthesis report), "Gaps" section:** add (a) the coverage summary (systems checked / partly / not assessed) and (b) the Important/Optional test split with "what it would reveal," covering both data-driven and general screening, profile-gated. Keep "discuss with your doctor."
- **Phase 7 (Doctors tab):** rewrite the "Scans & tests to know more (gaps)" spec into: coverage grid + per-system Important/Optional suggested tests (data-driven + general, profile-gated) + the AI-analysis caveat. Explicitly **no new tab**, **not** an anatomical Body Map.
- **Phase 8 (Verification checklist):** add — coverage honest (no system "checked" without data); tests split Important/Optional each with a reason; general screening present and profile-gated (age/sex-specific only when confirmed, neutral note otherwise); **no meds/doses anywhere**; AI-analysis caveat present; all new links land correctly; accessible (icon+label, keyboard).
- **What NOT to do:** add "don't mark a body system as covered without real data"; "don't assume age/sex to show a screening — gate on the confirmed profile"; reaffirm "no medicines/doses — suggest tests and lifestyle only."
- **Scope rule:** since the dashboard "builds only what the protocol specifies," these spec edits and the dashboard must stay in lockstep.

## 6. Sample implementation (demo) + keep everything in sync

**Every change updates the docs and demo artifacts together — never the protocol alone.** For this feature and all future ones, a change is not "done" until these are consistent:

- **Protocol:** `CLAUDE.md` **and** `SKILL.md` (identical bodies).
- **README.md:** reflect any user-visible change (tab list, feature bullets, the Doctors-tab description, screenshots).
- **Sample dashboard:** `examples/sample-output/sample_dashboard.html` — implement the coverage grid + per-system Important/Optional suggestions for "Rahul" (age/sex are confirmed in the demo, so age/sex-specific items render with the profile label).
- **Sample markdown (test/fixture files):** `examples/sample-output/sample_health_analysis.md` (Gaps section) and `sample_extracted_data.md` where relevant, kept numerically consistent with the dashboard.
- **Screenshots:** recapture `docs/screenshots/*.png` whenever the UI they show changes.
- **Re-verify** the sample in a real browser: console error-free, all new links land correctly, no meds/doses, caveats present, accessible.

## 7. Out of scope (YAGNI)

- No anatomical body image / Body Map.
- No new tab.
- No medicine/supplement/dose suggestions.
- No risk percentages or disease naming (consistent with the What's-Ahead rules).
