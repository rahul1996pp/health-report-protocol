# health-report-analysis (plugin)

Packages the Health Report Analysis Protocol as a Claude Code **Skill**.

## Install (from the marketplace in this repo)

```bash
/plugin marketplace add rahul1996pp/health-report-protocol
/plugin install health-report-analysis@health-tools
/health-report-analysis
```

## Test locally before publishing

```bash
# from the repo root
/plugin marketplace add ./
/plugin install health-report-analysis@health-tools
/plugin validate .
```

## What it does

Guides Claude through 8 phases — discovery → rename/organize → extraction → resolve unclear
(asking you, one question at a time) → trends → interlinks → Markdown synthesis → a self‑contained
**offline** HTML dashboard → verification — then caches results in `health_memory.json` for cheap
re‑runs.

**Informational only — not medical advice. Never diagnoses. Keeps all data on your machine.**

The full protocol is in `skills/health-report-analysis/SKILL.md`.
