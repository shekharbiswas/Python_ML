# Week 3 — Machine Learning Fundamentals

## Week Goal
Weeks 1 and 2 built the Python and pandas muscle needed to load, clean, and reshape real data. This week shifts from *preparing* data to *learning from* it: **scikit-learn** becomes the primary tool, and the goal is to go from a clean DataFrame to a trained, evaluated model with a clear understanding of what's happening at each step — not just which method to call.

The throughline for the week is the standard supervised learning workflow: pick a problem type (regression or classification), split the data honestly, fit a model, and evaluate it with the right metric for the job. By Friday, the workflow from raw features to a scored model should feel automatic, and the difference between a regression problem and a classification problem should be obvious on sight.

**Key theme to keep in mind throughout the week:** *"A model is only as trustworthy as the evaluation behind it. This week is about building both — the model and the habit of checking it properly."*

This week moves at a working-engineer pace: correct use of `train_test_split`, matching the evaluation metric to the problem type, and reading what a metric actually implies about model behavior — not just running `.fit()` and calling it done. Expect common traps (evaluating on training data, using accuracy on imbalanced classes, misreading R²) to be called out as they come up.

---

## Dataset Used
Two small, clean Kaggle datasets, used side by side all week so the regression/classification distinction is seen in real data rather than described abstractly:
- **Regression:** [Medical Cost Personal Datasets](https://www.kaggle.com/datasets/mirichoi0218/insurance) (`insurance.csv`) — predicting medical charges.
- **Classification:** [Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/data) (`train.csv`) — predicting survival.

Both are small enough to load and inspect in full within minutes, keeping the week's effort on the modeling workflow itself rather than on data wrangling.

---

## Week Structure

| Day | Focus | Real-World Question It Answers |
|-----|-------|----------------------------------|
| 1 | Supervised learning overview, regression vs. classification | "Given a problem, how do I tell whether it's a regression task or a classification task — and why does that decide everything downstream?" |
| 2 | Linear regression, train/test split | "How do I fit a regression model properly, and why can't I trust a score computed on the data it trained on?" |
| 3 | Logistic regression, classification basics | "How does a classifier actually make a decision, and how is that different from predicting a number?" |
| 4 | Evaluation metrics — accuracy, precision/recall, RMSE, R² | "How do I know if a model is actually good, and which metric tells the truth for this problem?" |
| 5 | Wrap-up & presentation of learning outcomes (lightweight) | "Can I clearly explain both models built this week, their results, and the single most important habit I'm taking forward?" |

---

## Approach This Week (Advanced Pace)
1. Minimal syntax hand-holding — a scikit-learn method or concept is introduced once, by use case, with the expectation it sticks; no repeated re-explanation of basics already covered in Weeks 1–2
2. Every exercise is framed around a real modeling decision (which model, which metric, why), not an abstract "run this code" prompt
3. Correct vs. incorrect evaluation practice gets flagged explicitly (train/test leakage, wrong metric for the problem) — the goal is a trustworthy model, not just a model that runs
4. One core new capability per day, but moving through it fast; remaining time goes to interpretation — reading what a coefficient, confusion matrix, or R² actually means — rather than more code for its own sake
5. Regression and classification are treated as genuinely different problems throughout, not variations of the same recipe
6. The week ends with a short, lightweight wrap-up — consolidating and presenting the week's two models rather than a heavy new build

---

## Deliverable by End of Week 3
A Python script or notebook that:
1. Loads a dataset and identifies whether the problem is regression or classification
2. Performs a proper train/test split
3. Trains a baseline model appropriate to the problem type (linear or logistic regression)
4. Evaluates the model with the correct metric(s) for that problem type
5. Includes a short written interpretation of the results — what the metric says about the model's performance and where it likely falls short

This mirrors the actual first step of any real modeling work: before reaching for anything more advanced, know how to build and honestly evaluate the simplest model that fits the problem.

---
*See `Week3_Day1_Concepts.md` / `Week3_Day1_Tasks.md` through `Week3_Day5_Concepts.md` / `Week3_Day5_Tasks.md` for detailed daily breakdowns.*