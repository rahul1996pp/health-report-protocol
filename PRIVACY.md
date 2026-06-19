# Privacy

**Short version:** this project has **no servers, no accounts, no analytics, and no telemetry**.
It does not collect, store, or transmit your data. Your reports and the generated dashboard stay
in a folder on **your own machine**.

This tool is **informational organization only — not medical advice, diagnosis, or treatment.**

## What this project does NOT do
- It has **no backend, database, or hosted service**, and no account or login.
- It does **not** collect, upload, sell, or share your personal or health information.
- It contains **no analytics, tracking, or telemetry**.
- The generated HTML dashboard is **fully offline** — it makes **zero network requests** and uses
  **no browser storage**.
- The author receives **none** of your data.

## One important caveat — how the analysis runs
The protocol is executed by **Claude Code** on your computer. To produce the analysis, Claude Code
**sends the content it reads (including your report text and values) to Anthropic's Claude models**
— that is simply how the AI works. That processing is governed by **Anthropic's Privacy Policy and
Usage Policies** (<https://www.anthropic.com/legal/privacy>), *not* by this project. This project
adds no data collection of its own. If you do not want your report contents processed by the AI,
do not run the protocol on them.

## Your data, your control
- Your source files, the extracted data, and the dashboard are written only to your **local
  folder** — you can delete them at any time.
- This repository itself contains **no real personal data** — only the protocol and a **100%
  synthetic** example (a fictional person with invented values).
- Nothing is ever published unless **you** choose to share it.

## Contact
Questions or concerns: open an issue at
<https://github.com/rahul1996pp/health-report-protocol/issues>.
