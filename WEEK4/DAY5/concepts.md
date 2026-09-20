# Week 4, Day 5 — Tasks
## Presentation Prep for the 3:30 PM Presentation (Home Credit Default Risk)

Read today's Concepts doc first. **No new modeling today.** All results, tables, and figures should already be in your `results/` folder from Day 4. Today you turn them into a clear, honest, well-rehearsed presentation.

**Timing:** the blocks below are one hour each and assume roughly an 8:30 AM start, so Block 6 ends around 2:30 PM and leaves a full hour of buffer before 3:30 PM. Shift the schedule if your day starts later, but protect the buffer and the two rehearsals.

---

## Block 1 (Hour 1) — Audience, Slot, and Storyline
1. Confirm the **length of your slot** and how much of it is Q&A, and who the audience is. Adjust everything below to that.
2. Write the **one-sentence main message** of your project.
3. Draft the slide outline (8–10 slides) with one full-sentence message per slide, following the arc from the Concepts doc: problem, data, evaluation design, baselines, improvement journey, pandas vs. Pipeline, diagnosis, final results, limitations, takeaways.
4. Decide what to cut or move to an appendix. Anything that doesn't support the main message goes.

---

## Block 2 (Hour 2) — Gather and Verify Assets
1. Collect from `results/` the tables and figures each slide needs: progress/ablation table, single-split-vs-CV figure, learning curve, ROC/PR curves, lift table, threshold confusion matrix, top features.
2. **Verify every number** against the notebook and the decision log. Check consistency: the same metric must have the same value everywhere.
3. Regenerate any figure that needs better labels or larger fonts, from saved results only. Do not retrain or re-tune anything.
4. Make an asset list mapping each slide to its figure or table.

---

## Block 3 (Hour 3) — Build the First Draft of the Deck
1. Create all slides with full-sentence titles and place the main visual on each.
2. Write the framing slides first (problem, data, evaluation design), in business language, then the results slides.
3. Prepare the **pandas vs. Pipeline** slide as a concrete example: two scores side by side and one sentence on what caused the gap.
4. Add a short **limitations and next steps** slide, using the limitations from your Day 4 summary.
5. Keep text minimal; save detail for speaker notes.

---

## Block 4 (Hour 4) — Polish and Speaker Notes
1. Fix the visual basics: readable fonts, labeled axes, consistent colors, aligned layout, no clutter.
2. Write speaker notes for every slide: the point to land, the evidence behind it, and the transition to the next slide.
3. Prepare the "why" answer for each major choice (metric, CV design, tuning approach, features, threshold), in one or two sentences with the supporting number.
4. Add backup slides for details likely to come up in questions (hyperparameter table, extra curves, data quality notes).

---

## Block 5 (Hour 5) — First Timed Rehearsal and Q&A Prep
1. Rehearse the full talk **out loud, timed**. Note where you run long, stumble, or rely on reading.
2. Cut or tighten content to fit the slot with room to spare.
3. Write down the 8–10 questions you're most likely to be asked, for example: Why not accuracy? Could there be leakage? Why this threshold? What would you do with more time? What are the risks of using this in a real lending decision? Draft short answers.
4. Practice at least two answers to questions you *can't* fully answer, using "I don't know, but here's how I'd find out."

---

## Block 6 (Hour 6) — Revise, Second Rehearsal, and Backups
1. Apply the fixes from the first rehearsal.
2. Do a **second full, timed rehearsal**, ideally in front of someone, and confirm it fits the slot.
3. Export the deck to **PDF** as a backup, and keep the key figures as image files.
4. Run a technical check: screen sharing or projector connection, slide transitions, fonts, file locations, and that nothing depends on a live notebook.
5. Finalize by around **2:30 PM**.

---

## Buffer (2:30–3:30 PM)
- Final read-through of the opening and closing slides, and a quick look at the Q&A list
- Have the deck and PDF open and ready, with water and a settled mind
- No last-minute edits to numbers or slides unless something is clearly wrong

---

## End-of-Day Checklist
- [ ] Slot length and audience confirmed; one-sentence main message written
- [ ] 8–10 slide outline with one message per slide
- [ ] All numbers verified against the notebook and decision log
- [ ] Figures readable and consistent, with no retraining done today
- [ ] Speaker notes and "why" answers prepared for each major choice
- [ ] Two timed rehearsals completed and the talk fits the slot
- [ ] Q&A list drafted with answers, including honest "I don't know" responses
- [ ] Deck exported to PDF as a backup and technical check done
- [ ] Finished by about 2:30 PM, with the buffer protected before 3:30 PM