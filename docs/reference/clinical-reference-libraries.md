# Clinical reference libraries (evidence base for the protocol)

**Date:** 2026-06-26 · **Status:** reference material backing the protocol's accuracy & plain-language rules.

These tables are the **evidence base** behind several `CLAUDE.md` / `SKILL.md` rules (trend-honesty,
unit/synonym handling, calm imaging translation, prescription purpose, extraction robustness). The
protocol stays lean and instructs the model to *apply* this knowledge; this doc records the specifics and
citations so they're traceable and updatable. **None of this is a diagnosis or a dosing guide.** The
protocol must always judge each value against the **lab's own printed range** on that report's date.

---

## 1. "Real change" vs noise — Reference Change Value (RCV) / biological variation

Two results from the same person differ for three independent reasons — only one is real change:
**within-subject biological variation (CVi)** (normal day-to-day rhythm), **analytical variation (CVa)**
(machine imprecision), and **true change**. A change "counts" only if it exceeds the marker's built-in
wobble. Conceptual formula: **RCV = √2 × Z × √(CVa² + CVi²)** (Z=1.96 → factor ≈ 2.77 for 95%).

Approximate within-subject CVi and a rough 95% "real-move" threshold (**approximate, lab-dependent,
qualitative — never print a false-precise per-patient statistic**):

| Marker | CVi % (approx) | Rough "real-move" threshold | Marker | CVi % (approx) | Rough "real-move" threshold |
|---|---|---|---|---|---|
| HbA1c | 1.9 | ~6–7% relative | Hemoglobin | 2.9 | ~8–9% |
| Fasting glucose | 5.6 | ~15% | Creatinine/eGFR | 6.0 | ~15% |
| Total cholesterol | 6.0 | ~15–18% | Uric acid | 8.6 | ~20–25% |
| LDL | 7.8 | ~20–25% | Ferritin | 14.2 | ~40% |
| HDL | 7.3 | ~20% | ALT (SGPT) | 19.4 | ~50–55% |
| Triglycerides | 19.9 | ~50–55% | AST (SGOT) | 12.3 | ~35–40% |
| Vitamin D (25-OH) | ~12 | ~35–40% | TSH | 19.3 | ~50–55% |

