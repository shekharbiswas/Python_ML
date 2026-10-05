# Week 6: Finish the Pipeline, Then Reuse It

## Week Goal
Week 5 asked how to turn a model into a repeatable pipeline. Some of that work is not finished yet, so Week 6 starts by completing the NYC taxi pipeline. After that, the same pipeline becomes the starter template for your own project.

**Key theme:** *A pipeline is finished when someone else can run it from scratch and get the same result.*

---

## How This Week Works
1. Days 1-2 are for catching up, not for adding new material.
2. Everyone keeps working in the same Week 5 repo.
3. Focus stays on the pipeline: stages, config, logging, and checks.
4. No new models and no extra datasets in Days 1-2.
5. The test month stays locked until the final evaluation.

---

## Week Structure

| Day | Focus | Question It Answers |
|-----|-------|---------------------|
| 1 | Finish data stages: ingest, audit, clean | "Can I get from raw data to a clean, checked table with one command?" |
| 2 | Finish modeling stages: features, split, baseline, train, save | "Can I train and save a model from the clean table, without leakage?" |
| 3 | One-command pipeline, config, logging, final test evaluation | "Can someone else run my project and get the same result?" |
| 4 | Start your own project using the pipeline as a template | "What do I keep, and what do I change?" |
| 5 | Continue your project and wrap up | "What did I build, and what did I learn?" |

Days 3-5 are an outline. Adjust them once Days 1-2 are done.

---

## Day 1: Data Stages
Finish whatever is missing from this list.

- Ingest: monthly taxi files, zone lookup, weather, holidays
- Data-quality audit saved to `results/data_audit.csv`
- Cleaning rules as functions, with `results/cleaning_report.csv`
- Checks after each stage that stop the run if something is wrong

**Done when:** `python run_pipeline.py --stage clean` runs and produces clean files in `data/processed/`.

---

## Day 2: Modeling Stages
Finish whatever is missing from this list.

- Complete zone by hour table with zeros filled in
- Joins that never change the row count
- Lag and calendar features that respect the information rule
- Time-based split with a locked test month
- Baseline: same zone, same hour, last week
- One sklearn pipeline, one model, saved with `joblib`

**Done when:** the baseline score is recorded, one model is trained and saved, and a separate script can load it and predict.

---

## What You Should Have by End of Day 2
```
project/
├── config.yaml
├── run_pipeline.py
├── src/
├── models/
├── results/
└── README.md
```

- A cleaning report and a data audit
- A baseline score and a validation score
- A saved model
- A short decision log

---

## Working Rules for Days 1-2
- Do not start new features or try new models.
- If a stage is already done, check it and move on.
- If you are stuck, write down what is blocking you and continue with the next stage.
- Keep the repo runnable after every change.

---

## Looking Ahead
From Day 3 the pipeline becomes a template. The stages stay the same, and only the data, target, and config change for your own project.