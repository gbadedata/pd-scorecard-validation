# Independent Validation: PD Application Scorecard

**Model:** Probability-of-default application scorecard (Weight-of-Evidence logistic, scaled to points)
**Validation type:** Independent second-line review (effective challenge)
**Data:** 1,218,296 real Lending Club loans whose 24-month outcome was observable by the March 2019 snapshot; development on loans issued up to 2014 (466,340), out-of-time validation on loans issued in 2015-2016 (751,956)
**Outcome:** default within 24 months of issue (charged off, default dated at last payment plus four months)
**Prepared by:** Oluwagbade Odimayo

---

## 1. Executive summary and validation opinion

The scorecard is methodologically sound and ranks risk adequately and stably. It rank-orders default risk with an out-of-time Gini of 0.32, its score distribution is stable over time (PSI 0.003), and it holds up against a gradient-boosting challenger. One material issue was found: the model **under-predicts the level of default on recent vintages** by roughly a fifth (predicted-to-observed PD ratio of 0.79), because it was developed on older, lower-default vintages.

**Overall opinion: APPROVED WITH CONDITIONS.**

- **Fit for use** in rank-ordering and approval cut-off decisions, where only the ordering of risk matters.
- **Not yet fit for use** in applications that depend on the absolute PD level (risk-based pricing, IFRS 9 expected credit loss) until the central tendency is recalibrated to current conditions.

| Validation area | Metric | Result | Standard | Rating |
|---|---|---|---|---|
| Discrimination | Gini, out-of-time | 0.320 | 0.30 or above acceptable | GREEN |
| Calibration | predicted / observed PD | 0.79 | 0.90 to 1.10 | RED |
| Stability | PSI, development vs out-of-time | 0.003 | below 0.10 stable | GREEN |
| Effective challenge | Gini gap to challenger | +0.040 | below 0.05 | GREEN |
| Segment performance | Gini across segments | 0.29 to 0.34 | close to overall (judgement) | GREEN |

---

## 2. Scope and approach

This review independently re-performs the key tests a model owner should expect from a second line of defence: discrimination, calibration, stability, benchmarking against an alternative model, and performance across segments. Testing is on an out-of-time sample (loans issued 2015 to 2016) that the model was not built on, so the results reflect how the model behaves on data it has not seen. The model was rebuilt from the development data to confirm it reproduces, then challenged.

The outcome is defined on a fixed window so that every loan is judged over the same period: a loan is bad if it defaulted within 24 months of issue, and only loans whose full window had passed by the snapshot are used. An earlier version of this review used finished loans only; that sample over-represented early defaults in recent vintages and inflated loan term's apparent predictive power (see section 4.6).

---

## 3. Model overview

The scorecard follows standard industry construction. Each characteristic is binned and converted to a Weight of Evidence, predictors are selected by Information Value, a logistic regression is fitted on the WoE values, and the result is scaled to a points-based score (20 points to double the odds, anchored at 600 points for odds of 50 to 1). Ten characteristics were retained (Information Value at or above 0.02), led by FICO (IV 0.105), payment-to-income (0.074), loan-to-income (0.067), income (0.054) and debt-to-income (0.044); loan term, recent enquiries, credit history length, home ownership and purpose complete the set. The platform's own grade and interest rate were excluded so the model earns its signal independently. Scores run from 486 to 614, with a median of 556.

---

## 4. Findings by area

### 4.1 Discrimination: GREEN
The scorecard separates good from bad accounts adequately, and the performance is durable out of time.

| Sample | AUC | Gini | KS |
|---|---|---|---|
| Development (issued up to 2014) | 0.659 | 0.318 | 0.231 |
| Out-of-time (issued 2015 to 2016) | 0.660 | 0.320 | 0.228 |

Gini is level from development to out-of-time (0.318 to 0.320), so there is no sign of overfitting. The 24-month default rate falls smoothly from 19% in the lowest score band to 2.6% in the highest, confirming a monotonic ranking. The Gini clears the 0.30 standard only narrowly, which is expected for an approved-only population whose FICO scores sit in a narrow band. **Conclusion: discrimination is satisfactory and stable, with little headroom.**

### 4.2 Calibration: RED (material finding)
While the ordering holds, the predicted PD level is too low. Across the out-of-time book the predicted-to-observed PD ratio is 0.79, and on the calibration plot every score band sits above the diagonal, meaning observed default exceeds the model's prediction in every band (for example 23.5% observed against 19.1% predicted in the riskiest band). The cause is structural: the development vintages defaulted at 9.4% within 24 months, against 11.6% for the 2015-2016 book (11.3% for 2015 and 12.1% for 2016), while the model's average predicted PD for those years stayed near 9%. The model's central tendency is anchored too low for current conditions.

