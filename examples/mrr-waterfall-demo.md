# Worked Example: MRR Waterfall + NRR/GRR

This is the kind of output the `saas-metrics` skill produces from a raw customer-level export. Numbers are illustrative; the format and checks are what the skill enforces.

## Input (excerpt of customer-level billing export)

| customer | plan | mrr_2025_09 | mrr_2025_12 | note |
|---|---|---|---|---|
| Acme | Pro | 4,000 | 6,000 | upgraded seats |
| Bee Inc | Starter | 800 | 0 | cancelled |
| Cee Corp | Pro | 4,000 | 4,000 | unchanged |
| Dee Ltd | Starter | 500 | 700 | expanded |
| Eff Co | Pro | 0 | 4,000 | new (first-ever account) |
| Gee Gmbh | Starter | 0 | 800 | returned after churn (reconnection — NOT new) |

## Step 1 — Classify every record (one bucket per period)

- New: Eff Co (+4,000)
- Expansion: Acme (+2,000), Dee Ltd (+200)
- Reconnection: Gee Gmbh (+800) — reported separately, never counted as New
- Contraction: —
- Churn: Bee Inc (−800)
- Existing base carryover: Cee Corp

## Step 2 — MRR waterfall (must reconcile exactly)

```
MRR_begin (Sep)          9,300
  + new                  +4,000   (Eff Co)
  + expansion            +2,200   (Acme 2,000 + Dee 200)
  + reconnection           +800   (Gee Gmbh)
  − contraction              −0
  − churn                 −800   (Bee Inc)
= MRR_end (Dec)         15,500

Check: sum of customer-level Dec MRR = 6,000+0+4,000+700+4,000+800 = 15,500 ✓
Residual = 0 → no misclassified records. If it weren't, hunt the record before reporting.
```

## Step 3 — Retention metrics (with the base stated)

```
GRR = (beginning MRR − churn − contraction) / beginning MRR
    = (9,300 − 800 − 0) / 9,300 = 91.4%
    → below 100%, as it must be. A GRR above 100% means you computed it wrong.

NRR (on the Sep-2025 cohort, trailing) = GRR + expansion/beginning
    = 91.4% + 2,200/9,300 = 115.1%
    Base stated: customers who existed in Sep-2025. Blending all customers into one "NRR" hides this.
```

## Step 4 — Diagnosis (what you say after computing)

- NRR 115% while revenue nearly doubled → growth is acquisition-led; retention is healthy but not the engine.
- Logo churn 1/6 ≈ 17% vs revenue churn 8.6% → the leaver was small (SMB churn pattern). If reversed, suspect enterprise concentration risk.
- Rule of thumb: 3 points make a line in SaaS churn — require 6+ months before calling any inflection.

## What the skill refuses to do

- Report "churn" without saying logo vs revenue
- Count Gee Gmbh's return as a New logo (it would inflate both new and later churn)
- Include one-time services revenue in MRR
- Present NRR > 100% from a formula error as "great retention"
