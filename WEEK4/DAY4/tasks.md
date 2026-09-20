# Week 4, Day 4 — Tasks
## Diagnosis, Final Model, and Locked Test Set Evaluation on Home Credit Default Risk (6-Hour Session)

Read today's Concepts doc first. Today is the **last modeling day**: Day 5 is presentation prep only, so every number you report must exist by tonight. The first half diagnoses the model; the second half freezes it, evaluates it once on the locked test set, and saves every result you'll need for slides.

Create a `results/` folder now and save every table and figure there as you go.

---

## Block 1 (Hour 1) — Learning Curves
1. Load the final feature pipeline from Day 3 with your best Day 2 hyperparameters (gradient boosting, plus logistic regression for contrast).
2. Run `learning_curve` with 5 or 6 training sizes on the working set (or a large subsample) using stratified CV and ROC-AUC, with train scores included.
3. Plot training and validation curves (mean with std bands) for each model, and save the figures.
4. Write a diagnosis per model: underfitting, overfitting, or balanced? Is the validation curve still rising at the largest size? Log what the curve implies about getting more data.

---

## Block 2 (Hour 2) — Validation Curves
1. Pick one or two influential hyperparameters, such as gradient boosting `max_depth` / `max_leaf_nodes` or the number of iterations, and `C` for logistic regression.
2. Run `validation_curve` over a sensible range for each and plot training vs. validation scores.
3. Identify where the validation score peaks, where overfitting starts, and whether your Day 2 choice sat near that peak.
4. Write down the actions your diagnosis suggests (for example, more regularization or a shallower tree) and log them.

---

## Block 3 (Hour 3) — Short Re-Tune on the Final Features
1. Run one focused `RandomizedSearchCV` on the final feature pipeline for the gradient boosting model, using ranges informed by Blocks 1–2. Keep the compute within the budget you set on Day 2.
2. Inspect `cv_results_` (top candidates, means and stds, near-ties) and choose the final configuration using the same reasoning as Day 2.
3. **Freeze:** write down the final feature pipeline, model, and hyperparameters, and save the fitted configuration (parameters in a file). After this step nothing about the model changes.
4. Record the final CV mean ± std next to the baseline, Day 2 tuned, and Day 3 feature results in one table.

---

## Block 4 (Hour 4) — Final Fit and One-Time Test Evaluation
1. Refit the frozen pipeline on the **entire working set**.
2. Load the locked test set for the first time since Day 1.
3. Evaluate **once**: ROC-AUC, PR-AUC, and log-loss, plus accuracy for the accuracy-trap contrast.
4. Score the dummy baseline and simple baseline on the same test set for comparison.
5. Compare the test ROC-AUC with the final CV estimate. Log the gap. If it's large, investigate for leakage or a mismatch and record what you find, without adjusting the model to chase a better test number.
6. Save the fitted model (`joblib`) and the metrics table to `results/`.

---

## Block 5 (Hour 5) — Business-Oriented Results
1. Plot ROC and precision-recall curves for the dummy, simple baseline, and final model on the test set.
2. Choose an operating threshold using an explicit, stated cost ratio (for example, a missed defaulter costs a multiple of a wrongly declined client). Report precision, recall, and the confusion matrix at that threshold.
3. Build a **decile/lift table**: sort test applicants by predicted default probability, and report the default rate per decile and the lift over the overall rate. Plot it.
4. Compute permutation importance on held-out data and list the top 15 features, with a sentence on whether they make business sense.
5. Pull out 5–10 confident mistakes and note any pattern in them.
6. Save all tables and figures to `results/`.

---

## Block 6 (Hour 6) — Results Summary and Freeze
1. Write a one-to-two-page results summary covering: final metrics vs. baselines, the diagnosis and what it led to, the threshold decision and assumed costs, the lift table takeaway, top drivers, and limitations (only `application_train.csv` used, sensitive attributes and regulatory considerations, single dataset snapshot).
2. Assemble a clean **ablation and progress table** covering the whole week: Day 1 baseline → Day 2 tuning → Day 3 features → Day 4 final, each with CV mean ± std and the test result for the final model.
3. Finalize the decision log and confirm every figure needed for Day 5 is in `results/`.
4. Make sure the notebook runs top to bottom from a clean kernel so the results are reproducible. **No further modeling after today.**

---

## End-of-Day Checklist
- [ ] Learning curves plotted and diagnosed for the final model(s)
- [ ] Validation curves plotted for key hyperparameters and actions logged
- [ ] Short re-tune completed and the final configuration frozen
- [ ] Final model refit on the full working set
- [ ] Locked test set evaluated exactly once, alongside the baselines
- [ ] Test vs. CV gap checked and logged
- [ ] ROC/PR curves, threshold analysis, confusion matrix, and lift table produced
- [ ] Permutation importance and error analysis done
- [ ] Results summary, progress table, and decision log finished
- [ ] All tables and figures saved in `results/`; notebook reruns cleanly