
**Key results** (train 2,640 / test 2,785 customers; base churn rate 50.3% / 38.2%)

- **ML vs one-line rule:** Logistic Regression reaches test PR-AUC **0.635** [0.602, 0.665] vs **0.527** [0.497, 0.557] for the recency rule. The intervals do not overlap, so the improvement is meaningful. Test ROC-AUC is 0.757.
- **Ensemble:** stacking changes CV PR-AUC by -0.002 vs the best single model, so it adds nothing meaningful and the simpler, interpretable model is preferred.
- **Stability:** across 5 backtest periods, ROC-AUC is 0.761 ± 0.021; PR-AUC mostly tracks the base rate (corr 0.97).
- **Business use:** the top 10% by risk has a churn rate of 71% (1.9x average) and reaches 19% of all churners; the top 30% reaches 50%.
- **ROI (illustrative assumptions: cost 5.0, margin 30%, uplift 10%):** targeting by risk score beats random contact by about 2,080 currency units at the same reach (top 45%). Absolute profit is assumption-driven and not a forecast.
- **Accuracy caveat:** at top 30% accuracy is 0.701 vs 0.618 for always predicting the majority class, so ranking metrics and recall are more informative.
- **Features:** 16 used; dropped for drift: tenure_days.
- **Main drivers:** frequency, recency_days, std_gap_days.
