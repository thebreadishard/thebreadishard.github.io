---
layout: post
title: "Half price, if you write the prediction down first"
date: 2026-10-14 08:15:00 +0200
categories: plan05 progress
---

<!-- Written 14 Oct 2026 at the user's request ("Ja oké" to proposal 1); published the same day on the user's word ("Publiceer maar").
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/PreRegistration_2026-10-13_S1_Anharmonic_Level.md (registered 13 Oct 08:40, commit b3777c42; amendment 1, place,
     commit 7716b936; amendment 2, decision rule and prices, commit 241ab079; outcome, commit 816f3f9a),
     probes/results_m1/s1_anharmonic_benzene_2026-10-13/s1_benzene/compare.json (Δ_anh per mode; G and B against R),
     probes/results_m1/qff_symmetry_count_2026-10-13/counts.md (Hessians per molecule and prices),
     GoalGathering/notes/Design_2026-10-13_Spectral_Shape_Anharmonic_Step.md §2–3 (the €2,750 table; the 3–5× hope of route (a)),
     TASKS.md row 53 (per-Hessian times on the server: medians of the file times). -->

Until now this blog has been about the *positions* of infrared lines and, last time, their *heights*. A real spectrum has a third
ingredient: its *shape*, and getting that right turned out to be the most expensive part of the whole plan. This post is about how a
prediction written down on Tuesday morning halved that price by midnight.

**Springs that are not quite springs.** Our method treats the bonds of a molecule as springs. A perfect spring has one stiffness however far
you stretch it, and a molecule built from perfect springs would vibrate at exactly the frequencies we compute. Real bonds are not perfect:
stretch one far enough and it gets softer, because at the end it breaks. That softening pulls every line in the spectrum down a little.
On benzene the ring vibrations move down by about 4 to 40 wavenumbers, and the C–H stretches, where light hydrogen atoms swing far, by about
140. The difference between the perfect-spring frequency and the real one is called the *anharmonic shift*.

For the line positions that our network corrects, the shift is a small add-on. For the shape of the spectrum it matters more: it moves
lines into or out of the bands where astronomers look, and it creates weak extra lines where two vibrations combine.

**Why it is expensive.** A perfect-spring spectrum needs one calculation of the molecule's stiffness: one *Hessian*, in the jargon. The
anharmonic shift needs to know how that stiffness itself changes when the molecule is pushed along each of its vibrations, in both
directions. So you compute the Hessian again at two pushed shapes per vibration. Symmetry spares some of that work, but for the five large
molecules we want to predict, from tetracene (30 atoms) to hexabenzocoronene (60 atoms), it still comes to between 100 and 192 Hessians
each. At our standard settings that added up to about €2,700 of rented computer time, more than half of it for the largest molecule alone.
The plan had written this line off as too expensive.

**Two cheaper ways to compute the same thing.** A Hessian has two dials that set its cost. One is the *basis set*: how flexible the
description of the electrons is. The other is the *integration grid*: on how many points in space the electrons' energy is added up. Turning
either down makes each Hessian cheaper. The question is whether the anharmonic shift notices. The shift is a *difference* between two
frequencies, and differences often forgive errors that both sides share.

On Tuesday at 08:40, before a single number existed, we wrote down a test on benzene:
- the reference: our standard basis and the fine grid;
- **G**: the same basis on a coarser grid;
- **B**: a smaller basis, without the functions that let electrons lean out of the plane of the ring, on the fine grid.

A cheaper setting would *pass* if, over the part of the spectrum the shape score looks at, its shifts differed from the reference's by at
most 1 wavenumber on average (root mean square) and 3 at worst. And my prediction, written in the same note: **G passes, B fails.** The
reasoning was that grid errors mostly cancel when you take a difference, while the missing out-of-plane functions change how the ring bends,
and so plausibly how its bending softens.

**The run.** The laptop was busy until the end of the month, so the test moved to a small rented server for the evening. All three
settings ran side by side, and the server timed every Hessian. The first number of interest was not a shift but a price: the coarse grid
took 112 seconds per Hessian, the smaller basis 162 and the reference 234.

Here I have to correct myself. The design note had hoped for a setting three to five times cheaper. The coarse grid is 2.1 times cheaper,
the small basis 1.4. That is still worth having, but it was an estimate I had not measured, and it was wrong by more than half. The measured
number went into the decision before the result did.

**The result.** The reference finished just after midnight.

| setting | average difference | largest difference | verdict |
|---|---|---|---|
| G, coarse grid | 0.19 | 0.35 | passes (limits 1 and 3) |
| B, smaller basis | 3.16 | 7.51 | fails |

*(wavenumbers, over the 22 benzene vibrations between 550 and 2,000)*

Both halves of the prediction held. The coarse grid changes the shifts by less than a fifth of a wavenumber; the smaller basis changes them
by up to seven and a half. The out-of-plane functions matter for how the ring softens; the grid hardly does.

**What it buys.** With the coarse grid the five large molecules cost about €1,300 instead of €2,700. More importantly, the part we have
committed to now costs about €250: a training set of twenty small molecules to see whether the shift can be *learned*, and the full
calculation for three of the five large molecules, which is where a learned shift will be checked. That training run started this morning
on the same server. The two largest molecules wait. If a learned shift works on the first three, they may not need the full treatment at all.

**Why write it down first.** The coarse grid would have looked like a good idea whatever we had written beforehand. The point of the note
is what it rules out. Once the shifts are in front of you, it is easy to choose the limits that the result happens to meet, or to decide
afterwards that a failing setting "was never really expected to pass". A prediction and two limits fixed fifteen hours earlier leave no such
room. If the coarse grid had come in at 1.2, it would have failed, and the €2,700 would have stood.

**What this is not.** One molecule, and the smallest of all aromatic molecules at that. The 2.1 is benzene's price ratio: on larger
molecules the grid takes a smaller share of the work, so the saving will probably be smaller there. Each large molecule's first Hessian is
timed before the rest runs. And the coarse grid passing on benzene licenses it for the training set and the three molecules above, under
the same check, not for every molecule we will ever compute.
