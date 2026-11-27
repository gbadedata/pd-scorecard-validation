# Changes

## Fixed 24-month outcome window (Nov 2026)

**Why.** The earlier version used finished loans only (fully paid or charged off). Loans that default finish early, so in recent vintages the finished loans were disproportionately the bad ones. The effect was strongest for 60-month loans: among finished loans issued up to 2016, 33% of 60-month loans had charged off against 16% of 36-month loans, compared with 13.7% and 9.6% defaulting within 24 months. That bias made loan term the strongest predictor and distorted every headline metric.

**What changed.**
- `lending_club.py` (new, shared with the other credit projects) dates each default at last payment plus four months and records the snapshot (March 2019).
- `build_dataset.py` keeps all 2.26M loans and adds 12- and 24-month outcome columns with an "observable" flag.
- `analysis.py` defines bad as default within 24 months, on the 1.22M loans whose window had passed. Development is loans issued up to 2014; out-of-time is 2015-2016.
- `analysis.py` is written in notebook cell format; `make_notebook.py` rebuilds and executes the notebook from it.
- README and VALIDATION_REPORT.md rewritten for the new results, with a new section 4.6 on the outcome definition. The segment rating is now labelled a judgement, as no threshold is coded for it.

**Numbers.**

| | Before (finished loans) | After (24-month window) |
|---|---|---|
| Development sample | 829,350 issued 2007-2015, 18.5% bad | 466,340 issued up to 2014, 9.4% bad |
| Out-of-time sample | 518,744 issued 2016-2018 | 751,956 issued 2015-2016, 11.6% bad |
| Characteristics kept | 12 (term IV 0.238 first) | 10 (FICO IV 0.105 first; term 0.039) |
| Gini development / out-of-time | 0.385 / 0.356 | 0.318 / 0.320 |
| KS out-of-time | 0.256 | 0.228 |
| Predicted / observed PD | 0.77 (RED) | 0.79 (RED) |
| PSI | 0.013 | 0.003 |
| Challenger Gini (gap) | 0.401 (+0.045) | 0.360 (+0.040) |
| Segment Gini range | 0.31-0.37 | 0.29-0.34 |
| Score range (median) | 465-599 (536) | 486-614 (556) |

The validation opinion is unchanged (approved with conditions; calibration is the material finding), but discrimination is lower than previously reported and only just clears the 0.30 standard.
