# Extracted Data — Example (SYNTHETIC)

> ⚠️ **Informational organization only — NOT a medical diagnosis.** Every value is transcribed
> from the named source file; reference ranges are copied as printed. Fictional data.

**Patient:** Rahul · Anantapur, India · prefers South-Indian (Rayalaseema) food
*(confirmed by user — fictional)* · three dated panels.

| Marker | 08 Aug 2023 | 10 Feb 2024 | 12 Aug 2024 | Unit | Reference | Latest flag | Source |
|---|---|---|---|---|---|---|---|
| Total Cholesterol | 198 | 210 | 225 | mg/dL | < 200 | **HIGH** | all three checkup files |
| Triglycerides | 145 | 160 | 180 | mg/dL | < 150 | **HIGH** | all three checkup files |
| HDL Cholesterol | 46 | 45 | 44 | mg/dL | ≥ 40 | NORMAL | all three checkup files |
| LDL Cholesterol | 120 | 130 | 140 | mg/dL | < 100 | **HIGH** | all three checkup files |
| HbA1c | 5.3 | 5.5 | 5.7 | % | 4.0–5.6 | **HIGH** (pre-diabetic band) | all three checkup files |
| Fasting Glucose | 90 | 92 | 95 | mg/dL | 70–99 | NORMAL (high-normal) | all three checkup files |
| Vitamin D (25-OH) | 22 | 28 | 35 | ng/mL | 30–100 | NORMAL (was LOW) | all three checkup files |
| TSH | 2.0 | 2.1 | 2.4 | µIU/mL | 0.35–4.94 | NORMAL | all three checkup files |
| Hemoglobin | 14.0 | 14.2 | 14.5 | g/dL | 13.0–17.0 | NORMAL | all three checkup files |
| ALT (SGPT) | 32 | 30 | 28 | U/L | 0–45 | NORMAL | all three checkup files |
| Vitamin B12 | — | — | **>2000** | pg/mL | 200–900 | **HIGH — censored (above the assay's measurable limit)** | 12 Aug 2024 panel |

**Plain-language note (example):** "LDL" is the cholesterol that builds up in artery walls; lower
is better. Here it rose 120 → 130 → 140 (target < 100) — a number to discuss with a doctor.

**Censored value (example):** Vitamin B12 was reported as **">2000"** — above the highest level the
assay can measure. It is kept **with the ">" operator**, shown verbatim, and **never trended,
projected, or treated as the bare number 2000** (a high B12 like this is commonly a supplement effect).

**Imaging (example):** Liver ultrasound, 12 Aug 2024 — *Grade I (mild) fatty liver (hepatic
steatosis).* Source: ultrasound report. Restated in plain words and framed calmly (grade I is the
mild end and commonly improvable); not a diagnosis.

**"Within normal variation" (example):** Hemoglobin 14.0 → 14.2 → 14.5 g/dL looks like a rise, but
the change is smaller than haemoglobin's normal day-to-day variation, so it is marked **stable — not
a real upward trend** (avoids over-reading noise).
