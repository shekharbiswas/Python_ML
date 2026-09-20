# Week 4, Day 1 — Concepts
## Cross-Validation and Project Kickoff: Getting a Score You Can Trust

---

## Question of the Day
**"Why is a single train/test split not enough, and how do I get a score I can actually trust?"**

Week 3 ended with a baseline on two small datasets. This week is one continuous project on one real industry dataset: **Home Credit Default Risk** (`application_train.csv`, 307,511 loan applications, 122 columns, a binary `TARGET` marking clients who had repayment difficulties). Every later day depends on today's foundation: if the evaluation setup is shaky, every "improvement" on Days 2–4 is meaningless. Today is about building a measurement you can trust.

**The week's arc:** Day 1 measure reliably → Day 2 tune hyperparameters → Day 3 engineer features → Day 4 diagnose over/underfitting and produce the final result → Day 5 presentation prep only (presentation at 3:30 pm). Since Day 5 has no modeling time, all numbers must be final by the end of Day 4.

---

## 1. Framing the Project Like Industry Work
A lender doesn't want "a model"; it wants a **decision**: approve, decline, or price a loan. Before any code, write down:
- **Who uses the prediction and what do they do with it?** (e.g., a credit analyst ranking applications by risk)
- **Which error is costlier?** Approving a client who defaults usually costs far more than declining a client who would have repaid.
- **What is the primary metric?** Roughly 1 in 12 applicants defaults, so the target is heavily imbalanced. **ROC-AUC** (ranking quality across all thresholds) is the primary metric, with **PR-AUC** as a secondary check. Accuracy is misleading here: predicting "repays" for everyone scores about 92% accuracy and is useless.

This framing becomes the opening slide on Day 5.

---

## 2. Why a Single Split Is Not Enough
`train_test_split` gives one score, and that score depends on which rows happened to land in the validation set. Run the same model with five different random seeds and you will get five different scores. If a later change improves the score by less than that spread, you can't tell progress from luck. A trustworthy evaluation needs to report **how much the score moves**, not only where it lands once.

---

## 3. K-Fold Cross-Validation
K-fold splits the working data into *k* parts. Each part serves as the validation set once while the model trains on the other *k − 1* parts, giving *k* scores.
- **Stratified k-fold** keeps the class ratio the same in every fold. It's required for imbalanced classification, otherwise some folds may have too few positives to score reliably.
- **Shuffle with a fixed seed** (`StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`) so runs are reproducible and every model is compared on the *same* folds.
- **Repeated stratified k-fold** (`RepeatedStratifiedKFold`) reruns the whole procedure with different shuffles for an even steadier estimate, at *n_repeats* times the cost.
- **`cross_validate` over `cross_val_score`** when you want more than one number: it can return train scores, fit times, and multiple metrics per fold.

---

## 4. Reading CV Results: Mean ± Standard Deviation
Always report **mean ± standard deviation** across folds.
- The mean is your best estimate of performance on new data.
- The standard deviation is your **noise floor**. If Day 3's feature engineering lifts the mean AUC by 0.002 and the fold-to-fold std is 0.004, that's not evidence of improvement.
- Also compare **train scores vs. validation scores**. A large gap is an early sign of overfitting, which Day 4 will diagnose properly.

---

## 5. Preprocessing Must Happen Inside the Folds
Imputers, scalers, and encoders learn statistics from data (medians, means, category lists). If they're fit on the full dataset *before* cross-validation, each validation fold has already influenced the preprocessing, so the score is inflated. That's **data leakage**. The fix is to put preprocessing and the model in one `Pipeline` and cross-validate the whole pipeline, so every fold re-fits preprocessing on its own training part only.

---

## 6. The Split Hierarchy: Locked Test Set + CV on the Working Set
Use two layers:
1. **A locked-away test set** (20%, stratified) is set aside today and untouched until Day 4. It answers "how will the finished model do on truly unseen data?" exactly once.
2. **The working set** (the other 80%) is where all experimentation happens, evaluated with cross-validation.

If you tune against the test set, even indirectly, its score becomes optimistic, which is why it stays locked.

---

## 7. Baselines and Leakage Checks
- A **dummy baseline** (predicts the class prior) is the floor. Its ROC-AUC will sit near 0.5, and its accuracy will look deceptively high, which makes the accuracy trap visible.
- A **simple pipeline baseline** (imputation, scaling, one-hot encoding, logistic regression) is the number Days 2–4 must beat.
- **Leakage audit:** identifiers like `SK_ID_CURR` carry no legitimate signal and should be dropped; check for any column that would not exist at application time or that duplicates the outcome.

---

## 8. Working at 300k Rows
- **Memory and timing:** check memory usage, downcast numeric types where safe, and time one full fit before planning experiments.
- **Iterate small, confirm big:** develop on a stratified **development subsample** (about 50k rows), then confirm important results on the full working set.
- **Cost arithmetic:** total model fits ≈ candidates × folds. This is why compute budgeting starts today and matters on Day 2.

---

## 9. Keep a Decision Log
Since Day 5 is presentation-only, the reasoning has to be captured as you go. Keep a running log with one line per decision: what you chose, the alternatives, and the evidence. Today's entries: framing, metric, split design, leakage checks, baseline results, noise-floor findings.

---

## What Success Looks Like Today
By the end of Day 1, you should be able to:
- Explain in plain language why a single split is unreliable and what cross-validation adds
- Justify ROC-AUC (with PR-AUC as a secondary metric) over accuracy for this dataset
- Describe the split design: locked test set plus stratified CV on the working set
- Explain why preprocessing must sit inside the pipeline being cross-validated
- Report baseline performance as mean ± std with a dummy baseline for context
- Show a decision log with today's choices and evidence