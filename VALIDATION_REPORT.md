# Independent Validation: PD Application Scorecard

**Model:** Probability-of-default application scorecard (Weight-of-Evidence logistic, scaled to points)
**Validation type:** Independent second-line review (effective challenge)
**Data:** 1,348,094 real loans, development on 2007 to 2015 vintages (829,350), out-of-time validation on 2016 to 2018 vintages (518,744)
**Prepared by:** Oluwagbade Odimayo

---

## 1. Executive summary and validation opinion

The scorecard is methodologically sound and discriminates well and stably. It rank-orders default risk with an out-of-time Gini of 0.36, its score distribution is stable over time (PSI 0.013), and it holds up against a gradient-boosting challenger. One material issue was found: the model **under-predicts the level of default on recent vintages** by roughly a quarter (predicted-to-observed PD ratio of 0.77), because it was developed on older, lower-default vintages.

**Overall opinion: APPROVED WITH CONDITIONS.**

- **Fit for use** in rank-ordering and approval cut-off decisions, where only the ordering of risk matters.
- **Not yet fit for use** in applications that depend on the absolute PD level (risk-based pricing, IFRS 9 expected credit loss) until the central tendency is recalibrated to current conditions.

| Validation area | Metric | Result | Standard | Rating |
|---|---|---|---|---|
| Discrimination | Gini, out-of-time | 0.356 | above 0.30 acceptable | GREEN |
| Calibration | predicted / observed PD | 0.77 | 0.90 to 1.10 | RED |
| Stability | PSI, development vs recent | 0.013 | below 0.10 stable | GREEN |
| Effective challenge | Gini gap to challenger | +0.045 | below 0.05 | GREEN |
| Segment performance | Gini across segments | 0.31 to 0.37 | within tolerance | GREEN |

---

## 2. Scope and approach

This review independently re-performs the key tests a model owner should expect from a second line of defence: discrimination, calibration, stability, benchmarking against an alternative model, and performance across segments. Testing is on an out-of-time sample (2016 to 2018) that the model was not built on, so the results reflect how the model behaves on data it has not seen. The model was rebuilt from the development data to confirm it reproduces, then challenged.

---

## 3. Model overview

The scorecard follows standard industry construction. Each characteristic is binned and converted to a Weight of Evidence, predictors are selected by Information Value, a logistic regression is fitted on the WoE values, and the result is scaled to a points-based score (20 points to double the odds, anchored at 600 points for odds of 50 to 1). Twelve characteristics were retained (Information Value at or above 0.02), led by loan term (IV 0.238), loan-to-income (0.122), FICO (0.112), payment-to-income (0.080) and debt-to-income (0.071). The platform's own grade and interest rate were excluded so the model earns its signal independently. Scores run from roughly 465 to 600, with a median near 536.

---

## 4. Findings by area

### 4.1 Discrimination: GREEN
The scorecard separates good from bad accounts well and the performance is durable out of time.

| Sample | AUC | Gini | KS |
|---|---|---|---|
| Development (2007 to 2015) | 0.692 | 0.385 | 0.277 |
| Out-of-time (2016 to 2018) | 0.678 | 0.356 | 0.256 |

The modest drop from development to out-of-time (Gini 0.385 to 0.356) is normal and small. Default rate falls smoothly from about 42% in the lowest score band to about 5% in the highest, confirming a clean, monotonic ranking. **Conclusion: discrimination is satisfactory and stable.**

### 4.2 Calibration: RED (material finding)
While the ordering is good, the predicted PD level is too low. Across the out-of-time book the predicted-to-observed PD ratio is 0.77, and on the calibration plot every score band sits above the diagonal, meaning observed default exceeds the model's prediction in every band. The cause is structural: the model was developed on 2007 to 2015 vintages, whose central default rate is below that of the 2016 to 2018 book, so the model's central tendency is anchored too low for current conditions.

A formal goodness-of-fit statistic is not relied on here, because at this sample size (over 500,000 accounts) such tests reject calibration on negligible differences. The predicted-to-observed ratio and the calibration plot are the appropriate evidence. **Conclusion: the model is miscalibrated on the level of risk and must not be used for PD-level applications until recalibrated.**

### 4.3 Stability: GREEN
The Population Stability Index between the development and out-of-time score distributions is 0.013, well inside the stable range (below 0.10). The mix of applicants by score has not shifted.

This is worth stating alongside the calibration finding, because the two are easy to confuse. The shape of the score distribution is stable (PSI is low), but the default rate associated with each score has risen. Stability of the population does not imply calibration of the PD, and here the population is stable while the central tendency has drifted. **Conclusion: population stability is satisfactory; it does not offset the calibration finding.**

### 4.4 Effective challenge: GREEN (with a watch item)
A gradient-boosting challenger was built on the raw characteristics and compared out of time. It reaches a Gini of 0.401 against the scorecard's 0.356, a gap of 0.045. The gap sits just inside tolerance, so the scorecard's discrimination is not being materially beaten, and its transparency and ease of governance justify retaining it. The gap does indicate some non-linear signal the linear scorecard leaves on the table. **Conclusion: the scorecard is competitive; treat the challenger gap as a watch item at the next redevelopment.**

### 4.5 Segment performance: GREEN
Discrimination is consistent across segments out of time: Gini of 0.32 and 0.31 for 36-month and 60-month terms, and 0.34 to 0.37 across the main loan purposes, all close to the overall 0.36. **Conclusion: no segment is materially under-served by the model.**

---

## 5. Findings and recommendations

| # | Finding | Severity | Recommendation |
|---|---|---|---|
| 1 | Predicted PD under-states observed default on recent vintages (P/O 0.77) | High | Recalibrate the central tendency to current through-the-cycle conditions, or refit on recent vintages, before any PD-level use (pricing, ECL). Re-validate calibration after. |
| 2 | Score-to-default relationship has drifted while population is stable | Medium | Add a quarterly calibration check (predicted vs observed PD) to monitoring, not only a PSI check, so level drift is caught even when the population looks stable. |
| 3 | Challenger shows a small discrimination uplift (+0.045 Gini) | Low | Review non-linear effects (for example the FICO and affordability interaction) at the next redevelopment. Retain the scorecard in the interim for transparency. |
| 4 | Goodness-of-fit statistics are uninformative at this sample size | Low | Standardise on the predicted/observed ratio and the calibration plot for large books, with formal tests reserved for small samples. |

---

## 6. Conclusion

The scorecard is well built and a sound basis for ranking and approval decisions. The single material issue is calibration of the PD level on recent vintages, which is a recalibration matter rather than a design flaw, and which the discrimination and stability results show is isolated to the central tendency. Subject to recalibration and the addition of a calibration check to ongoing monitoring, the model is suitable for wider use.

---

## 7. Caveats

The data is US unsecured consumer lending used to demonstrate the methodology; the approach transfers directly to an auto or captive-finance book, though the characteristics and bins would be re-derived. The out-of-time vintages are partly more recent than the development window, which is the intended test and also the source of the calibration finding. The simplified binning is quantile-based; a production build would use monotonic optimal binning, which would not change the conclusions.

*Reproduce all figures and metrics with `python analysis.py`.*
