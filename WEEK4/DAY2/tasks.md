# Week 4, Day 2 — Tasks
## Hyperparameter Tuning on Home Credit Default Risk (6-Hour Session)

Read today's Concepts doc first. Continue the same project. Today you tune models on the **baseline feature pipeline** from Day 1; features get improved on Day 3, and a short re-tune follows on Day 4. The **locked test set stays untouched**. Use the same `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)` and the same primary metric (ROC-AUC) as yesterday so every number is comparable.

---

## Block 1 (Hour 1) — Reload and Reproduce
1. Reload the saved working set and development subsample (not the test set).
2. Rebuild the Day 1 simple baseline pipeline and re-score it with 5-fold CV. Confirm you reproduce yesterday's mean ± std within noise. If not, find out why before continuing.
3. Build a second pipeline using `HistGradientBoostingClassifier` as the model, with preprocessing suited to it (encode categoricals; imputation isn't required for the model itself). Score it with default settings as your **untuned** gradient boosting reference.
4. Log both untuned scores. These are the "before" numbers for today's tuning.

---

## Block 2 (Hour 2) — Plan the Searches
1. Using Day 1 timings, estimate the cost of each search: `candidates × folds × seconds per fit`.
2. Define a search space for each model with a one-line reason per range:
   - Logistic regression: `C` on a log scale, `class_weight` in `{None, "balanced"}`, and any penalty options you want to test.
   - Gradient boosting: `learning_rate` (log scale), `max_depth` or `max_leaf_nodes`, `min_samples_leaf`, `l2_regularization`, `max_iter`.
3. Decide the search type for each and justify it (small grid vs. random search) in your log.
4. Set a total compute budget for the day and stay within it. Note it in the log.

---

## Block 3 (Hour 3) — Grid Search on Logistic Regression
1. Run `GridSearchCV` on the development subsample with the parameter grid (use `model__C` style names) and `scoring="roc_auc"`.
2. Load `cv_results_` into a DataFrame, sort by rank, and inspect the top 10 rows with their means and stds.
3. Check whether the best value of `C` is at the edge of your grid. If yes, extend the grid and rerun.
4. Log the best parameters, the best CV score, the time taken, and the improvement over the untuned baseline, compared against the noise floor.

---

## Block 4 (Hour 4) — Random Search on Gradient Boosting
1. Run `RandomizedSearchCV` on the development subsample with your distributions (`loguniform` for learning rate and regularization, integer ranges for depth and leaf sizes), a fixed `random_state`, and an `n_iter` that fits the budget.
2. Inspect `cv_results_` the same way: top 10, means and stds, edge-of-range check.
3. Compare `mean_train_score` with `mean_test_score` for the top candidates. Flag any that look clearly overfit.
4. Log the best parameters, score, time, and improvement over the untuned gradient boosting reference.
5. Optional if time permits: a small random search for a random forest on the subsample.

---

## Block 5 (Hour 5) — Grid vs. Random at Equal Budget
1. For one model, run a grid search and a random search with roughly the **same number of total fits**. Logistic regression or a small tree-based model works well.
2. Plot best-score-so-far against the number of candidates evaluated for each method.
3. Write a short paragraph: which found a better setting, how close the runner-up candidates were, and what this says about when each method makes sense.
4. Apply the one-standard-error idea: among near-ties for the best score, which is the simplest or cheapest? Note which candidate you'd choose and why.

---

## Block 6 (Hour 6) — Confirm on More Data and Write Up
1. Take the chosen configuration for each model and score it with 5-fold CV on a **larger sample or the full working set**, timing permitting, to check the subsample result holds.
2. Build a summary table: model, untuned CV mean ± std, tuned CV mean ± std, improvement, search type, number of fits, and time.
3. Write a half-page summary covering what improved, by how much relative to the noise floor, which hyperparameters mattered most, and what surprised you.
4. Update the decision log with the search designs and your reasoning. Save the best pipelines' parameters to a file for Day 4.

---

## End-of-Day Checklist
- [ ] Reproduced the Day 1 baseline score and recorded an untuned gradient boosting reference
- [ ] Estimated search costs before running anything and set a compute budget
- [ ] Ran a grid search on logistic regression and inspected `cv_results_`
- [ ] Ran a random search on gradient boosting with sensible distributions
- [ ] Checked results for edge-of-range values and near-ties
- [ ] Compared grid vs. random search at equal budget
- [ ] Confirmed the tuned configurations on more data
- [ ] Built a results summary table and updated the decision log
- [ ] Locked test set still untouched