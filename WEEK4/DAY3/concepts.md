# Week 4, Day 3 — Concepts
## Feature Engineering and Selection, Categorical Encoding, Scaling — Pandas vs. sklearn Pipeline

---

## Question of the Day
**"How should preprocessing be structured so it's reproducible and leak-free, and where does ad-hoc pandas stop being safe?"**

Tuning (Day 2) improves how a model learns from the features it's given. Feature engineering improves the features themselves, and on tabular data like loan applications it is often where the bigger gains come from. Today also builds intuition for a practical question that every real pipeline faces: which transformations are safe to do with plain pandas, and which must live inside a scikit-learn `Pipeline`.

---

## 1. Missing Values Are Information, Not Just a Nuisance
Home Credit has many columns with heavy missingness. Options:
- **Impute** with a simple statistic (median for numeric, most frequent for categorical). Fast and usually adequate.
- **Add missing-value indicators** (`SimpleImputer(add_indicator=True)` or a `MissingIndicator`), because *whether* a value is missing can itself predict the outcome.
- **Let the model handle it:** histogram gradient boosting supports missing values natively, while linear models and most other estimators need imputation.

**Sentinel values** are placeholders masquerading as data. `DAYS_EMPLOYED` contains a very large positive value in a column that is otherwise negative. Left alone, it distorts scaling and any linear fit. The fix is to convert it to missing, add an indicator flag, and let imputation handle the rest.

---

## 2. Creating New Features From Domain Logic
Good features encode how the business thinks:
- **Ratios:** credit amount relative to income, loan annuity relative to income, credit relative to goods price. Affordability matters more than absolute amounts.
- **Rescaled units:** `DAYS_BIRTH` is negative days, so convert to age in years; similarly for employment length.
- **Aggregates across related columns:** the mean or min of the `EXT_SOURCE_*` scores, or a count of flagged documents.
- **Handle skew:** log-transform heavy-tailed amounts so linear models aren't dominated by outliers.

Guardrails: every new feature must be computable at application time, and each one is a hypothesis to test with CV, not an assumption.

---

## 3. Encoding Categorical Variables
| Encoding | Good for | Watch out for |
|---|---|---|
| One-hot | Low-cardinality categories, linear models | Column explosion on high-cardinality features |
| Ordinal | Tree-based models, or categories with true order | Implies a fake order for linear models |
| Frequency / count | High-cardinality features with a compact representation | Loses category identity |
| Target encoding | High-cardinality features with strong signal | **Leaks the target if fit naively**; must be cross-fitted inside CV |

Handle rare categories explicitly (group infrequent levels) and unseen categories at prediction time (`handle_unknown`). `ORGANIZATION_TYPE` in this dataset is a good test case.

---

## 4. Scaling: When It Matters and When It Doesn't
- **Matters** for regularized linear models, distance-based methods, and anything that uses gradient-based optimization sensitive to feature scale.
- **Doesn't matter** for tree-based models, which split on thresholds and are invariant to monotonic rescaling.
- With outliers, `RobustScaler` (median/IQR) is safer than `StandardScaler`.

Scaling is stateful, since it learns means and spreads from data, so it belongs inside the pipeline.

---

## 5. Feature Selection
More features isn't always better; irrelevant ones add noise, cost, and overfitting risk. Approaches, from cheapest to costliest:
- **Filter methods:** drop constant or near-constant columns, columns with extreme missingness, and one of each highly correlated pair.
- **Embedded methods:** L1-regularized logistic regression zeroes out weak features; tree-based importances rank them.
- **Permutation importance:** measures how much the validation score drops when a feature is shuffled, which is model-agnostic and less biased than built-in importances.
- **Wrapper methods** (e.g., recursive feature elimination): powerful but expensive at 300k rows.

**Selection leaks too.** If you pick features using the whole working set and then cross-validate, the validation folds have influenced which features survived. Do selection inside the pipeline or inside each fold.

---

## 6. Pandas vs. sklearn Pipeline: Where Each Belongs
This is today's central comparison.

**Stateless, row-wise transformations** (ratios, unit conversions, sentinel replacement, log transforms) compute each row from that row alone. They give the same result whether applied before or after splitting, so plain pandas, or a `FunctionTransformer` in the pipeline, is safe.

**Stateful transformations** (imputation, scaling, target/frequency encoding, feature selection) learn something from the data. Done in pandas on the whole DataFrame *before* splitting or CV, they leak information from validation rows into training. Done inside a `Pipeline`, they are refit on each fold's training data only.

| | Ad-hoc pandas | sklearn Pipeline |
|---|---|---|
| Leakage risk (stateful steps) | High if applied before the split | None, since it fits on training folds only |
| Reproducibility on new data | Easy to forget a step or use different statistics | One object, `.fit` then `.predict`, same steps every time |
| Works with CV and search | Awkward, requires manual loops | Native: `cross_validate`, `GridSearchCV`, `RandomizedSearchCV` |
| Speed of experimentation | Fast for one-off exploration | Slightly more setup, much safer to iterate |

**Rule of thumb:** explore in pandas, but ship stateless logic as functions and all stateful steps in the Pipeline.

---

## 7. Test Every Change With the Same CV
Change one thing at a time, score with the identical CV splits, and compare the improvement with the fold-to-fold std. Keep changes that help beyond the noise floor; drop those that don't. Record each as an **ablation** (what was added, effect on score) in the decision log. That ablation table is strong presentation material.

---

## What Success Looks Like Today
By the end of Day 3, you should be able to:
- Handle missing values and sentinel values deliberately, including missing indicators
- Engineer domain-driven features and explain the reasoning behind each
- Choose an encoding for each categorical column and defend the choice, including why naive target encoding leaks
- Say when scaling matters and when it doesn't
- Apply feature selection without leaking information
- Explain, with evidence from your own experiment, which transformations are safe in pandas and which must live in the Pipeline