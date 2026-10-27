# SaaS Metrics Skill for Claude Code

> Most SaaS metric tools will happily tell you your NRR is 140%. This one tells you when you computed it wrong.

A production-grade **Claude Code skill** that computes MRR/ARR waterfalls, NRR/GRR, CAC payback, LTV:CAC, burn multiple and Rule of 40 from your raw billing or customer-level export — and refuses to report a number it can't trace back to source rows.

## Why it's not "just a prompt"

Every metric carries the traps that actually happen in SaaS finance, built in as guardrails:

- **NRR/GRR on the correct base** — GRR above 100% is flagged as a formula error, not "great retention"
- **MRR waterfall must reconcile exactly** — a residual beyond rounding means a misclassified record, and the skill hunts it down before reporting anything
- **LTV capped at a sanity bound** — no 10-year-lifetime fictions, no annual-churn-applied-monthly 2–3× overstatement
- **Logo churn vs revenue churn, computed separately** — different diseases, different medicine
- **One-time/services revenue excluded from MRR — always**, and reconnections reported separately from new logos
- **Audit trail on every headline number** — source table, row filter, formula

If a metric can't be computed from your data, it says `insufficient data` and lists exactly what's missing. An honestly-missing metric beats an invented one.

## Install (30 seconds)

```bash
git clone https://github.com/DeMoth-1/saas-metrics-claude-skill.git
mkdir -p ~/.claude/skills
cp -r saas-metrics-claude-skill/saas-metrics ~/.claude/skills/
```

Then ask Claude Code naturally:

- *"compute MRR and NRR from this export"*
- *"why is churn up this quarter?"*
- *"are these metrics good for our stage?"*

Feed it a CSV/Excel export of billing or customer-level records and it will classify every record (new / expansion / reconnection / contraction / churn), build the waterfall, and diagnose.

## Diagnosis patterns it knows

- NRR >110% but growth flat → new-business pipeline problem, not retention
- Payback lengthening while churn flat → CAC inflation, check channel mix
- Logo churn high, NRR fine → monetize the top, fix SMB onboarding
- 3 points make a line in SaaS churn — it requires 6+ months before calling an inflection

## Free skill vs the full FP&A Agent Kit

This is 1 of 6 skills in the **[FP&A Agent Kit](https://yrrsignup.gumroad.com/l/fpa-agent-kit)** ($29, one-time):

| Skill | What it does |
|---|---|
| `three-statement-model` | Linked IS/BS/CF with a Checks sheet that must read TRUE every period |
| **`saas-metrics`** | ← this free skill |
| `budget-vs-actual` | Rate/volume/mix decomposition + the 6-bullet MBR commentary |
| `scenario-analysis` | Coherent scenarios, tornado ranking, two-way sensitivity |
| `dcf-valuation` | Both terminal methods with reconciliation, reverse-DCF cross-check |
| `cash-flow-13w` | Probability-weighted collections, 4-zone liquidity triggers |

The full kit also ships `sample-three-statement-model.xlsx` — a complete 4-year model built to the skill's own spec: live formulas, zero hardcodes, all four years balancing to the cent.

→ **[Get the FP&A Agent Kit](https://yrrsignup.gumroad.com/l/fpa-agent-kit)** — launch week: 25% off with code `LAUNCH25` (ends Nov 3)

⭐ Star this repo if it saved you a spreadsheet fight.

## License

[CC BY-NC 4.0](./LICENSE) — use it freely in your own work (including commercial analysis work); don't sell or redistribute the skill file itself.
