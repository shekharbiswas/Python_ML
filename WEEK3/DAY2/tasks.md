# Week 3, Day 2 — Tasks
## Linear Regression & Train/Test Split (6-Hour Session)

Read today's Concepts doc first. Today uses the same **insurance.csv** dataset from Day 1 (Medical Cost Personal Datasets, Kaggle) — you already know its shape, target, and feature types, so today goes straight to modeling. Each task includes a hint — try it yourself first before opening the hint.

---

## Block 1 (Hour 1) — Prep the Data for Modeling
1. Load `insurance.csv` and rebuild `X` (features) and `y` (`charges`) as you did on Day 1.
2. One-hot encode the categorical columns (`sex`, `smoker`, `region`) using `drop_first=True`.
3. Confirm `X` is now fully numeric with `.dtypes`, and note the new column count after encoding.

<details>
<summary>Hint</summary>

```python
import pandas as pd

df = pd.read_csv("insurance.csv")
X = df.drop(columns=["charges"])
y = df["charges"]
X = pd.get_dummies(X, columns=["sex", "smoker", "region"], drop_first=True)
print(X.dtypes)
```
</details>

---

## Block 2 (Hour 2) — Train/Test Split
1. Split `X` and `y` into training and test sets, holding out 20% for testing, with a fixed `random_state`.
2. Print the shape of all four resulting objects and confirm the split proportions look right (roughly 80/20 row counts).
3. Write one sentence: what would you expect to go wrong later if you skipped this step and just fit on all the data?

<details>
<summary>Hint</summary>

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
print(X_train.shape, X_test.shape, y_train.shape, y_test.shape)
```
</details>

---

## Block 3 (Hour 3) — Fit the Model
1. Instantiate a `LinearRegression` model and fit it on `X_train`/`y_train` only.
2. Print `model.intercept_` and `model.coef_`.
3. Pair each coefficient with its column name so it's readable, and identify which feature has the largest positive coefficient.

<details>
<summary>Hint</summary>

```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)

print("Intercept:", model.intercept_)
coef_table = dict(zip(X_train.columns, model.coef_))
for name, value in sorted(coef_table.items(), key=lambda x: -x[1]):
    print(name, round(value, 2))
```

The `smoker_yes` coefficient is usually by far the largest — worth pausing on why that makes intuitive sense before moving on.
</details>

---

## Block 4 (Hour 4) — Predict and Get a First Score
1. Use the fitted model to predict on `X_test`.
2. Call `.score(X_test, y_test)` to get R² on held-out data.
3. Print 5 actual vs. predicted values side by side and eyeball how close they are.

<details>
<summary>Hint</summary>

```python
y_pred = model.predict(X_test)
r2 = model.score(X_test, y_test)
print("R² on test data:", round(r2, 3))

comparison = pd.DataFrame({"actual": y_test.values[:5], "predicted": y_pred[:5]})
print(comparison)
```
</details>

---

## Block 5 (Hour 5) — Prove the Overfitting Point to Yourself
1. Also call `.score(X_train, y_train)` — the score on the data the model was trained on.
2. Compare the train score to the test score from Block 4.
3. Write two sentences: are they close or far apart, and what would a *much* higher train score than test score suggest about the model?

<details>
<summary>Hint</summary>

```python
train_score = model.score(X_train, y_train)
test_score = model.score(X_test, y_test)
print("Train R²:", round(train_score, 3))
print("Test R²:", round(test_score, 3))
```

For simple linear regression on this dataset, the two scores should be reasonably close — a large gap is the signature of overfitting, which becomes more relevant with more complex models later in the module.
</details>

---

## Block 6 (Hour 6) — Write It Up
Produce a short written summary (half a page) covering:
1. Your final test R² score and what it means in plain language
2. The two or three features with the largest coefficients, and whether their direction (positive/negative) makes real-world sense
3. Your train vs. test score comparison from Block 5, and what it tells you about overfitting risk here
4. One open question heading into Day 3 (e.g., "how would this whole workflow change for a classification target instead of a numeric one?")

<details>
<summary>Hint</summary>

No code needed here — this is a reasoning check. If you're stuck on point 2, look back at the coefficient table from Block 3 and think about each feature causally: does it make sense that this feature would raise or lower medical costs, and by roughly this much?
</details>

---

## End-of-Day Checklist
- [ ] One-hot encoded categorical features and confirmed `X` is fully numeric
- [ ] Performed a train/test split with a fixed `random_state` and correct shapes
- [ ] Fit a `LinearRegression` model on training data only
- [ ] Read and interpreted the model's coefficients against real column names
- [ ] Got and interpreted a test-set R² score
- [ ] Compared train vs. test scores and reasoned about overfitting
- [ ] Wrote a short summary covering score, coefficients, and the train/test comparison