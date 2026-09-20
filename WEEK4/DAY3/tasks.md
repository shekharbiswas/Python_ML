# Week 4, Day 3 — Tasks
## Feature Engineering and Pandas vs. Pipeline on Home Credit Default Risk (6-Hour Session)

Read today's Concepts doc first. Continue the same project. Use the same CV splitter and ROC-AUC as before, work on the development subsample for experiments, and keep the **locked test set untouched**. Every change is scored against the Day 1 baseline and the Day 2 tuned models, and recorded as an ablation.

---

## Block 1 (Hour 1) — Feature Inventory
1. Reload the working set and development subsample.
2. Build a table of all features: type, percent missing, number of unique values, and a note on whether it needs treatment.
3. Verify the `DAYS_EMPLOYED` sentinel again and check for similar anomalies in other columns (min/max/value counts).
4. Note which categorical columns are high-cardinality and which columns are constant or near-constant.
5. Write three or four feature hypotheses in your log before coding (for example, "affordability ratios should help because...").

---

## Block 2 (Hour 2) — Stateless Feature Engineering
1. Write functions (or a `FunctionTransformer`) for row-wise features:
   - Sentinel fix: replace the `DAYS_EMPLOYED` sentinel with missing and add an indicator flag
   - Age in years from `DAYS_BIRTH`, employment length in years
   - Ratios: credit-to-income, annuity-to-income, credit-to-goods-price, annuity-to-credit (loan term proxy)
   - Aggregates: mean/min of `EXT_SOURCE_*` columns, count of document flags
   - Optional: log transforms of skewed amounts
2. Add the new features to the baseline and Day 2 tuned pipelines. Score each with the shared CV and record mean ± std.
3. Run **ablations**: add feature groups one at a time and record the effect on ROC-AUC against the noise floor.

---

## Block 3 (Hour 3) — The Pandas vs. Pipeline Comparison
Do the same **stateful** preprocessing twice:
1. **Ad-hoc pandas way:** on the whole working-set DataFrame, before any CV, fit median imputation, standardization, and a target-mean or frequency encoding for a high-cardinality column such as `ORGANIZATION_TYPE`. Then run cross-validation on the transformed data.
2. **Pipeline way:** put the same steps inside a `Pipeline` / `ColumnTransformer` and cross-validate the whole thing.
3. Compare ROC-AUC (mean ± std) for both. Which is higher? The ad-hoc version usually looks better, which is exactly the leakage effect; the gap is largest for target encoding.
4. Test **reproducibility:** apply each approach to a small block of "new" rows (use a slice of the working set held out from fitting). Record how easy and how safe it is to reproduce the transformation with each approach.
5. Write a paragraph in your log: which transformations are safe in pandas, which must be in the Pipeline, and your evidence. This is a core presentation point.

---

## Block 4 (Hour 4) — Categorical Encoding and Scaling Experiments
1. Compare encodings for the categorical columns with logistic regression: one-hot vs. one-hot with grouped rare categories vs. frequency encoding. Record CV results and feature counts.
2. Compare encodings for the gradient boosting model: ordinal vs. one-hot.
3. If your scikit-learn version has `TargetEncoder`, try it inside the pipeline (it cross-fits internally) and compare it with the leaky pandas version from Block 3.
4. Test scaling: `StandardScaler` vs. `RobustScaler` vs. none for logistic regression, and none vs. scaled for gradient boosting. Write one sentence on what you found and why.

---

## Block 5 (Hour 5) — Feature Selection Inside the Pipeline
1. Apply a filter step (drop constant, near-constant, and extremely sparse columns; drop one of each highly correlated pair) inside the pipeline and check the CV effect.
2. Try L1-regularized logistic regression as an embedded selector and note how many features survive.
3. Compute permutation importance for the gradient boosting model on a validation fold, and list the top 15 features. Check whether your engineered features appear.
4. Try training on the top N features by importance for several values of N, computing selection inside the CV loop, and plot score against N.
5. Decide which features to keep and log the reasoning.

---

## Block 6 (Hour 6) — Assemble the Final Feature Pipeline and Write Up
1. Combine the changes that helped beyond the noise floor into a single final feature `Pipeline`.
2. Score it with 5-fold CV against three references: Day 1 baseline, Day 2 tuned logistic regression, and Day 2 tuned gradient boosting. Where time allows, confirm on more data than the development subsample.
3. Build the **ablation table**: change, CV mean ± std, effect vs. baseline.
4. Write a half-page summary: which features and encodings helped, which didn't, and the pandas vs. Pipeline finding.
5. Save the final feature pipeline code so Day 4 can re-tune on it.

---

## End-of-Day Checklist
- [ ] Built a feature inventory and verified the `DAYS_EMPLOYED` sentinel
- [ ] Engineered stateless features and tested them as ablations against the noise floor
- [ ] Ran the pandas vs. Pipeline comparison and documented the leakage gap
- [ ] Compared categorical encodings and scaling choices
- [ ] Applied feature selection inside the CV loop
- [ ] Assembled and scored the final feature pipeline against earlier baselines
- [ ] Built the ablation table and updated the decision log
- [ ] Locked test set still untouched