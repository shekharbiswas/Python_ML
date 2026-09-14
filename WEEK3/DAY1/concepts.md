# Week 3, Day 1 — Concepts
## Supervised Learning Overview: Regression vs. Classification

---

## Question of the Day
**"Given a problem, how do I tell whether it's a regression task or a classification task — and why does that decide everything downstream?"**

Today is the on-ramp for the whole week. Before touching a single model, the goal is to build a fast, reliable instinct: look at a dataset and a target column, and immediately know what kind of problem you're looking at. That one decision — regression or classification — determines which models are even valid candidates, which metrics mean anything, and how you'll interpret the output. Get this wrong on day one of a real project and everything built on top of it is wrong too.

---

## 1. What "Supervised" Actually Means
Supervised learning is learning from labeled examples: every row of training data has both **features** (the inputs) and a **target** (the known correct answer). The model's job is to learn the relationship between features and target well enough to predict the target on new, unseen rows.

This is different from unsupervised learning (no labels — finding structure on your own, e.g., clustering), which isn't part of this week. Everything from here through Friday assumes you already have the right answer for every training example, and the model's whole job is to approximate the function that produced it.

---

## 2. Regression vs. Classification — The Actual Distinction
The split comes down to one question: **what kind of thing is the target column?**

| | Regression | Classification |
|---|---|---|
| Target type | Continuous number | Discrete category/label |
| Example targets | Price, temperature, age, revenue | Spam/not spam, species, churn/no churn |
| Example question | "How much?" / "How many?" | "Which one?" / "Yes or no?" |
| Output of the model | A number (e.g., 42.7) | A class label (e.g., `"yes"`) or a probability per class |
| Wrong metric to use | Accuracy (meaningless — no discrete classes to be "correct" about) | RMSE / R² (meaningless — no numeric distance between classes) |

**The fast check:** if the target could sensibly be averaged (mean price, mean temperature), it's regression. If averaging it would be nonsense (average of "cat" and "dog"), it's classification. This single test resolves almost every ambiguous case.

**Binary vs. multiclass classification:** classification isn't always two options. Binary (2 classes — spam/not spam) and multiclass (3+ classes — species A/B/C) both count as classification and use mostly the same tools, with small adjustments to metrics later in the week.

---

## 3. Why the Distinction Decides Everything Downstream
This isn't an academic label — it's a fork in the road:
- **Model choice** — linear regression is a valid choice for a regression target and meaningless for a classification target. Logistic regression, despite the name, is a classification model, not a regression model.
- **Metric choice** — RMSE and R² only make sense when "how far off was the prediction" is a meaningful number. Precision/recall and accuracy only make sense when predictions are discrete categories.
- **Interpretation** — "the model is off by $2,300 on average" is a regression statement. "The model correctly identifies 91% of positive cases" is a classification statement. Mixing these up misleads whoever reads your results.

Misclassifying the problem type at the start is the single most common beginner mistake in applied ML — usually not because the concept is hard, but because it's easy to skip past without asking.

---

## 4. The scikit-learn Mental Model (Preview)
You won't train a model until Day 2, but it's worth previewing the shape of every model you'll touch this week, because it's identical regardless of problem type:

```python
model = SomeModel()       # instantiate
model.fit(X_train, y_train)     # learn from labeled examples
predictions = model.predict(X_test)   # predict on new data
model.score(X_test, y_test)     # a quick, built-in performance check
```

`fit` / `predict` / `score` is the same three-step shape for regression models, classification models, and nearly everything else in scikit-learn. Today, just recognize the pattern — Day 2 is where you'll actually run it.

---

## 5. Features (X) and Target (y) — Getting the Shape Right
Every supervised learning setup separates a dataset into:
- **`X`** — a 2D table of feature columns (even a single feature needs to stay 2D — scikit-learn expects it)
- **`y`** — a 1D column of target values, the thing being predicted

A common beginner error is passing a Series where a DataFrame is expected, or vice versa — scikit-learn will complain about shape mismatches, and reading that shape (`(n_samples,)` vs. `(n_samples, 1)`) is a skill worth building now, before it becomes a debugging distraction in Week 3, Day 2.

---

## What Success Looks Like Today
By the end of Day 1, you should be able to:
- Look at any dataset's target column and immediately say "regression" or "classification," with a reason
- Explain in plain language why a regression metric would be meaningless applied to a classification problem, and vice versa
- Recognize the `fit`/`predict`/`score` shape as the common thread across every scikit-learn model, even without having run it yet
- Correctly separate a dataset into `X` (features) and `y` (target) with the right shapes