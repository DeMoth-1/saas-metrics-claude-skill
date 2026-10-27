---
name: saas-metrics
description: Compute and diagnose SaaS subscription metrics (MRR/ARR, expansion/contraction/churn waterfalls, NRR/GRR, CAC payback, LTV:CAC, burn multiple, Rule of 40) from raw billing or customer-level data, with audit trail from every headline number back to source rows. Use when the user mentions MRR, churn, retention, CAC/LTV, or asks "are these metrics good". NOT for building forecast models (see three-statement-model) or non-subscription businesses.
---

# SaaS Metrics Engine

Every SaaS metric is a claim about money. Your job is to make each headline number traceable to raw records, and to refuse metrics the data cannot support.

## Data intake — classify before computing

From billing/CRM export or customer-level table, classify every record into exactly one bucket per period:

- **New** (first-ever paying account), **Expansion** (upgrade/seat increase vs prior period), **Reconnection** (returned after churn — report separately, never as New), **Contraction** (downgrade), **Churn** (cancelled or lapsed beyond grace), **Existing base**.
- Monthly price × 12 = ARR by convention; MRR excludes one-time fees, services, and taxes — ALWAYS. If the export mixes them, separate first and say so.
- Segment by plan/cohort where the data allows; cohort view beats blended for every diagnostic below.

## Core computations (with the traps)

**MRR waterfall**: `MRR_end = MRR_begin + new + expansion + reconnection − contraction − churn`. Waterfall must reconcile exactly; a residual row of more than rounding means a misclassified record — hunt it down before reporting anything.

**Logo vs revenue churn**: compute both. Logo churn ≫ revenue churn = small customers leaving (SMB churn problem); the reverse = big accounts leaving (enterprise concentration risk). Different diseases, different medicine.

**NRR/GRR** (cohort-based, trailing 12m where data allows):
- `GRR = (beginning MRR − churn − contraction) / beginning MRR` — can never exceed 100%. If yours does, you computed it wrong.
- `NRR = GRR + expansion/beginning MRR` — 100-120% healthy, >130% excellent for SMB SaaS, enterprise can run higher.
- Trap: NRR measured on ALL customers vs on customers who existed a year ago are different numbers; always state the base. Never blend cohorts of different ages into one "NRR".

**CAC payback**: `months = (S&M cost of the period) / (new ARR added in period × gross margin)`. Blended CAC from total S&M is acceptable for reporting; CAC by channel is what decisions need. Flag if S&M expense accrual period and revenue start period are misaligned (prepaid annual = payback overstated).

**LTV:CAC**: `LTV = ARPA × gross margin % / revenue churn %` (use churn as decimal, same period basis). Cap displayed LTV at 3-5× ARPA lifetime sanity bound; a 10-year LTV assumption is fiction in SaaS. Healthy ≥ 3. State the churn basis (logo vs revenue) — it changes the answer by 2×.

**Burn multiple** (if the company is loss-making): `net burn / net new ARR`. <1 great, 1-2 normal, >3 broken — investigate CAC or churn before raising spend. **Rule of 40**: growth % + FCF margin %. Report, don't judge alone (stage matters).

## Diagnosis patterns (what you say after computing)

- NRR >110% but growth flat → new-business pipeline problem, not retention problem.
- Payback lengthening while churn flat → CAC inflation; check channel mix.
- Logo churn high, NRR fine → monetize the top, fix SMB onboarding or reprice tiers.
- Seasonality: always ask whether the quarter-end cohorts are real or billing-cycle artifacts before declaring a trend. 3 points make a line in SaaS churn — require 6+ months before calling inflection.

## Output format

1. **Headline table**: MRR/ARR, NRR, GRR, logo churn, revenue churn, CAC payback, LTV:CAC, burn multiple (if applicable), each with period and basis noted.
2. **MRR waterfall chart data** (period × component).
3. **Cohort retention table** (if customer-level data present): rows = cohort month, columns = month-since-acquisition, values = retained revenue %.
4. **Audit trail note**: for each headline metric, one line stating source table, row filter, and formula. If a metric cannot be computed from provided data, say `insufficient data` and list exactly what's missing — a missing metric honestly reported beats an invented one every time.

## Anti-patterns

- Reporting "churn" without saying logo vs revenue, or blending them silently
- Counting reactivations as new logos (inflates both new and churn later)
- Including one-time/services revenue in MRR
- NRR > 100% from a formula error presented as "great retention"
- LTV built on annual churn applied monthly (unit-period mismatch — the classic 2-3× overstatement)
