# Week 3, Day 4 — Tasks
## Evaluation Metrics: Accuracy, Precision/Recall, RMSE, R² (6-Hour Session)

Read today's Concepts doc first. Today revisits **both** models built this week — Day 2's linear regression on `insurance.csv` and Day 3's logistic regression on `train.csv` (Titanic) — and puts them under a proper metrics microscope. Reuse your existing code from those days rather than rebuilding from scratch. Each task includes a hint — try it yourself first before opening the hint.

---

## Block 1 (Hour 1) — Rebuild Both Models
1. Re-run your Day 2 pipeline end-to-end (load insurance data → encode → split → fit `LinearRegression`) to get `y_test`, `y_pred` for regression.
2. Re-run your Day 3 pipeline end-to-end (load Titanic data → clean → encode → split → fit `LogisticRegression`) to get `y_test`, `y_pred` for classification.
3. Confirm both run cleanly before moving on — today's focus is entirely on evaluating them, not on debugging the models themselves.

<details>
<summary>Hint</summary>

If either pipeline throws an error, it's almost always a leftover from Day 1/2/3 — check that you're loading the raw CSV again (not a modified copy) and re-applying the same cleaning/encoding steps in the same order.
</details>

---

## Block 2 (Hour 2) — Regression Metrics on the Insurance Model
1. Compute RMSE and R² on the test set for your linear regression model.
2. Compute a trivial baseline: predict the mean of `y_train` for every test row, and compute that baseline's RMSE too.
3. Write one sentence comparing your model's RMSE to the baseline's — how much better is it, in dollar terms?

<details>
<summary>Hint</summary>

```python
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

rmse = np.sqrt(mean_squared_error(y_test, y_pred))
r2 = r2_score(y_test, y_pred)
print("RMSE:", round(rmse, 2))
print("R²:", round(r2, 3))

baseline_pred = np.full_like(y_test, fill_value=y_train.mean(), dtype=float)
baseline_rmse = np.sqrt(mean_squared_error(y_test, baseline_pred))
print("Baseline RMSE:", round(baseline_rmse, 2))
```
</details>

---

## Block 3 (Hour 3) — Classification Metrics on the Titanic Model
1. Compute accuracy, precision, and recall on the test set for your logistic regression model.
2. Print the full `classification_report()`.
3. Compute a trivial baseline: predict the majority class for every test row, and compute that baseline's accuracy.

<details>
<summary>Hint</summary>

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, classification_report

print("Accuracy:", round(accuracy_score(y_test, y_pred), 3))
print("Precision:", round(precision_score(y_test, y_pred), 3))
print("Recall:", round(recall_score(y_test, y_pred), 3))
print(classification_report(y_test, y_pred))

majority_class = y_train.mode()[0]
baseline_pred = [majority_class] * len(y_test)
print("Baseline accuracy:", round(accuracy_score(y_test, baseline_pred), 3))
```
</details>

---

## Block 4 (Hour 4) — Precision vs. Recall, Made Concrete
1. Look at your Titanic model's precision and recall from Block 3 — are they close together, or is one noticeably higher?
2. Pick one: imagine you're optimizing this model for a scenario where missing a survivor (false negative) is the worse mistake. Would you want to raise or lower the decision threshold from Day 3 to favor recall over precision?
3. Test your answer: use `predict_proba()` to generate predictions at a lower threshold (e.g., 0.35 instead of 0.5), recompute precision and recall, and confirm whether they moved the direction you expected.

<details>
<summary>Hint</summary>

```python
y_proba = model.predict_proba(X_test)[:, 1]
y_pred_lower_threshold = (y_proba >= 0.35).astype(int)

print("Precision at 0.35:", round(precision_score(y_test, y_pred_lower_threshold), 3))
print("Recall at 0.35:", round(recall_score(y_test, y_pred_lower_threshold), 3))
```

Lowering the threshold should raise recall (catching more actual positives) at some cost to precision (more false alarms) — if your numbers moved the other way, double check which class `predict_proba()`'s second column corresponds to.
</details>

---

## Block 5 (Hour 5) — Match the Metric to the Question
For each of the following real-world questions, write down which single metric (from today's Concepts doc table) answers it best, and why the others on today's list wouldn't:
1. "On average, how many dollars off will a cost prediction be?"
2. "Of the passengers this model flags as survivors, how often is it actually right?"
3. "Of all the passengers who actually survived, how many did this model correctly catch?"
4. "How much of the variation in medical costs does this model explain overall?"

<details>
<summary>Hint</summary>

No code needed — if you're stuck, match each question's wording directly against the table in today's Concepts doc: "how many dollars off" is a unit-based question (RMSE), "how often right when it says yes" is about false positives (precision), "how many actual cases caught" is about false negatives (recall), "how much variation explained" is R².
</details>

---

## Block 6 (Hour 6) — Write It Up
Produce a short written summary (half a page) covering:
1. Your regression model's RMSE and R², compared against the baseline from Block 2
2. Your classification model's accuracy, precision, and recall, compared against the baseline from Block 3
3. Your threshold experiment from Block 4 and what it showed about the precision/recall trade-off
4. One open question heading into Day 5 (e.g., "which of my two models from this week would I actually be comfortable showing to someone else, and why?")

<details>
<summary>Hint</summary>

No code needed here — this is a reasoning check. If you're stuck on point 4, think about which model's baseline comparison from today felt more convincing.
</details>

---

## End-of-Day Checklist
- [ ] Computed RMSE and R² for the regression model and compared against a mean baseline
- [ ] Computed accuracy, precision, and recall for the classification model and compared against a majority-class baseline
- [ ] Explained precision vs. recall using a concrete cost-of-error example
- [ ] Tested how moving the decision threshold shifts precision and recall
- [ ] Matched each of four real-world questions to the correct metric
- [ ] Wrote a short summary covering both models' metrics, the baseline comparisons, and the threshold experiment