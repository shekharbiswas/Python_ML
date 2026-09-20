# Module 4: Python + Machine Learning (6 Weeks)
### Part of the 24-Week Data Engineering with AI & Cloud Program

---

## Module Overview

This module builds directly on the SQL, dbt, and Snowflake foundations already covered earlier in the program. Nothing here re-teaches those — Snowflake is the data source and dbt is the transformation layer throughout, and the new skill being layered on top is Python and applied machine learning.

Six weeks, one continuous build: Python fundamentals → data wrangling → machine learning → model tuning → full end-to-end pipelines → capstone. Two projects are built into the module — a mini-project at the Week 4 midpoint, and a full capstone in Week 6 — so there's tangible proof of progress before the module ends, not just exercises.

**Tech stack used throughout:** Python 3, pandas, NumPy, scikit-learn, snowflake-connector-python (or snowflake-sqlalchemy), Snowflake, dbt.

---

## Prerequisites (from earlier modules)

- Comfortable writing SQL, including joins, aggregations, and window functions
- Working knowledge of dbt: models, staging vs. marts layers, `dbt run` / `dbt test`
- Snowflake basics to mid-level: warehouses, stages, loading data, querying
- No prior Python or ML experience required — Week 1 starts from the ground up on that side

If any of the SQL/dbt/Snowflake items feel shaky, a quick refresh before Week 1 is worth it — this module assumes it as a given and moves fast.

---

## Weekly Breakdown

### Week 1 — Python Foundations
**Topics:** Variables and data types, control flow (if/for/while), functions, lists/dicts/tuples/sets, list comprehensions, basic OOP (classes, objects), working with virtual environments and `pip`, reading/writing files.
**Snowflake/dbt tie-in:** Install and configure `snowflake-connector-python`, authenticate, and run a first query — pulling a Snowflake table directly into a Python script.
**By end of week:** Can write clean, working Python scripts and pull data from Snowflake into Python without help.

### Week 2 — Data Wrangling with pandas
**Topics:** `pandas` DataFrames and Series, indexing/filtering/sorting, handling missing data, merging/joining datasets, groupby and aggregation, `NumPy` array basics, basic exploratory data analysis (EDA) and simple visualizations (matplotlib/seaborn).
**Snowflake/dbt tie-in:** All exercises pull raw data from Snowflake into pandas; students compare the raw data against their own dbt staging models to see directly what the dbt transformations cleaned up.
**By end of week:** Can take a messy, real Snowflake dataset and produce a clean, well-understood DataFrame ready for analysis or modeling.

### Week 3 — Machine Learning Fundamentals
**Topics:** Supervised learning overview, linear and logistic regression, classification vs. regression, train/test split, core evaluation metrics (accuracy, precision/recall, RMSE, R²), intro to `scikit-learn`'s API and workflow (`fit`/`predict`/`score`).

**By end of week:** Can train, evaluate, and interpret a basic regression and classification model end-to-end.

### Week 4 — Model Tuning + Project 1
**Topics:** Cross-validation, hyperparameter tuning (grid search / random search), feature engineering and selection, handling categorical variables and scaling, overfitting/underfitting and how to diagnose it.

**Pandas/sklearn tie-in:** A direct, hands-on comparison: doing a feature engineering task as ad-hoc pandas code versus rebuilding the same task as a scikit-learn Pipeline with ColumnTransformer, to build real intuition for why preprocessing belongs inside the modeling pipeline (reproducibility, avoiding data leakage, plugging into cross-validation and hyperparameter search).

**📌 Project 1 (Week 4):** A focused mini-project on a single dataset — features prepared, a model trained and evaluated in Python, and a short written summary of the results, the modeling choices made, and why.

### Week 5 — End-to-End ML Pipelines
**Topics:** Structuring a repeatable ML workflow, separating data prep / training / evaluation into clean stages, writing results back to a database, basic pipeline reliability (logging, error handling), intro to model persistence (saving/loading a trained model).
**Snowflake/dbt tie-in:** The full loop is built and run: dbt transforms raw Snowflake data → Python reads the resulting feature table → trains and evaluates a model → predictions/results are written back into Snowflake.
**By end of week:** Can describe and build, from memory, a working dbt-to-Python-to-Snowflake pipeline.

### Week 6 — Capstone Project
**Focus:** Independent project work, applying everything from Weeks 1–5 to a self-chosen (or assigned) real dataset, with instructor/mentor check-ins rather than new topics.

**🏆 Capstone (Week 6):** A complete, production-style project — raw data loaded into Snowflake, transformed with dbt, modeled in Python, results delivered back out — documented as a full repo: raw/source data references, dbt models, Python code, results, and a README explaining the approach and findings.

---

## Projects at a Glance

| | Project 1 (Week 4) | Capstone (Week 6) |
|---|---|---|
| **Scope** | Focused, single dataset | Full pipeline, production-style |
| **Goal** | dbt-prepared features → trained, evaluated model | Own the entire loop start to finish |
| **Deliverable** | Model + short write-up | Full repo: data, dbt models, Python code, results, README |
| **Presentation** | Informal walkthrough | Presented like a real project — code, reasoning, and results |

---

## What You'll Be Able to Do After This Module

- Write solid, practical Python for data work — not just syntax, but as a daily tool
- Clean, explore, and reshape real data with `pandas` and `NumPy`
- Train, evaluate, and tune machine learning models with `scikit-learn`
- Query and write to Snowflake directly from Python
- Design and build a complete dbt → Python → Snowflake pipeline, and explain the trade-offs of doing transformation work in SQL vs. Python