Tight markers (HbA1c, hemoglobin) move meaningfully with a small %; noisy ones (triglycerides, ALT, TSH,
ferritin) need a big % before it's real — e.g. a 30% ferritin bump is noise, a 30% HbA1c rise is huge.
**Use this to suppress false alarms, not create them.** Sources: [Westgard biological-variation DB](https://westgard.com/clia-a-quality/quality-requirements/biodatabase1.html),
[Westgard RCV FAQ](https://westgard.com/essays/guest-essay/faq-ri-bv.html), [Fraser CCLM 2011](https://www.degruyter.com/document/doi/10.1515/cclm.2011.733/html),
[MetricGate RCV](https://metricgate.com/docs/reference-change-value/), [AcuteCareTesting](https://acutecaretesting.org/en/articles/biological-variation--whats-it-all-about),
[Oxford Lab Med – vit D BV](https://academic.oup.com/labmed/article/44/1/20/2657727).

---

## 2. Lab marker dictionary — synonyms, units/conversions, typical ranges

Conversion shown **conventional → SI** (multiply). Ranges are **guidance only**; flag against the lab's own.

| Canonical (synonyms) | Units & conversion (conv→SI) | Typical adult range | Why ranges differ |
|---|---|---|---|
| Glucose, fasting (FBS, FPG) | mg/dL→mmol/L ×0.0555 | 70–99 mg/dL | fasting vs PP vs random; pregnancy lower |
| HbA1c (A1c, glycated Hb) | % ↔ mmol/mol: IFCC=(%−2.15)×10.929 | <5.7% | anemia/hemoglobinopathy distort |
| Total cholesterol (TC) | mg/dL→mmol/L ×0.0259 | <200 mg/dL | risk-based, not a true "normal" |
| LDL (LDL-C) | ×0.0259 | <100 mg/dL optimal | target scales to cardiac risk |
| HDL (HDL-C) | ×0.0259 | >40 M / >50 F mg/dL | sex-specific; higher better |
| Triglycerides (TG, TGL) | mg/dL→mmol/L ×0.0113 | <150 mg/dL | fasting; rises after meals/alcohol |
| Creatinine (Cr, SCr) | mg/dL→µmol/L ×88.4 | 0.7–1.3 M / 0.6–1.1 F mg/dL | sex/muscle-mass |
| eGFR (CKD-EPI) | mL/min/1.73m² — none | ≥90 normal | equation-dependent; often ">90" |
| **Urea / BUN** ⚠ | **urea mg/dL = BUN ×2.14**; BUN→mmol/L ×0.357 | BUN 7–20 mg/dL | **US=BUN, India/UK=urea — convert before trending** |
| Uric acid (UA, urate) | mg/dL→µmol/L ×59.48 | 3.4–7.0 M / 2.4–6.0 F mg/dL | sex-specific; pregnancy lower |
| ALT (**SGPT**) | U/L | ~7–55 U/L | SGPT=ALT exactly; ULN varies |
| AST (**SGOT**) | U/L | ~8–48 U/L | SGOT=AST; also muscle/heart |
| ALP (alk phos) | U/L | ~40–129 U/L | high-normal in teens & pregnancy |
| GGT (gamma-GT) | U/L | 8–61 M / 5–36 F U/L | sex-specific; alcohol-sensitive |
| Bilirubin, total (TBIL) | mg/dL→µmol/L ×17.1 | 0.1–1.2 mg/dL | Gilbert's raises indirect benignly |
| TSH (thyrotropin) | mIU/L = µIU/mL (same) | 0.4–4.0/4.5 mIU/L | pregnancy/elderly shift; assay |
| Free T4 (FT4) | ng/dL→pmol/L ×12.87 | 0.8–1.8 ng/dL | assay; pregnancy; ≠ total T4 |
| Hemoglobin (Hb, Hgb) | g/dL→g/L ×10 | 13.5–17.5 M / 12.0–15.5 F | sex-specific; altitude; pregnancy ≥11 |
| Hematocrit (Hct, **PCV**) | % → L/L ×0.01 | 41–50 M / 36–44 F % | PCV=Hct; sex-specific |
| RBC | ×10⁶/µL = ×10¹²/L (same) | 4.7–6.1 M / 4.2–5.4 F | sex-specific |
| WBC (**TLC**) | ×10³/µL = ×10⁹/L (same) | 4.5–11.0 ×10³/µL | TLC=WBC; children higher |
| Platelets (PLT) | ×10³/µL = ×10⁹/L (same) | 150–400 ×10³/µL | clumping → false low |
| Ferritin | ng/mL = µg/L (same) | 24–336 M / 11–307 F ng/mL | **acute-phase reactant — rises with inflammation** |
| Vitamin D, 25-OH | ng/mL→nmol/L ×2.5 | ≥20–30 ng/mL | IOM ≥20 vs Endocrine ≥30 |
| Vitamin B12 (cobalamin) | pg/mL→pmol/L ×0.738 | 200–900 pg/mL | 200–300 borderline |
| Sodium (Na⁺) | mmol/L = mEq/L (same) | 135–145 mmol/L | narrow |
| Potassium (K⁺) | mmol/L = mEq/L (same) | 3.5–5.1 mmol/L | **hemolysis falsely raises** |
| Calcium, total | mg/dL→mmol/L ×0.2495 | 8.5–10.5 mg/dL | **correct for albumin**; ionized is separate |
| CRP (≠ **hs-CRP**) | mg/dL→mg/L ×10 | CRP <10; hs-CRP <3 mg/L | **CRP vs hs-CRP differ 10× — never trend together** |

Sources: [SI conversions – Kidney Int Suppl](https://www.kisupplements.org/article/S2157-1716(16)30001-6/fulltext),
[NCBI NBK83505](https://www.ncbi.nlm.nih.gov/books/NBK83505/), [Testing.com CMP](https://www.testing.com/tests/comprehensive-metabolic-panel-cmp/),
[Medscape lab ranges](https://emedicine.medscape.com/article/2172316-overview), [NGSP HbA1c](https://ngsp.org/ifccngsp.asp),
[AcuteCareTesting urea/BUN](https://acutecaretesting.org/en/articles/urea-and-the-clinical-value-of-measuring-blood-urea-concentration), UNITSLAB conversion pages.

---

## 3. Imaging / ECG / Echo / TMT — plain-language meanings (calm, never a diagnosis)

**Commonly benign** (say so plainly to reduce needless alarm, still "confirm with your doctor"):
grade I fatty liver · simple liver/kidney cyst · gallstones that are "silent" · colloid / low-TI-RADS
thyroid nodule · sinus bradycardia in fit people or sleep · sinus arrhythmia · incomplete RBBB · occasional
ectopics/PVCs · trace/mild mitral or tricuspid regurgitation · grade I diastolic dysfunction · aortic-valve
sclerosis · normal ejection fraction (~50–70%) · "negative" TMT · ≥10 METs · refractive error · cup-to-disc ~0.3.

**Worth asking the doctor about** (calmly, never diagnosing): LBBB · frequent PVCs · positive stress test ·
ejection fraction <50% · concentric LVH · raised intraocular pressure · cup-to-disc >0.6 · hepatomegaly ·
higher TI-RADS nodules.

**Jargon glossary:** *steatosis* = fat buildup; *echogenicity* = brightness on ultrasound; *cyst* = fluid sac;
*-megaly* = enlargement; *nodule* = small lump; *TI-RADS* = thyroid ultrasound risk score (1 benign → 5 suspicious);
*sinus* = the heart's natural pacemaker; *brady/tachy* = slow/fast; *bundle branch block* = delay in an electrical
"wire"; *LVH* = thickened main chamber; *PVC* = early extra beat; *regurgitation* = small backward valve leak;
*ejection fraction* = % of blood pumped per beat; *diastole* = the heart's filling phase; *ischemia* = reduced
blood flow; *MET* = unit of exercise effort; *IOP* = eye pressure; *cup-to-disc* = optic-nerve measure.

Sources: Cleveland Clinic ([fatty liver](https://my.clevelandclinic.org/health/diseases/15831-fatty-liver-disease),
[bradycardia](https://my.clevelandclinic.org/health/diseases/17841-bradycardia),
[RBBB](https://my.clevelandclinic.org/health/diseases/21692-right-bundle-branch-block),
[ejection fraction](https://my.clevelandclinic.org/health/diagnostics/16950-ejection-fraction)),
[American Liver Foundation – cysts](https://liverfoundation.org/liver-diseases/complications-of-liver-disease/liver-cysts/),
[RSNA TI-RADS](https://pmc.ncbi.nlm.nih.gov/articles/PMC11374223/), [≥10 METs & risk](https://pmc.ncbi.nlm.nih.gov/articles/PMC2826719/),
[AAO – glaucoma/IOP](https://www.aao.org/eye-health/diseases/what-is-glaucoma).

---

## 4. Prescription explainer — "what it's generally for" (purpose only; NO doses, NO advice)

List what's written; explain purpose; for anything about *taking* it say "ask your doctor or pharmacist."

| Drug / supplement | Generally used for |
|---|---|
| Metformin | helps control blood sugar (type 2 diabetes) |
| Atorvastatin / statins | lower cholesterol → reduce heart-attack/stroke risk |
| Amlodipine | lowers blood pressure (relaxes vessels) |
| Lisinopril / Ramipril (ACE inhibitors) | lower BP; protect heart/kidneys |
| Losartan / Telmisartan (ARBs) | lower BP (ACE-inhibitor alternative) |
| Metoprolol / Bisoprolol (beta-blockers) | steady the heart; BP, angina, palpitations |
| Hydrochlorothiazide / Indapamide | "water tablets" — lower BP |
| Omeprazole / Pantoprazole (PPIs) | reduce stomach acid (reflux, ulcers) |
| Levothyroxine | replaces thyroid hormone (underactive thyroid) |
| Aspirin (low-dose) | thins blood to help prevent clots |
| Clopidogrel | antiplatelet — helps prevent clots |
| Salbutamol / Albuterol (inhaler) | opens airways (wheeze/breathlessness) |
| Amoxicillin / Azithromycin | antibiotics for bacterial infections |
| Paracetamol / Acetaminophen | eases pain, lowers fever |
| Ibuprofen (NSAID) | eases pain, swelling, fever |
| Gabapentin / Pregabalin | calm nerve pain (some seizure/anxiety uses) |
| Sertraline / Escitalopram (SSRIs) | help with depression/anxiety |
| Levocetirizine / Cetirizine | ease allergy symptoms |
| Vitamin D3 | supports bones; helps use calcium |
| Vitamin B12 | supports nerves & red blood cells |
| Iron / Ferrous salts | treat/prevent iron-deficiency anaemia |
| Folic acid (B9) | red-blood-cell formation; common in pregnancy |
| Calcium (± vit D) | supports bone health |

Sources: [NHS Medicines A–Z](https://www.nhs.uk/medicines/), [Drugs.com](https://www.drugs.com/), [MedlinePlus](https://medlineplus.gov/).

---

## 5. Extraction / OCR robustness checklist

- **Character/number corruption** (`I↔l`, `8↔B`, `0↔O`, dropped digits) — re-check flagged/critical + a random
  sample against the image; range-bound each marker and re-verify outliers.
- **Abnormal flags** (`H`/`L`/`*`/`A`/`↑↓`/bold/colour) — capture verbatim **and** independently re-derive
  in/out-of-range; a bare `*`/`A` = "abnormal, direction unknown" (keep, don't guess).
- **Censored values** (`>`/`<`/`≥`/`≤`) — store the comparator; never plot/trend as a plain number.
- **"See comment" / footnotes** — link the note to its value; never extract the value alone.
- **Multi-column / multi-page / multi-photo** — group all parts of one report; consolidate columns before
  field-splitting; reconcile derived ratios against components.
- **Units/method printed separately** — resolve the governing unit from headers; never assume a default.
- **Sex/age reference rows** — pick the row matching the confirmed profile; if unknown, keep the whole block
  and mark `[unclear]` rather than guessing.
- **Decimal vs thousands separator** (`1,5` EU = `1.5`) and **date format** (`DD/MM` vs `MM/DD`) — infer per
  report; store dates ISO `YYYY-MM-DD`; auto-resolve only when one reading is impossible, else ask.
- **Ditto marks** (`"`, `-do-`, blank-under) — expand to the referenced value; never read as empty/zero.
- **Repeated measures** (pre/post, fasting/PP, L/R) — keep as distinct datapoints; never average or trend
  pre against post. **Duplicate scans** — count once.
- **Handwriting** — treat drug/dose as low-confidence; transcribe what's written, validate names, ask the user;
  never autocorrect to a similar drug.

Sources: [OCR in healthcare – LlamaIndex](https://www.llamaindex.ai/blog/ocr-in-healthcare-automating-patient-data),
[PMC10629084](https://pmc.ncbi.nlm.nih.gov/articles/PMC10629084/), [flagging abnormal results](http://www.gregoryschmidt.ca/writing/flag-abnormal-lab-results-why),
[censored values – OpenSAFELY/PMC10904973](https://pmc.ncbi.nlm.nih.gov/articles/PMC10904973/),
[prescription OCR](https://www.handwritingocr.com/blog/prescription-ocr-doctors-handwriting), [ISO 8601](https://calculatorian.com/en/articles/time-and-date/iso-8601-date-format-guide).
