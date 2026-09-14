# Week 3, Day 4 — Concepts
## Evaluation Metrics: Accuracy, Precision/Recall, RMSE, R²

---

## Question of the Day
**"How do I know if a model is actually good, and which metric tells the truth for this problem?"**

Days 2 and 3 both ended with a single score (R² for regression, accuracy for classification) treated as a first, honest read. Today is where those get properly unpacked — plus the metrics sitting right next to them that are just as important and easy to skip. The goal isn't memorizing formulas; it's building the judgment to pick the metric that actually answers the real-world question being asked, and to recognize when a metric everyone defaults to (like accuracy) is quietly lying.

---

## 1. Regression Metrics: RMSE and R²

**RMSE (Root Mean Squared Error)** measures the typical size of the model's prediction error, in the *same units as the target*:

$$\text{RMSE} = \sqrt{\frac{1}{n}\sum (y_i - \hat{y}_i)^2}$$

```python
from sklearn.metrics import mean_squared_error
import numpy as np

rmse = np.sqrt(mean_squared_error(y_test, y_pred))
```
An RMSE of 4,500 on the insurance dataset means "the model's predictions are typically off by about $4,500" — directly interpretable, which is exactly why it's often more useful to a non-technical reader than R². Squaring the errors before averaging means large errors are penalized disproportionately more than small ones — a model with a few wildly wrong predictions will show a worse RMSE than its "typical" error might suggest.

**R² (coefficient of determination)** measures the *proportion of variance in the target the model explains*, on a scale that tops out at 1:
- R² = 1 → the model perfectly explains the target's variation
- R² = 0 → the model does no better than always predicting the mean
- R² < 0 → the model is worse than just predicting the mean every time (possible, and a real warning sign)

```python
from sklearn.metrics import r2_score
r2 = r2_score(y_test, y_pred)
```
**RMSE vs. R², when to reach for which:** RMSE answers "how far off, in real units?" — the number a stakeholder can act on directly. R² answers "how much of the pattern did the model actually capture?" — more useful for comparing models against each other than for explaining real-world impact. Good practice: report both, since they answer different questions.

---

## 2. Classification Metrics: Beyond Accuracy
Accuracy is $\frac{TP + TN}{TP + TN + FP + FN}$ — the fraction of predictions that were correct overall. It's intuitive, and it's also the metric most likely to mislead on an imbalanced dataset, exactly as flagged on Day 3: a 90/10 split model can hit 90% accuracy by never predicting the minority class at all.

**Precision** — of everything the model predicted as positive, how much was actually positive:
$$\text{Precision} = \frac{TP}{TP + FP}$$
High precision means: when this model says "yes," it's usually right. Precision matters most when a **false positive is costly** (e.g., flagging a good transaction as fraud and blocking a real customer).

**Recall** — of everything that was actually positive, how much did the model catch:
$$\text{Recall} = \frac{TP}{TP + FN}$$
High recall means: this model rarely misses a real positive case. Recall matters most when a **false negative is costly** (e.g., a disease screening test that misses actual cases).

**Precision and recall trade off against each other** — moving the decision threshold from Day 3 lower (more things get classified "yes") typically raises recall and lowers precision, and vice versa. There's no universal "better" between them; the right balance depends entirely on which mistake is more expensive in the specific problem.

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, classification_report

accuracy_score(y_test, y_pred)
precision_score(y_test, y_pred)
recall_score(y_test, y_pred)
print(classification_report(y_test, y_pred))
```
`classification_report()` is the fastest way to get accuracy, precision, recall, and F1 (a combined precision/recall score, worth recognizing by name even without a deep dive today) all in one readable block — reach for it by default once a classifier is fit.

---

## 3. Matching the Metric to the Real-World Question
| Real-world question | Right metric |
|---|---|
| "On average, how far off in dollars will this prediction be?" | RMSE |
| "How much of the variation in price does this model actually explain?" | R² |
| "Overall, how often is this model right?" (only trustworthy on balanced classes) | Accuracy |
| "When this model raises an alarm, how often is it a real alarm?" | Precision |
| "Of all the real cases out there, how many does this model actually catch?" | Recall |

The Day 1 test ("could the target be averaged?") decided regression vs. classification. Today's test is one level down: within the right category, which specific metric matches the cost of being wrong in this particular problem? Getting this right is what separates a technically correct model evaluation from a *useful* one.

---

## 4. A Metric Is Only as Good as the Baseline It's Compared To
A score in isolation is hard to judge. Always compare against a trivial baseline:
- **Regression baseline:** a model that always predicts the mean of `y_train`. If your RMSE isn't meaningfully better than this, the model isn't adding value.
- **Classification baseline:** a model that always predicts the majority class. If your accuracy isn't meaningfully better than this — especially on an imbalanced dataset — the model isn't adding value either.

This baseline habit is what actually catches the "90% accuracy on a 90/10 split" trap from Day 3 in practice, not just in theory.

---

## What Success Looks Like Today
By the end of Day 4, you should be able to:
- Compute and correctly interpret RMSE and R² for a regression model
- Compute and correctly interpret accuracy, precision, and recall for a classification model
- Explain, with a concrete example, when precision matters more than recall and vice versa
- Read a `classification_report()` output without needing to look up what each column means
- Compare any model's score against a trivial baseline before calling it "good"