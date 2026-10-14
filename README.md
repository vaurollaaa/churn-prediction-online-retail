# Customer Churn Prediction for a Loyalty / Retention Team

Rank recently active customers by their risk of not buying again, so a retention team with limited capacity can decide whom to contact first.

**Dataset:** UCI Online Retail II (UK gift retailer, Dec 2009 – Dec 2011, ~1M transaction lines)
**Stack:** Python, pandas, scikit-learn, XGBoost, LightGBM, SHAP

## TL;DR
- **Problem:** which customers active in the last 180 days will make no purchase in the next 90 days?
- **Method:** leakage-safe features, out-of-time test, 5 models vs a one-line recency rule, PSI drift check, 5-period temporal backtest.
- **Result:** Logistic Regression, test ROC-AUC 0.76 (stable across 5 periods, ±0.02). The top 10% by risk has 1.9x the average churn rate and reaches 19% of all churners; the top 30% reaches 50%.
- **Honest findings:** stacking adds nothing over the simpler model; performance is weaker for new customers (ROC-AUC 0.64 vs 0.78 for returning).
- **Output:** a ranked risk score, High/Medium/Low tiers, and a campaign ROI simulation with explicit assumptions.

> The model is a **prioritisation tool**, not an exact churn predictor. Scores are for ranking, not probabilities.

## Key figures
| Cumulative gains | Drivers (SHAP) |
|---|---|
| ![gains](output/figures/cumulative_gains.png) | ![shap](output/figures/shap_summary.png) |

| Model comparison | ROI simulation |
|---|---|
| ![models](output/figures/model_comparison.png) | ![roi](output/figures/roi_simulation.png) |

## Approach
1. **Business framing:** churn defined from behaviour (no purchase in 90 days after a cutoff).
2. **Cleaning with an audit log:** duplicates, missing IDs, non-product codes, invalid prices/quantities.
3. **Features:** RFM, purchase rhythm (gaps), momentum, basket, returns, all computed only from data before the cutoff.
4. **Validation:** out-of-time test, 5-fold CV on train, bootstrap CIs, temporal backtest over 5 periods.
5. **Statistics:** Mann–Whitney U with Holm correction and effect sizes; PSI for drift.
6. **Interpretation:** logistic coefficients and SHAP.
7. **Business output:** risk tiers, value prioritisation index, ROI simulation.

## Results
| Model | CV PR-AUC | Test PR-AUC | Test ROC-AUC | Lift@10% |
|---|---|---|---|---|
| Rule: recency only | 0.631 | 0.527 | 0.656 | 1.58 |
| Logistic Regression | 0.756 | 0.635 | 0.757 | 1.85 |
| Random Forest | 0.750 | 0.632 | 0.763 | 1.94 |
| XGBoost | 0.742 | 0.642 | 0.759 | 2.01 |
| LightGBM | 0.733 | 0.640 | 0.754 | 1.99 |
| Stacking | 0.754 | 0.639 | 0.764 | 1.93 |

Logistic Regression was selected as the simplest model within 0.005 CV PR-AUC of the best (chosen on CV only, not on test).

**Campaign simulation** (illustrative: cost 5, margin 30%, uplift 10%): contacting everyone loses money, while contacting the highest-risk customers first beats random targeting. Absolute profit depends entirely on these assumptions; a real A/B test is needed to measure true uplift.

## Limitations
- Moderate predictive power (lift ≈ 1.9x in the top decile): useful for prioritising, not for certain predictions.
- Weaker for new customers with short history.
- Scores are not calibrated probabilities (class weights and seasonal base-rate shift).
- Associations, not causal effects; ROI uses assumed parameters.
- One retailer, two years of data.

## Reproduce
```bash
git clone https://github.com/<username>/churn-prediction-online-retail.git
cd churn-prediction-online-retail
pip install -r requirements.txt
# download the dataset into data/ (see data/README.md)
jupyter notebook notebooks/churn_prediction.ipynb
```

## Repo structure
`notebooks/` analysis · `output/` figures and result tables · `data/` dataset instructions

## Author
<Nama kamu> · <LinkedIn> · <Email>
