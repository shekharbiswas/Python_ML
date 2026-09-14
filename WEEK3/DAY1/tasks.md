# Week 3, Day 1 — Tasks
## Supervised Learning Overview: Regression vs. Classification (6-Hour Session)

Read today's Concepts doc first. Today uses two small, simple Kaggle datasets side by side — one regression, one classification — so the distinction between them is seen directly in real data, not just described. No modeling yet; today is about correctly framing the problem, not solving it.

**Datasets for today (download from Kaggle, both are small and clean):**
- **Regression:** [Medical Cost Personal Datasets](https://www.kaggle.com/datasets/mirichoi0218/insurance) (`insurance.csv`) — predict `charges` (medical insurance cost) from age, sex, BMI, children, smoker, region.
- **Classification:** [Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/data) (`train.csv`) — predict `Survived` (0/1) from passenger features.

Both are small enough to load and eyeball in full within a minute, which is the point — today's effort goes into reasoning, not data wrangling.

---

## Block 1 (Hour 1) — Load and First Look
1. Load both CSVs into separate DataFrames: `df_reg` (insurance) and `df_clf` (titanic).
2. For each, run `.shape`, `.dtypes`, `.head()`, and `.describe()`.
3. Identify the target column in each (`charges` for insurance, `Survived` for titanic) and note its dtype.

---

## Block 2 (Hour 2) — Apply the Regression/Classification Test
For each dataset, apply the "could this target be sensibly averaged?" test from today's Concepts doc:
1. For `charges`: compute the mean, and write one sentence on why that mean is a meaningful number.
2. For `Survived`: compute the mean, and write one sentence on why averaging `0`s and `1`s here does *not* give you a meaningful "typical passenger" the way the insurance mean does — even though the arithmetic runs without error.
3. Explicitly label each dataset's problem type (regression / classification) and write one sentence justifying each label using the target column's nature, not just "because the instructions said so."

---

## Block 3 (Hour 3) — Explore Feature Types in Both Datasets
1. For each dataset, split columns into: numeric features, categorical features, and the target.
2. For the insurance dataset, check the cardinality (`.nunique()`) of `sex`, `smoker`, and `region`.
3. For the titanic dataset, check the cardinality of `Sex`, `Pclass`, and `Embarked`.
4. Note any columns that are clearly not useful as features (e.g., `PassengerId`, `Name`, `Ticket`, `Cabin` in titanic) and write one sentence on why each should likely be dropped or transformed before modeling later in the week.

---

## Block 4 (Hour 4) — Build X and y for Both Datasets
1. For the insurance dataset, construct `X_reg` (all feature columns) and `y_reg` (`charges`). Confirm shapes with `.shape` — `X_reg` should be 2D, `y_reg` should be 1D.
2. For the titanic dataset, construct `X_clf` (a reasonable subset of feature columns — leave out obvious non-features identified in Block 3) and `y_clf` (`Survived`). Confirm shapes the same way.
3. Deliberately try passing a single-bracket column (`df['charges']`) where a DataFrame is expected somewhere in your exploration, observe what happens, and write one sentence on what the shape mismatch actually looked like.

---

## Block 5 (Hour 5) — Missing Data and Quick Sanity Checks
1. Run `.isna().sum()` on both datasets and note which columns have missing values (titanic's `Age`, `Cabin`, and `Embarked` are the usual suspects — do not fix them today, just identify them).
2. For each dataset, write one sentence predicting what would go wrong if you tried to call `.fit()` on data with missing values as-is (this sets up Day 2, where it actually gets handled).
3. Check for obvious target leakage risk: for titanic, confirm no feature column is a disguised copy of `Survived` (e.g., a column literally derived from the outcome). Note there isn't one here, but write one sentence on why this check matters before modeling anything.

---

## Block 6 (Hour 6) — Write It Up
Produce a short written summary (half a page) covering:
1. Both datasets, their target columns, and your regression/classification labels with justification
2. The feature/target split you built for each, with shapes
3. At least one column from each dataset you'd drop or transform before modeling, and why
4. One open question or concern you have heading into Day 2 (e.g., "how exactly should the missing `Age` values in titanic be handled?")

This write-up is the seed of your Week 3 running notes — Day 2 builds directly on the `X`/`y` splits and missing-data observations from today.

---

## End-of-Day Checklist
- [ ] Loaded and inspected both the insurance (regression) and titanic (classification) datasets
- [ ] Correctly labeled each dataset's problem type with a justification based on the target column
- [ ] Identified categorical vs. numeric features and flagged columns to drop before modeling
- [ ] Built `X`/`y` splits for both datasets with correctly confirmed shapes
- [ ] Identified missing-data columns in both datasets without yet fixing them
- [ ] Wrote a short summary covering problem framing, feature/target splits, and one open question for Day 2