A formal goodness-of-fit statistic is not relied on here, because at this sample size (over 750,000 accounts) such tests reject calibration on negligible differences. The predicted-to-observed ratio and the calibration plot are the appropriate evidence. **Conclusion: the model is miscalibrated on the level of risk and must not be used for PD-level applications until recalibrated.**

### 4.3 Stability: GREEN
The Population Stability Index between the development and out-of-time score distributions is 0.003, well inside the stable range (below 0.10). The mix of applicants by score has not shifted.

This is worth stating alongside the calibration finding, because the two are easy to confuse. The shape of the score distribution is stable (PSI is low), but the default rate associated with each score has risen. Stability of the population does not imply calibration of the PD, and here the population is stable while the central tendency has drifted. **Conclusion: population stability is satisfactory; it does not offset the calibration finding.**

### 4.4 Effective challenge: GREEN (with a watch item)
A gradient-boosting challenger was built on the raw characteristics and compared out of time. It reaches a Gini of 0.360 against the scorecard's 0.320, a gap of 0.040. The gap sits inside tolerance, so the scorecard's discrimination is not being materially beaten, and its transparency and ease of governance justify retaining it. The gap does indicate some non-linear signal the linear scorecard leaves on the table. **Conclusion: the scorecard is competitive; treat the challenger gap as a watch item at the next redevelopment.**

### 4.5 Segment performance: GREEN
Discrimination is consistent across segments out of time: Gini of 0.31 and 0.30 for 36-month and 60-month terms, and 0.29 to 0.34 across the four largest loan purposes (debt consolidation 0.31, credit card 0.34, home improvement 0.31, other 0.29), all close to the overall 0.32. There is no coded threshold for this test, so the rating is a judgement. **Conclusion: no segment is materially under-served by the model.**

### 4.6 Outcome definition: resolved
The earlier version of this review modelled finished loans only. Because a 60-month loan takes five years to finish, the 60-month loans that had finished by the snapshot were disproportionately those that defaulted early, which made loan term look like the strongest predictor (IV 0.238). On the fixed 24-month window its IV is 0.039. The earlier headline figures (Gini 0.356, P/O 0.77, PSI 0.013) were measured on that biased sample and are superseded by this report; `CHANGES.md` lists old and new values side by side. **Conclusion: the outcome definition is now consistent across vintages.**

---

## 5. Findings and recommendations

| # | Finding | Severity | Recommendation |
|---|---|---|---|
| 1 | Predicted PD under-states observed default on recent vintages (P/O 0.79) | High | Recalibrate the central tendency to current through-the-cycle conditions, or refit on recent vintages, before any PD-level use (pricing, ECL). Re-validate calibration after. |
| 2 | Score-to-default relationship has drifted while population is stable | Medium | Add a quarterly calibration check (predicted vs observed PD) to monitoring, not only a PSI check, so level drift is caught even when the population looks stable. |
| 3 | Challenger shows a small discrimination uplift (+0.040 Gini) | Low | Review non-linear effects (for example the FICO and affordability interaction) at the next redevelopment. Retain the scorecard in the interim for transparency. |
| 4 | Goodness-of-fit statistics are uninformative at this sample size | Low | Standardise on the predicted/observed ratio and the calibration plot for large books, with formal tests reserved for small samples. |

---

## 6. Conclusion

The scorecard is well built and a sound basis for ranking and approval decisions. The single material issue is calibration of the PD level on recent vintages, which is a recalibration matter rather than a design flaw, and which the discrimination and stability results show is isolated to the central tendency. Subject to recalibration and the addition of a calibration check to ongoing monitoring, the model is suitable for wider use.

---

## 7. Caveats

The data is US unsecured consumer lending used to demonstrate the methodology; the approach transfers directly to an auto or captive-finance book, though the characteristics and bins would be re-derived. The out-of-time vintages are more recent than the development window, which is the intended test and also the source of the calibration finding. Only approved loans are available, with no reject inference, and default is dated approximately (last payment plus four months) because the file has no delinquency history. The out-of-time period (2015-2016 issues) is a single benign US credit period. The simplified binning is quantile-based; a production build would use monotonic optimal binning, which would not change the conclusions.

*Reproduce all figures and metrics with `python build_dataset.py` then `python analysis.py` (see the README). Every number in this report is in `results.json`.*
