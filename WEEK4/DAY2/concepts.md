# Week 4, Day 2 — Concepts
## Hyperparameter Tuning: Grid Search vs. Random Search

---

## Question of the Day
**"How do I find better model settings systematically, without tuning against the test set?"**

Yesterday produced a trustworthy measurement: stratified CV on the working set, a locked test set, and baseline scores with a known noise floor. Today spends that measurement: search over model settings and keep what genuinely improves the cross-validated score. The discipline matters as much as the search. A tuned model is only impressive if the tuning process didn't quietly leak information from the data used to judge it.

---

## 1. Parameters vs. Hyperparameters
- **Parameters** are learned from data during `.fit()` (regression coefficients, tree split points).
- **Hyperparameters** are set *before* training and control how learning happens (regularization strength `C`, tree depth, learning rate, number of trees).

Defaults are reasonable guesses, not tuned values. On a dataset with real structure, moving away from the defaults can meaningfully change performance, but usually less than good features do (Day 3), which is why the search should be budgeted, not endless.

---

## 2. Grid Search
`GridSearchCV` tries every combination in a grid you specify, scoring each with cross-validation.
- **Strength:** exhaustive and easy to explain; good for one or two hyperparameters.
- **Weakness:** the number of combinations multiplies with each added hyperparameter. Five values for each of four hyperparameters is 625 candidates, and with 5-fold CV that's 3,125 fits.
- **Best used for:** small, low-dimensional spaces, such as `C` and `penalty` for logistic regression.

---

## 3. Random Search
`RandomizedSearchCV` samples a fixed number (`n_iter`) of combinations from distributions you define.
- **Strength:** the budget is set by `n_iter`, regardless of how many hyperparameters there are. It also tends to explore important hyperparameters more thoroughly, because every trial uses a fresh value for each one, whereas a grid repeats the same few values many times. This is why random search often matches or beats grid search at the same cost when only a few hyperparameters really matter.
- **Weakness:** less exhaustive, and results vary with the random seed (fix it).
- **Best used for:** larger spaces, such as gradient boosting or random forests with several settings.

**Practical pattern:** start with a broad random search to find the promising region, then, if worthwhile, run a small grid around it (coarse-to-fine).

---

## 4. Designing the Search Space
- **Use log scales for scale-like hyperparameters:** `C`, learning rate, and regularization strength span orders of magnitude, so sample them with `loguniform` rather than evenly.
- **Use integer ranges for counts and depths:** `max_depth`, `max_leaf_nodes`, `min_samples_leaf`.
- **Treat class weighting as a hyperparameter:** `class_weight` (e.g., `None` vs. `"balanced"`) can matter on an imbalanced target.
- **Reach pipeline parameters with double underscores:** `model__C` targets the `C` of the step named `model`, so the search cross-validates the *entire* pipeline and preprocessing stays inside each fold.

---

## 5. Budget First: Candidates × Folds × Time per Fit
Before launching a search, estimate its cost with yesterday's timings: `candidates × folds × seconds per fit`. If that's hours, shrink it:
- Search on the **development subsample** (~50k rows), then confirm the winner on the full working set.
- Use fewer folds (3) during exploration if the std allows it.
- Reduce `n_iter`, narrow ranges, or use `n_jobs=-1` for parallelism.
- Prefer models that scale well to 300k rows, such as histogram-based gradient boosting.

Tuning cost is a real engineering constraint, and describing your compute budget is part of the story on Day 5.

---

## 6. Reading `cv_results_` Instead of Only `best_params_`
The search returns a table of every candidate's mean and std. Look at it:
- Are the top candidates **statistically indistinguishable** (means within one std of each other)? If so, the "winner" may just be lucky.
- Prefer the **simpler or cheaper** configuration among near-ties (a common heuristic is the one-standard-error rule).
- Check whether the best values sit at the **edge** of your ranges. If so, the range was too narrow and should be extended.
- Compare `mean_train_score` against `mean_test_score` per candidate to spot overfitting configurations.

---

## 7. Selection Bias: The Best CV Score Is Optimistic
When you pick the best of many candidates, you pick partly on luck, so `best_score_` slightly overstates true performance. Two consequences:
- The honest estimate of the tuned model comes from **data the search never saw**, which is the locked test set, used once on Day 4.
- **Nested cross-validation** (an outer CV around the search) gives an unbiased estimate but is expensive, so it's usually skipped at this scale. Know that it exists and why.

Tuning against the test set, even "just to check," destroys its value.

---

## 8. Candidate Models for This Project
- **Logistic regression** (`C`, `penalty`, `class_weight`): fast, interpretable, a natural target for a small grid.
- **Random forest** (`n_estimators`, `max_depth`, `min_samples_leaf`, `max_features`): needs imputed inputs and gets slow on large data, so use the subsample.
- **Histogram gradient boosting** (`HistGradientBoostingClassifier`: `learning_rate`, `max_depth` or `max_leaf_nodes`, `l2_regularization`, `max_iter`): fast on 300k rows and handles missing values natively; the likely best performer.

Tune at least two of these with the search type that fits each.

---

## What Success Looks Like Today
By the end of Day 2, you should be able to:
- Explain the difference between parameters and hyperparameters
- Choose between grid and random search for a given space, and justify it
- Design a search space with sensible scales, including pipeline parameter names
- Estimate a search's cost before running it and keep it within budget
- Read `cv_results_` and judge whether a "best" candidate is truly better than the rest
- Explain why the best CV score is optimistic and why the locked test set stays untouched