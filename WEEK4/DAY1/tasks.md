# Week 4, Day 1 — Tasks
## Cross-Validation and Project Kickoff on Home Credit Default Risk (6-Hour Session)

Read today's Concepts doc first. The whole week is **one project on one dataset**: [Home Credit Default Risk](https://www.kaggle.com/competitions/home-credit-default-risk). Download and use **`application_train.csv` only** (307,511 rows, 122 columns, target `TARGET`). Ignore the other tables. You may need to accept the competition rules on Kaggle once before the download works.

Today's goal: a framed problem, a locked-away test set, and baselines scored with cross-validation. No tuning or feature engineering yet.

---

## Block 1 (Hour 1) — Frame the Problem and Load the Data
1. Load `application_train.csv` into `df`. Run `.shape`, `.dtypes`, `.head()`.
2. Compute the class balance of `TARGET` (percentage of 0s and 1s).
3. Write a short problem statement (4–6 sentences): who uses the prediction, what decision it drives, which error is costlier, and what a useful model looks like.
4. Justify ROC-AUC as the primary metric (PR-AUC secondary) in one or two sentences, referring to the class balance.
5. Start your **decision log** (`decision_log.md` or a notebook markdown cell) with these first entries.

---

## Block 2 (Hour 2) — Scale and Quality Audit
1. Check memory usage (`df.memory_usage(deep=True).sum()` in MB), downcast numeric columns where safe, and re-check. Log before/after.
2. Compute the percentage of missing values per column and list the 15 worst.
3. Check for duplicate rows and duplicate `SK_ID_CURR` values.
4. Scan numeric columns for suspicious values. Look at `DAYS_EMPLOYED` in particular: a large positive value in a column that is otherwise negative (days before application) is commonly reported here, so confirm with `value_counts()`. Note anything similar in other columns.
5. List constant or near-constant columns and high-cardinality categorical columns.
6. Log each issue with a planned treatment. Don't permanently fix anything that depends on data statistics (imputation, capping); that belongs inside the pipeline.

---

## Block 3 (Hour 3) — Leakage Audit and Split Design
1. Review the columns and flag any that would not exist at application time or that identify rather than describe an applicant. Drop `SK_ID_CURR` from the features. Log a one-line verdict for anything else you flag.
2. Separate `X` and `y` and confirm shapes.
3. Create the **locked test set**: 80/20 `train_test_split` with `stratify=y` and a fixed `random_state`. Save the test set to disk (`X_test.parquet`, `y_test.parquet`) and do not load it again until Day 4.
4. Create a **development subsample** of about 50k rows, stratified, from the working set. Save it too.
5. Confirm the class balance is the same across train, test, and the subsample.

---

## Block 4 (Hour 4) — Baseline Pipelines With Cross-Validation
1. Build a **dummy baseline** (`DummyClassifier(strategy="prior")`) and score it with `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)` using `cross_validate` on the development subsample, with ROC-AUC, PR-AUC (`average_precision`), and accuracy.
2. Build a **simple baseline pipeline**: `ColumnTransformer` (median imputation + scaling for numeric columns, most-frequent imputation + one-hot encoding for categorical columns) followed by `LogisticRegression`.
3. Score it with the *same* splitter and metrics, with `return_train_score=True`.
4. Report **mean ± std** for each metric and for both baselines. Note the gap between train and validation scores.
5. Write one sentence comparing the dummy baseline's accuracy with its ROC-AUC.

---

## Block 5 (Hour 5) — See Why a Single Split Isn't Enough
1. Score the simple baseline with **10 different single stratified train/validation splits** (10 different seeds) on the development subsample. Record the ROC-AUC of each and note the min, max, and spread.
2. Compare that spread with the mean ± std from 5-fold CV. Write two sentences on what this shows about trusting a single split.
3. Run `RepeatedStratifiedKFold(n_splits=5, n_repeats=3, random_state=42)` and compare its mean and std with plain 5-fold.
4. Time one full `.fit()` of the simple pipeline on the development subsample and on the full working set. Log both and estimate the cost of a Day 2 search using *candidates × folds × time per fit*.
5. Plot the 10 single-split scores next to the CV mean ± std as a simple figure. Save it for the presentation.

---

## Block 6 (Hour 6) — Write-Up and Plan
Produce a one-page summary covering:
1. The business problem statement and chosen metrics
2. The split design (locked test set, working set, development subsample) and leakage findings
3. The top 3–5 data quality issues and planned treatments
4. Baseline results: dummy vs. simple pipeline, mean ± std, and the noise floor from Block 5
5. Timings and a compute plan for Day 2
6. Hypotheses for later days: which hyperparameters to tune, and two or three feature ideas

---

## End-of-Day Checklist
- [ ] Loaded `application_train.csv` and computed the class balance
- [ ] Wrote a problem statement and justified ROC-AUC (PR-AUC secondary)
- [ ] Audited memory, missing values, duplicates, and suspicious values, with each issue logged
- [ ] Dropped identifier and leaking columns
- [ ] Created and saved a stratified locked test set that has not been touched since
- [ ] Created and saved a stratified development subsample (about 50k rows)
- [ ] Scored dummy and simple baselines with stratified 5-fold CV, reported as mean ± std
- [ ] Demonstrated single-split noise vs. CV, and compared plain vs. repeated CV
- [ ] Timed fits and estimated search cost
- [ ] Wrote the one-page summary and updated the decision log