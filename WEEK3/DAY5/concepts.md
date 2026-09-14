# Week 3, Day 5 — Concepts
## Wrap-Up & Presentation of Learning Outcomes

---

## Question of the Day
**"Can I take a new dataset and, unaided, run the full fit/predict/score loop and explain the result?"**

Today is intentionally light. There's no new technique to learn — everything needed for a complete, defensible supervised learning workflow was covered Monday through Thursday. Today is about consolidating that into something you can explain out loud, clearly, to someone who wasn't in the room for the week.

---

## 1. What This Week Actually Built, End to End
Trace the full arc before presenting it:
- **Day 1** — telling regression and classification apart by looking at the target
- **Day 2** — the train/test split discipline, and fitting/interpreting a linear regression model
- **Day 3** — the same discipline applied to a classifier, plus what a probability, a threshold, and a confusion matrix actually are
- **Day 4** — matching the right metric to the real question being asked, and never trusting a score without a baseline next to it

Each day added exactly one new piece on top of the last. The point of today's presentation is to show that arc, not just the final result.

---

## 2. What a Good Presentation of This Week Looks Like
This isn't a formal deck — a short walkthrough (notebook, slides, or just talking through your written summaries) covering:
- Both problems tackled this week (insurance cost regression, Titanic survival classification) and why each is the problem type it is
- One thing that surprised you or that you got wrong initially and had to correct
- Your final metrics for both models, stated in plain language a non-technical person would understand (not just "R² was 0.75" on its own, but what that means)
- The single most important habit from this week you'd carry into any future modeling work (most people land on the train/test split discipline or the baseline-comparison habit — both are correct answers)

---

## 3. The One Idea Worth Over-Explaining
If there's one thing worth being able to explain clearly and confidently by the end of today, it's this: **a model's score only means something in comparison to something else** — a held-out test set it never saw, and a trivial baseline it should be beating. Everything else this week (splits, metrics, thresholds) exists in service of that one idea.

---

## What Success Looks Like Today
By the end of Day 5, you should be able to:
- Explain, without notes, why regression and classification need different models and different metrics
- Walk someone through both of this week's models and their results in plain language
- Point to the train/test split and baseline-comparison habits as the throughline of the whole week, not isolated steps