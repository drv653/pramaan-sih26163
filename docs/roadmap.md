# Pramaan Engineering Roadmap & Implementation Status

This document tracks the implementation progress of Pramaan against the technical build plan outlined in **PRD §22**. In strict adherence to our project honesty principles, all planned features are explicitly distinguished from what is currently built.

---

## Status Legend
- ✅ **Built (Prototype):** Fully functional in the interactive web prototype, live deployment, or repository.
- 📐 **Designed in PRD:** Complete technical specification, schema, and interface defined in the PRD; awaiting engine implementation.
- 🗺 **Roadmap (P2):** Long-term architectural vision; explicitly not built.

---

## 1. P0 — Demo-Critical Baseline

| # | Feature / Milestone | PRD Section | Status | Notes |
|---|---|---|:---:|---|
| 1 | **Repository skeleton, schema, and baseline setup** | §8, §18.3 | 📐 Designed in PRD | Postgres/SQLite schema and SHA-256 hash-chained audit log specified in PRD §8. |
| 2 | **`worldmonitor_local` TargetAdapter & Docker sandbox** | §14.1 | 📐 Designed in PRD | Target adapter interface defined; Docker Compose environment with stub feeds designed. |
| 3 | **Target Profiler** | §7.2 | 📐 Designed in PRD | Fingerprints deployment surfaces, authentication modes, and active controls. |
| 4 | **Core check framework & initial 25 checks** | §7 | 📐 Designed in PRD | Seven PS domain modules mapped to CWE/OWASP; catalog data structures designed. |
| 5 | **Validator state machine & FindingSchema enforcement** | §9, §10.1 | 📐 Designed in PRD | Mandatory 8-field gate and state transitions (`DETECTED → CONFIRMED → CLOSED`). |
| 6 | **Safe PoC Runner with Canary Listener** | §11 | 📐 Designed in PRD | Non-destructive `canary`, `header_proof`, and `version_proof` execution strategies. |
| 7 | **Deterministic CVSS 3.1 & Posture Scoring** | §10.3, §10.4 | 📐 Designed in PRD | 0–100 posture score formula and environmental RiskRank algorithm specified. |
| 8 | **Interactive Web Dashboard (Screens S0–S7, S9)** | §17.1 | ✅ Built | Complete clickable web prototype displaying overview, findings, coverage, posture score, and audit log. |
| 9 | **Golden Lab fixture suite with clean controls** | §21 | 📐 Designed in PRD | 30+ seeded vulnerabilities and 30+ clean controls for precision/recall benchmarking. |
| 10 | **Executive and Technical PDF reporting** | §16.1 | 📐 Designed in PRD | WeasyPrint report templates and export schemas designed. |
| 11 | **Public live deployment & demonstration video** | §17 | ✅ Built | Live demo hosted on Vercel; walkthrough video recorded and published on YouTube. |

---

## 2. P1 — High-Priority Enhancements

| # | Feature / Milestone | PRD Section | Status | Notes |
|---|---|---|:---:|---|
| 12 | **Differential PoC strategy for authorization checks** | §11.1 | 📐 Designed in PRD | Dual synthetic identities for BOLA/IDOR verification. |
| 13 | **Remediation Engine with fix diffs & regression tests** | §12 | 📐 Designed in PRD | Automated code diff generation and Playwright/pytest test scripts. |
| 14 | **Catalog expansion to 40–60 checks + ZAP baseline** | §8.2 | 📐 Designed in PRD | Broader check coverage and optional OWASP ZAP baseline engine integration. |
| 15 | **SARIF 2.1.0 and issue-tracker export** | §14.5 | 📐 Designed in PRD | Interoperability with GitHub Code Scanning and issue trackers. |
| 16 | **Multilingual executive reporting (Hindi localization)** | §16.3 | 📐 Designed in PRD | Pre-translated Hindi static templates with Noto Sans Devanagari font embedding. |
| 17 | **Interactive Audit Chain verification screen (S10)** | §18.3 | ✅ Built (UI) | Prototype includes audit log view and interactive hash-chain verification UI. |
| 18 | **Air-gapped local AI Gateway (Ollama / serverless)** | §15 | 📐 Designed in PRD | Prompt-templated summarizer with 2500ms static fallback. |

---

## 3. P2 — Long-Term Roadmap (Explicitly Not Built)

| # | Feature / Milestone | PRD Section | Status | Notes |
|---|---|---|:---:|---|
| 19 | **Continuous monitoring mode & drift detection** | §17.1 (S11) | 🗺 Roadmap | Wireframe / mock view included in prototype; engine not built. |
| 20 | **Mobile application binary analysis (APK / IPA)** | §22 | 🗺 Roadmap | Static and dynamic analysis of mobile client binaries. |
| 21 | **Remote staging targets with signed owner authorization** | §14.1 | 🗺 Roadmap | Production stance strictly forbids remote targets in current releases. |
| 22 | **AI-assisted vulnerability triage suggestions** | §22 | 🗺 Roadmap | Advisory suggestion layer; will never make definitive severity or validity decisions. |
| 23 | **Multi-target fleet dashboard & enterprise analytics** | §22 | 🗺 Roadmap | Aggregated reporting across multiple deployments and organisations. |
| 24 | **CVSS v4.0 scoring integration** | §14.3 | 🗺 Roadmap | Side-by-side v3.1 and v4.0 scoring matrices. |
