# Week 3, Day 3 — Concepts
## Logistic Regression & Classification Basics

---

## Question of the Day
**"How does a classifier actually make a decision, and how is that different from predicting a number?"**

Day 2 built the full regression workflow end to end. Today swaps the target from a number to a category and asks what actually changes. The mechanics (`fit`/`predict`/`score`, train/test split) carry over almost unchanged — what's genuinely new is what the model is predicting, how it gets from a raw number to a decision, and why "logistic regression" is a classification model despite the name.

---

## 1. Logistic Regression Is Classification, Not Regression
The name is the single most common source of confusion in this whole topic. Logistic regression does **not** predict a continuous number — it predicts the **probability** that a row belongs to a given class, then converts that probability into a class label. It shares its name and some math with linear regression, but it belongs firmly in the classification family and gets evaluated with classification metrics, not R² or RMSE.

---

## 2. From a Number to a Decision — What's Actually Under the Hood
Linear regression outputs $w_1x_1 + w_2x_2 + \dots + b$ directly as the prediction. Logistic regression takes that same kind of linear combination and passes it through the **sigmoid function**, which squashes any real number into a range between 0 and 1:

$$P(y=1) = \frac{1}{1 + e^{-(w_1x_1 + w_2x_2 + \dots + b)}}$$

That output is a probability, not a label. A **threshold** — 0.5 by default — turns it into a decision: probability ≥ 0.5 → predict class 1, otherwise → predict class 0. This threshold is a choice, not a law of nature — it can be moved, and where it should sit depends on the cost of different kinds of mistakes (more on this Thursday, with precision/recall).

---

## 3. `predict()` vs. `predict_proba()`
scikit-learn's classifiers expose both:
```python
model.predict(X_test)          # class labels, e.g., array([0, 1, 1, 0])
model.predict_proba(X_test)    # probability of each class, e.g., array([[0.82, 0.18], [0.13, 0.87], ...])
```
`predict_proba()` returns one probability column per class — for binary classification, the second column is the probability of class 1, and `predict()` is effectively `predict_proba()` thresholded at 0.5. Reaching for `predict_proba()` matters whenever the confidence behind a prediction is as important as the prediction itself (e.g., "87% likely to survive" is a very different statement than "51% likely to survive," even though both round to the same predicted label).

---

## 4. The Confusion Matrix — The Foundation Everything Else Builds On
Before naming any metric, every classification result can be broken into four counts:

| | Predicted: No | Predicted: Yes |
|---|---|---|
| **Actual: No** | True Negative (TN) | False Positive (FP) |
| **Actual: Yes** | False Negative (FN) | True Positive (TP) |

```python
from sklearn.metrics import confusion_matrix
confusion_matrix(y_test, y_pred)
```
Every classification metric that comes up Thursday (accuracy, precision, recall) is just a different arithmetic combination of these four numbers. Getting comfortable reading a confusion matrix today makes tomorrow's metrics feel like arithmetic, not new concepts.

A **false positive** and a **false negative** are not the same kind of mistake, and which one matters more depends entirely on the problem (a false negative on a disease screening test is a very different cost than a false positive on a spam filter) — a theme that gets fully unpacked tomorrow.

---

## 5. Class Balance — Look Before You Model
Before fitting anything, check how the target classes are distributed:
```python
y.value_counts(normalize=True)
```
A dataset that's 90% one class and 10% the other is **imbalanced**. A model that just predicts the majority class every time would score 90% "accuracy" while being completely useless — this is the exact trap tomorrow's metrics section exists to prevent, but the check itself belongs today, before a model is even fit, because it changes how you should read every result that follows.

---

## 6. Fit/Predict/Score for a Classifier
```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression(max_iter=1000)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
model.score(X_test, y_test)   # accuracy by default for a classifier
```
`max_iter=1000` is a practical note, not a concept to dwell on: logistic regression is fit iteratively, and the scikit-learn default sometimes isn't enough iterations to converge on real data, throwing a convergence warning. Raising it is the standard fix. `.score()` on a classifier defaults to **accuracy** — correct today as a first read, with the caveat from Section 5 already in mind, and with tomorrow's content on when accuracy misleads you still to come.

---

## What Success Looks Like Today
By the end of Day 3, you should be able to:
- Explain why logistic regression is a classification model despite its name
- Describe what the sigmoid function does and why a threshold is needed to get from probability to label
- Use both `.predict()` and `.predict_proba()` and explain when each is the right tool
- Read a confusion matrix and correctly identify TP, TN, FP, and FN
- Check class balance before modeling and explain why an imbalanced target makes accuracy alone unreliable