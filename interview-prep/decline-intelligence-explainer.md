# Explainer: How Cross-Border Txns Decline Intelligence Works

*Use this as the "walk me through what you built" interview answer. Repo: github.com/AnkitB-IITD/cross-border-txns-decline-intelligence*

## The one-sentence architecture

The dashboard contains zero payments knowledge of its own — every classification, routing decision, and customer message on screen is a lookup into `taxonomy.json`. Swap the simulated transactions for a real reject feed and the dashboard works unchanged.

## Two layers

1. **Knowledge layer** — `data/taxonomy.json`: 166 decline codes across 6 rails, each with 9 fields (code, rail, native_description, leg, category, retryability, owner, customer_message, iso20022_equivalent). Hand-researched from public rail docs (provenance in `sources.md`). Useful standalone.
2. **Demo layer** — `dashboard/`: simulates a cross-border payments business and applies the taxonomy to its failures. Simulation fake; intelligence real.

## File roles

| File | Role |
|------|------|
| data/taxonomy.json | The product — the "brain" |
| data/taxonomy.csv + make-csv.mjs | Spreadsheet export + validator (`node make-csv.mjs`) |
| data/sources.md | Provenance per code system |
| docs/schema.md | Field definitions + design rationale |
| dashboard/sample-data.js | Fake transaction generator — the "world" |
| dashboard/app.js | Joins world to brain; renders 3 views — the "glue" |
| dashboard/index.html + style.css | Skeleton + visuals, no logic |

## Boot sequence

1. **sample-data.js**: seeded PRNG (`mulberry32(20260611)` — identical data every reload) generates 2,000 txns / 30 days. Corridor mix: IN→US 42%, IN→UK 22%, US→IN 20%, IN→SG 16%. Each txn **walks its legs in order** (collection → correspondent → disbursement), rolling against per-leg failure rates (UPI ~3%, cards ~8%, ACH ~1.5%, ISO correspondent ~0.8%, disbursement 1.1–2.2%). First failing leg wins; txn records `(failedLeg, rail, code)`. Code drawn from weighted per-rail mixes matching real skew (R01 ≈ 45% of ACH collection returns; card 05 dominates). Every rate commented with its benchmark.
2. **app.js**: `fetch('../data/taxonomy.json')` (why the repo must be *served*, not file://), builds dictionary keyed `rail:code`. Header line "taxonomy v0.1.0 · 166 codes · 6 rails" prints from the fetched meta — on-screen proof of the live join.
3. **The join**: declined txn knows only `(rail, code)` — what a real system logs. `lookup()` adds the 5 derived dimensions: category, retryability, owner, customer message, ISO equivalent. Everything on screen is aggregation over this join.

## Example trace (TXN11410)

IN→SG, $10,786, UPI collection leg fails with `ZX` →
taxonomy join: *Inactive or dormant account*, limits_regulatory, retryable_after_fix, owner=payer, ISO equiv AC06 →
- **Corridor health**: counted in IN→SG rate, in Value at Risk, AND in Recoverable (not terminal)
- **Drill-down**: collection-leg row + limits_regulatory row + top-culprits candidate, with AC06 shown for cross-rail comparison
- **Ops queue**: routes to "Customer action needed", near top of high-value worklist, customer message ready to send

One tuple, one join, three answers: where it failed, why, who does what next.

## View → data mapping

**Corridor health**: counts/sums over txns; "Recoverable" KPI = first business number changed by taxonomy (`retryability !== terminal`); sparkline = daily rates, red bar = spike >1.8× corridor average; "worst leg" = failedLeg counts.

**Failure drill-down**: "by leg" uses txn.failedLeg (WHERE) vs "by category" uses taxonomy.category (WHY) — different sources, different questions. Top culprits grouped by rail:code, ranked by **value at risk, not count** (deliberate PM choice: 5 failed $10k payments > 50 failed $10 payments). Corridor dropdown filters before aggregating — IN→UK surfaces FPS codes like ANNM.

**Ops routing queue**: queue cards group by taxonomy.owner (the mis-routing fix) with retryability split; worklist = non-terminal declines sorted by amount, printing taxonomy.customer_message. SLA/aging is mocked (labeled demo behavior).

## Honest boundaries (volunteer these)

- Transactions simulated; banner says so; assumptions commented in sample-data.js
- Code meanings paraphrased from public docs (sources.md) — v0.1 expected wrong where docs diverge from real reject files; that's the feedback loop
- SLA/aging mocked; production would use queue timestamps
