# Example (100% synthetic)

Everything in this folder is **made up** — a fictional person ("Rahul"), invented lab values,
and fake clinics. It exists only to show **what the protocol produces** before you run it on your
own data. No real patient information is here.

## What's inside

```
examples/
├── sample-reports/        ← fake "input" — the kind of files you'd drop in a health folder
│   ├── 2024-02-10_blood_annual-checkup.md
│   └── 2024-08-12_blood_followup.md
└── sample-output/         ← trimmed "output" the protocol generates
    ├── sample_extracted_data.md     (per-marker extraction, with sources)
    ├── sample_health_analysis.md    (the Markdown synthesis report)
    └── sample_dashboard.html        (a small, offline demo dashboard — open in any browser)
```

## How to read it

1. Look at `sample-reports/` — that's what a user provides (here as plain text; real ones are
   usually PDFs/photos).
2. `sample-output/sample_extracted_data.md` shows every value traced back to its source file.
3. `sample-output/sample_health_analysis.md` is the plain-language report (the source of truth).
4. Open `sample-output/sample_dashboard.html` in any browser — it works fully offline.

> Reminder: informational organization only, **not** medical advice. The real protocol never
> diagnoses and asks you to confirm anything unclear.
