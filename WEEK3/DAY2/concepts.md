# Week 3, Day 2 — Concepts
## Linear Regression & Train/Test Split

---

## Question of the Day
**"How do I fit a regression model properly, and why can't I trust a score computed on the data it trained on?"**

Yesterday was about correctly framing a problem as regression or classification. Today is the first time a model actually gets fit — but the real point of today isn't linear regression itself (it's one of the simplest models that exists). The real point is the discipline that has to wrap around *any* model from here forward: never evaluate on the data the model has already seen.

---

## 1. Why You Can't Just Train and Score on the Same Data
If a model is fit on 100 rows and then scored on those same 100 rows, the score answers the wrong question. It tells you how well the model **memorized** those specific rows, not how well it will perform on a new row it's never seen. A model can achieve a near-perfect score on training data while being nearly useless in the real world — this gap is called **overfitting**, and it's the single most important failure mode to guard against starting today.

The fix is simple in mechanics, foundational in importance: split the data *before* fitting anything, train on one part, and evaluate only on the part the model never touched.

---

## 2. `train_test_split` — The Mechanics
```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
```
- **`test_size=0.2`** — holds out 20% of the data purely for evaluation; 80% remains for training. 20-30% held out is a common default for small-to-medium datasets.
- **`random_state=42`** — fixes the random shuffle so the split is reproducible. Without it, re-running the script gives a different split every time, and results become impossible to compare run-to-run. The number itself is arbitrary — what matters is that it's fixed.
- The function shuffles by default before splitting, which matters if the original data is sorted in some way (e.g., by date, by target value) — without shuffling, the split could accidentally put all of one kind of row into training and none into test.

**The rule going forward:** `X_test` and `y_test` are untouched by anything except the final `.score()` or `.predict()` call. No exploring them, no fitting on them, no peeking at them to "double check" — that quietly reintroduces the exact problem the split exists to prevent.

---

## 3. Linear Regression — What It's Actually Doing
Linear regression fits a straight-line (or hyperplane, with multiple features) relationship between features and target:

$$\hat{y} = w_1x_1 + w_2x_2 + \dots + w_nx_n + b$$

- Each **coefficient** (`w`) represents how much the prediction changes for a one-unit increase in that feature, holding the others constant.
- The **intercept** (`b`) is the prediction when all features are zero — sometimes meaningful, sometimes just a mathematical anchor point (e.g., "age 0, BMI 0" isn't a real person, so the intercept alone isn't meaningful for the insurance dataset).
- The model finds the line that minimizes the total squared distance between predictions and actual values (this is why RMSE, tomorrow's topic in more depth, is the natural metric for it).

---

## 4. The Fit/Predict/Score Loop, For Real This Time
```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)          # learn coefficients from training data only
y_pred = model.predict(X_test)       # predict on held-out data
model.score(X_test, y_test)          # R² on held-out data — how it's actually used
```
- `.fit()` learns `model.coef_` (the weights) and `model.intercept_` from `X_train`/`y_train` only.
- `.score()` on a regression model returns **R²** by default — a preview of tomorrow's metrics content, but useful today as a first, honest read on model quality precisely because it's computed on `X_test`.
- `model.coef_` is inspectable directly after fitting — pairing each coefficient with its column name (e.g., `dict(zip(X_train.columns, model.coef_))`) turns raw numbers into a readable story about which features move the prediction most.

---

## 5. Handling Categorical Features Before Fitting
Linear regression only works with numbers — a column like `smoker` (`"yes"`/`"no"`) or `region` (`"southwest"`, `"northeast"`, ...) has to be converted first. The standard tool today is **one-hot encoding**:
```python
X = pd.get_dummies(X, columns=["sex", "smoker", "region"], drop_first=True)
```
`drop_first=True` avoids the "dummy variable trap" — keeping all categories as separate 0/1 columns creates redundant information (if you know a row isn't `northeast`, `northwest`, or `southeast`, it must be `southwest` — that fourth column adds nothing). This is a preview; feature encoding gets a deeper pass in Week 4, but today's model needs it to run at all.

---

## What Success Looks Like Today
By the end of Day 2, you should be able to:
- Explain, in plain language, why scoring a model on its own training data is misleading
- Correctly perform a train/test split with a fixed `random_state` and explain what each parameter does
- Fit a `LinearRegression` model and read its coefficients as a real statement about the data (not just numbers)
- Encode simple categorical features so a linear model can use them
- Get a first R² score on genuinely held-out data and explain, at a basic level, what that number means