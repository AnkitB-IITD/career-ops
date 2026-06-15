# Project Plan: Cross-Border Decline Intelligence (Taxonomy + Dashboard)

**Date:** 2026-06-11
**Target:** Glomo PM role (report 001) + broader cross-border fintech positioning
**Verdict from /career-ops project:** BUILD (4.45/5)
**Optimization goal:** Real-world impact in cross-border txn space, not just interview artifact

## Core reframe

The product is NOT the dashboard. The product is an **open, unified, cross-rail decline taxonomy** — no public mapping exists across ISO 20022, SWIFT gpi, NACHA, SEPA, NPCI/UPI, card network, FPS, and FAST failure codes. Every cross-border fintech rebuilds this internally. The dashboard is the demo layer on top.

## Unified schema (per code)

- Leg: collection / FX / correspondent / disbursement
- Root category: funds, compliance (AML-CFT), data quality, technical, limits/regulatory
- Retry-ability: retryable as-is / retryable after fix / terminal
- Owner: payer, merchant ops, compliance, banking partner, internal eng
- Customer-facing message

## Status (updated 2026-06-11)

- ✅ **Phase 1 BUILT** — taxonomy.json with 166 codes (ISO 41, ACH 30, UPI 33, card 32, FPS 15, FAST 15), sources.md, schema.md, README. Local repo: `cross-border-txns-decline-intelligence/` (git initialized, commit c99d0f2).
- ✅ **Phase 2 BUILT** — Decline Intelligence dashboard (3 views: corridor health, failure drill-down, ops routing queue). Verified working in browser, zero console errors, runs live on taxonomy.json.
- ✅ **Phase 3 DRAFTED** — article draft (`interview-prep/decline-intelligence-article-draft.md`) + outreach drafts (`interview-prep/decline-intelligence-outreach.md`: Akash Arun, Lijo Varghese, practitioner template, LinkedIn post).
- ⬜ **USER ACTIONS PENDING** — create GitHub repo + push, enable GitHub Pages, publish article, send outreach (checklist in interview-prep/decline-intelligence-outreach.md).

## Phases

### Phase 1 — Ground truth (Week 1) [NON-NEGOTIABLE CORE]
- Collect public code sets: ISO 20022 ExternalStatusReason1Code, SWIFT gpi statuses, NACHA R01-R85, SEPA rejects, NPCI UPI errors, Visa/MC response codes, UK FPS + SG FAST rejects
- Scope guard: start with Glomo corridors — India (NEFT/RTGS/UPI), US (ACH), UK (FPS), SG (FAST), ISO 20022 as spine
- Map into unified schema; publish as JSON/CSV in public GitHub repo `cross-border-txns-decline-intelligence`

### Phase 2 — Demo layer (Week 2)
- Realistic simulated txn set (failure distributions seeded from published benchmarks, assumptions documented)
- Dashboard: corridor health → leg drill-down → impact-ranked culprits → ops routing queue
- PRD written LAST, grounded in the taxonomy as data contract

### Phase 3 — Distribution & validation (Weeks 3-4)
- Deep-dive article: "Why cross-border payments fail: a unified taxonomy across 7 rails"
- Practitioner validation: 8-10 ops/product people at Glomo, Nium, Skydo, Wise, BriskPe — ask "does this match your reject files?"
- Targeted send to Glomo: founder Akash Arun + TA Lijo Varghese, one-liner + repo + article
- Iterate from feedback, tag v1.0

## Success metrics (in order)
1. Practitioner conversations held (target 5+)
2. Taxonomy corrections from people with real reject-file access
3. Article reach within cross-border fintech niche
4. Interviews where artifact is discussed (incl. Glomo)
5. Repo references/forks (slowest, don't optimize)

## Risks
- Scope blowup → corridor-first scoping (Glomo's rails only at v0)
- Taxonomy errors → version it; public corrections = engagement loop
- Day-job time → Phase 1 alone already beats a Figma+PRD plan
- Glomo closes early → apply now, ship article mid-process; artifact helps at interview stage

## STAR bridge (use in interviews)
ICICI UPI SMS failure 7.4%→0.9% via RCA on one leg of a domestic stack → cross-border has 3+ legs, each with its own compliance regime and failure language → "so I mapped the failure space publicly."
