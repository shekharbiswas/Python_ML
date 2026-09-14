# Week 3, Day 3 — Tasks
## Logistic Regression & Classification Basics (6-Hour Session)

Read today's Concepts doc first. Today uses the same **Titanic (`train.csv`)** dataset from Day 1 — you already identified it as a classification problem and flagged which columns to drop. Each task includes a hint — try it yourself first before opening the hint.

---

## Block 1 (Hour 1) — Prep the Data for Modeling
1. Load `train.csv` and rebuild `X` and `y` (`Survived`), dropping the non-feature columns you flagged on Day 1 (`PassengerId`, `Name`, `Ticket`, `Cabin`).
2. Handle the missing `Age` values simply for today — fill with the median. Handle missing `Embarked` by filling with the most frequent value.
3. One-hot encode `Sex` and `Embarked` with `drop_first=True`.

<details>
<summary>Hint</summary>

```python
import pandas as pd

df = pd.read_csv("train.csv")
df = df.drop(columns=["PassengerId", "Name", "Ticket", "Cabin"])

df["Age"] = df["Age"].fillna(df["Age"].median())
df["Embarked"] = df["Embarked"].fillna(df["Embarked"].mode()[0])

X = df.drop(columns=["Survived"])
y = df["Survived"]
X = pd.get_dummies(X, columns=["Sex", "Embarked"], drop_first=True)
print(X.isna().sum())   # should be all zeros now
```
</details>

---

## Block 2 (Hour 2) — Check Class Balance, Then Split
1. Before doing anything else, check the class balance of `y` with `.value_counts(normalize=True)`.
2. Write one sentence: is this dataset meaningfully imbalanced, and what would "always predict the majority class" score as accuracy here?
3. Split `X` and `y` into train/test sets (80/20, fixed `random_state`).

<details>
<summary>Hint</summary>

```python
print(y.value_counts(normalize=True))

from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)
```

`stratify=y` keeps the class balance roughly the same in both the train and test splits — worth noticing here even though it isn't explained in depth until later in the module.
</details>

---

## Block 3 (Hour 3) — Fit a Logistic Regression Model
1. Instantiate and fit a `LogisticRegression` model on the training data.
2. Predict class labels on `X_test` with `.predict()`.
3. Predict probabilities on `X_test` with `.predict_proba()`, and print the probability pair for the first 5 test rows alongside their predicted label.

<details>
<summary>Hint</summary>

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression(max_iter=1000)
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
y_proba = model.predict_proba(X_test)

for i in range(5):
    print(f"Predicted: {y_pred[i]}, Probabilities: {y_proba[i]}")
```

If you get a convergence warning, that's the `max_iter` note from today's Concepts doc — it's already handled above.
</details>

---

## Block 4 (Hour 4) — Read the Confusion Matrix
1. Compute the confusion matrix comparing `y_test` and `y_pred`.
2. Manually label which cell is TP, TN, FP, and FN based on today's Concepts doc.
3. Write one sentence on what a false positive means concretely in this dataset (predicted survived, actually didn't) versus a false negative (predicted didn't survive, actually did).

<details>
<summary>Hint</summary>

```python
from sklearn.metrics import confusion_matrix

cm = confusion_matrix(y_test, y_pred)
print(cm)
# Layout:
# [[TN, FP],
#  [FN, TP]]
```
</details>

---

## Block 5 (Hour 5) — Find a Case Where the Threshold Matters
1. Find at least one row in the test set where the predicted probability is close to 0.5 (say, between 0.4 and 0.6).
2. For that row, show what the predicted label would be at the default 0.5 threshold, and what it would become if the threshold were moved to 0.4 or 0.6 instead.
3. Write two sentences: why is 0.5 just a default and not a rule, and can you think of a real reason someone might want to lower the threshold for this specific problem (e.g., prioritizing catching more true survivors even at the cost of more false positives)?

<details>
<summary>Hint</summary>

```python
import numpy as np

proba_class1 = y_proba[:, 1]
borderline_idx = np.where((proba_class1 > 0.4) & (proba_class1 < 0.6))[0]
print(borderline_idx[:5])

i = borderline_idx[0]
print("Probability of survival:", proba_class1[i])
print("Label at 0.5 threshold:", int(proba_class1[i] >= 0.5))
print("Label at 0.4 threshold:", int(proba_class1[i] >= 0.4))
print("Label at 0.6 threshold:", int(proba_class1[i] >= 0.6))
```
</details>

---

## Block 6 (Hour 6) — Write It Up
Produce a short written summary (half a page) covering:
1. Your class balance check from Block 2 and whether it changes how you'd read an accuracy score
2. Your confusion matrix from Block 4, with the four counts labeled and one sentence on which type of error (FP or FN) feels more costly for this specific problem
3. Your threshold exploration from Block 5 and what it taught you about `predict()` being a choice built on top of `predict_proba()`, not a separate thing
4. One open question heading into Day 4 (e.g., "given the class balance here, is accuracy alone even the right metric to trust tomorrow?")

<details>
<summary>Hint</summary>

No code needed here — this is a reasoning check. If you're stuck on point 2, imagine explaining a wrong prediction to a person it directly affected: which kind of wrong feels worse to explain?
</details>

---

## End-of-Day Checklist
- [ ] Handled missing values simply and one-hot encoded categorical features
- [ ] Checked class balance before modeling and reasoned about what it implies for accuracy
- [ ] Fit a `LogisticRegression` model on training data only
- [ ] Used both `.predict()` and `.predict_proba()` and can explain the difference
- [ ] Read a confusion matrix and correctly labeled TP, TN, FP, FN
- [ ] Explored how moving the decision threshold changes predictions on a borderline case
- [ ] Wrote a short summary covering class balance, the confusion matrix, and the threshold exercise