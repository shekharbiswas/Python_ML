# Week 4 — Model Tuning + Project 1

## Week Goal
Week 3 produced a first honest baseline: a trained model, a proper train/test split, and the right metric for the problem type. This week asks the next question: **how do you make a model better on real industry data, and how do you know the improvement is real?** The week is one continuous project on a single large dataset, covering cross-validation, hyperparameter tuning, feature engineering and selection, and diagnosing overfitting and underfitting. It ends with a presentation at **3:30 pm on Day 5**.

The throughline is a disciplined improvement loop: measure reliably (cross-validation), improve systematically (tuning and better features), diagnose what's actually wrong (learning curves and train-vs-validation gaps), then report the result honestly. Day 5 is presentation prep only, so all modeling is finished by the end of Day 4.

**Key theme to keep in mind throughout the week:** *"A better score means nothing if the evaluation isn't trustworthy. Tuning, feature engineering, and preprocessing all have to happen inside the validation loop, never outside it."*

---

## Dataset Used
**One project, one dataset:** [Home Credit Default Risk](https://www.kaggle.com/competitions/home-credit-default-risk), using **`application_train.csv` only** — 307,511 loan applications, 122 columns, and a binary `TARGET` marking clients who had repayment difficulties. It is real consumer-finance data with mixed numeric and categorical columns, heavy missingness, and class imbalance, which makes it a strong fit for every topic this week. The primary metric is **ROC-AUC**, with PR-AUC as a secondary check.

---

## Week Structure

| Day | Focus | Real-World Question It Answers |
|-----|-------|----------------------------------|
| 1 | Cross-validation (k-fold, stratified k-fold) + project kickoff | "Why is a single train/test split not enough, and how do I get a score I can actually trust?" |
| 2 | Hyperparameter tuning — grid search vs. random search | "How do I find better model settings systematically, without tuning against the test set?" |
| 3 | Feature engineering & selection, categorical encoding, scaling — pandas vs. sklearn Pipeline comparison | "How should preprocessing be structured so it's reproducible and leak-free, and where does ad-hoc pandas stop being safe?" |
| 4 | Overfitting/underfitting diagnosis (learning curves, validation curves) + final model and one-time test evaluation | "How do I tell what's actually wrong with a model, what should I do about it, and how do I report the final result honestly?" |
| 5 | Presentation prep (presentation at 3:30 pm) | "Can I explain what I built, what I chose, and why, in a short, honest presentation?" |

---

## Approach This Week (Advanced Pace)
1. Minimal syntax hand-holding: a scikit-learn method or concept is introduced once, by use case, with no repeated re-explanation of Week 3 basics
2. Every day is applied to the **same project**, so each day's result becomes the next day's starting point
3. Every exercise is framed around a real modeling decision (which CV strategy, which search, which encoding, why), not an abstract "run this code" prompt
4. Leakage and evaluation mistakes get flagged explicitly: preprocessing fitted before the split, tuning against the test set, trusting a single lucky split
5. **Pandas vs. sklearn Pipeline tie-in (Day 3):** the same stateful preprocessing is done ad hoc in pandas and inside a `Pipeline`, and compared on leakage, reproducibility, and fit with cross-validation and search
6. The locked test set is created on Day 1 and used exactly once, on Day 4
7. A running **decision log** captures every choice and its evidence, so the Day 5 presentation is assembled, not reconstructed

---

## 📌 Project 1 (Week 4)
A focused project on the single Home Credit dataset.

**Requirements:**
1. Problem framed as a business decision, with a justified primary metric
2. A locked test set and a stratified development subsample, with a leakage audit
3. Features prepared with a `Pipeline` / `ColumnTransformer`
4. Baselines (dummy and simple) and models evaluated with cross-validation, reported as mean ± std
5. Hyperparameters tuned with grid or random search within a stated compute budget
6. Overfitting/underfitting diagnosed with learning and validation curves
7. Final model evaluated once on the locked test set, with ROC/PR curves, a threshold decision, and a lift table
8. A short written summary and a presentation covering the results, the modeling choices made, and why

---

## Deliverable by End of Week 4
1. A Python notebook that runs top to bottom from a clean kernel, with a `results/` folder of saved tables and figures
2. A decision log recording each choice, the alternatives, and the evidence
3. A short written results summary, including limitations
4. A presentation delivered at 3:30 pm on Day 5

This mirrors real modeling work: a model isn't finished when it runs, it's finished when its performance is measured reliably, improved deliberately, and explained honestly.

---
*See `Week4_Day1_Concepts.md` / `Week4_Day1_Tasks.md` through `Week4_Day5_Concepts.md` / `Week4_Day5_Tasks.md` for detailed daily breakdowns.*