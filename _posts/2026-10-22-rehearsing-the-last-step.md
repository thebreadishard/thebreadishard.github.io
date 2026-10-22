---
layout: post
title: "Rehearsing the last step"
date: 2026-10-22 06:44:01 +0200
categories: plan05 progress
---

<!-- Written 21-22 Oct 2026 (the user: 'later vandaag of morgen ook een blog'; rewritten 22 Oct morning on their request: simpler,
     no skipped steps; 'What this means' answers its own question); published 22 Oct on the user's word ('Publiceer maar').
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/PreRegistration_2026-10-12_Anchor_Set_Three.md, Outcome: the final read (20 Oct 23:58; R-A 14.06 -> 13.69, R-B 0 of 3)
       and the whole-head repeat (21 Oct 09:22, commit 3c088dd1; amendment 2801b401): R-A 7.24 -> 6.25 (-13.8 %), R-B 3 of 3;
     GoalGathering/notes/Diagnosis_2026-10-21_Set_Three_Read.md (commit 409a58c6): W06 7.76 / E18 0.80 on the training anchors; E01 12.29 ->
       E16 6.54 on held-out anchors, 3 repeats;
     GoalGathering/notes/Design_2026-10-21_Few_Anchor_Proxy_Check.md (registered, commit bf408823; read 22 Oct 02:1x, commits 62474707 and
       07818876, 'Outcome'): arm (a) CH-oop lambda 1 9.05 / 8.09 / 9.90, whole head 6.55 / 5.08 / 4.04 / 3.81 / 3.17 / 2.82 (k 5 / 10 /
       20 / 50 / 100 / 200), lambda 0 5.90 at k 5, scratch
       7.33 / 5.38 / 4.76 (k 50) / 2.79 (k 5 / 20 / 200); arm (b) base as is 2.66; read sets 343 and 27 molecules. -->

The project tries to predict the infrared spectra of large carbon molecules accurately without paying for the most expensive
calculations. The plan has two parts. A cheap quantum-chemistry method gives every molecule's vibrations, but with a systematic error.
A neural network learns to correct that error.

The correction we really want points towards [coupled cluster](/glossary/#coupled-cluster), the reference method. But one coupled-cluster
calculation of the vibrations costs days for a small molecule, and it is impossible for the large ones we care about. So we cannot show
the network many examples of the real correction. We do it in two stages:

1. **Practice on a stand-in.** The network first learns a different correction that is cheap to compute for hundreds of molecules: the
   difference between two cheaper methods. From that it learns what a correction looks like in general and how it changes with the size
   of a molecule.
2. **Adjust with a few real examples.** Then we show it a handful of molecules where we do have coupled cluster, our *anchors*, and let
   it adjust.

This post is about stage 2, which did not work at first.

## How we let the network adjust

A neural network is a stack of layers. The first layers turn a molecule into a description the network can work with; the last few
layers, which I call the *head*, turn that description into the correction. When a network adjusts to new examples, you choose which
layers may change.

We let only the very last layer change, and we added a brake: a penalty that keeps that layer close to what it learned from the
stand-in. The idea was caution. With only eleven anchors, a network that changes too much can learn the eleven by heart and get worse
on everything else.

## Eleven anchors, a flat line

The registered test (written down before we looked) asked: do more anchors help? We looked at the out-of-plane C–H bending vibrations,
where the hydrogen atoms move up and down out of the plane of the rings. These are the hardest for the network and important in the
spectrum.

The answer was no. On the six original anchors the error went from 14.06 to 13.69 cm⁻¹ when five new anchors joined, which is within
the noise. The predicted brightness of the lines was also worse than not correcting at all, on all three anchors where we could check it.

## A night of asking why

"The idea does not work" was one possible reading. But there are other possible causes: too few anchors, wrong settings, the wrong
layers allowed to change, the network's design. Overnight I tested more than twenty of them, one at a time, on the data we had.

The test that decided it is an unusual one. Normally you test a network on molecules it has *not* seen, because the question is whether
it can predict new ones. This time I did the opposite on purpose: I let the network adjust on all eleven anchors and then tested it on
*those same eleven*.

That is like asking a student to answer the exact questions they just studied, with the answers in front of them. A student who still
gets them wrong does not need more practice questions: something stops them from taking the answers in. The same holds for the network.
If it cannot reproduce the anchors it was just shown, more anchors will not help; the way it is allowed to learn is too narrow.

That is what happened. With only the last layer allowed to change, the network stayed 7.76 cm⁻¹ off on the very anchors it had been
shown. With the whole head allowed to change, it reproduced them to within 0.80 cm⁻¹. It *could* take the answers in; we had not let
it. Tested properly again, on anchors it had not seen, the error halved: from 12.29 to 6.54 cm⁻¹, averaged over three repeats.

A second finding explains why the brake hurt. For the out-of-plane vibrations, the coupled-cluster correction does not point the same way
as the stand-in correction; it partly points the opposite way. A brake that holds the network near the stand-in holds it near the wrong
answer.

## Repeating the test, honestly

The next morning we ran the registered test again, this time with the whole head free and the predictions written down first. Both came
out as predicted. The out-of-plane error now falls with every added anchor, from 7.24 to 6.25 cm⁻¹ (14 % lower), and the line
brightness beats "no correction" on all three anchors where we can check it.

That is encouraging, but it is not proof. I chose the whole-head approach *by looking at these same eleven anchors*. It is like adjusting
a recipe by tasting one pot of soup and then judging the recipe by tasting the same pot again. To judge the recipe you need other pots.

## A rehearsal with hundreds of molecules

We will not have more coupled-cluster anchors before next summer. So we rehearsed stage 2 in a setting where we do have hundreds of
correct answers: the cheaper methods. We built two rehearsals.

- **A different correction.** We taught a network a deliberately wrong correction, bent family by family the way coupled cluster
  differs from the stand-in. Then we gave it *k* molecules with the right answer and let it adjust. We judged it on 343 other molecules
  it never adjusted on. This copies our real situation: a network that learned something close but wrong has to switch with few
  examples.
- **A new kind of molecule.** We taught a network the right correction but kept one whole family of molecules out of its training. Then
  we gave it *k* molecules of that family and judged it on the other 27 of that family.

For each *k*, from 5 to 200, four ways of adjusting competed: the last layer with the brake (the old way), the last layer without the
brake, the whole head free, and a fresh network that learns from the *k* molecules alone. The fresh network tells us what the earlier
practice is worth.

Before anything ran, we wrote down what we expected: in the first rehearsal the whole head should beat the old way; in the second
probably not, because there the correction does not change, only the molecules.

## What the rehearsal said

Both expectations held.

In the first rehearsal, the old way did not learn at all. With the brake on, the out-of-plane error stayed between 8 and 10 cm⁻¹ whether
the network saw 5 molecules or 200: the brake kept it on the wrong correction. With the whole head free, the error fell steadily, from
6.6 cm⁻¹ with 5 molecules to 4.0 with 20 and 2.8 with 200, and the line brightness improved with it. One nuance: with only five
molecules, freeing just the last layer (without the brake) did slightly better (5.9) than freeing the whole head. From twenty molecules
on, the whole head led.

The earlier practice was worth something even though it had learned the wrong correction, and most of all when examples were few. With
20 molecules, the adjusted network (4.0) was already better than a fresh network that had seen 50 (4.8). With more examples the gap
closed, and at 200 molecules the two were equal.

In the second rehearsal, the network was right before it adjusted at all: about 2.7 cm⁻¹ on a family it had never seen. Adjusting
changed little. That was expected too: what it had learned carried over to the new molecules.

## What this means

Our eleven anchors failed the first test because of how we let the network learn, not because there were too few of them or because the
idea is wrong.

The rehearsal also gives a first answer to the question the next phase has to put a price on: how many anchors buy how much accuracy.
With the whole head free, the out-of-plane error in the rehearsal was:

| molecules shown | 5 | 10 | 20 | 50 | 100 | 200 |
|---|---|---|---|---|---|---|
| error (cm⁻¹) | 6.6 | 5.1 | 4.0 | 3.8 | 3.2 | 2.8 |

Most of the gain comes from the first twenty or so: going from 5 to 20 molecules removes more error than going from 20 to 200. After
that, each doubling buys a few tenths of a wavenumber. Our own target for this family is 3 cm⁻¹ or better; in the rehearsal that took between
100 and 200 molecules. For coupled cluster that would be far more anchors than we have, so for the next phase the curve says: a few
dozen anchors buy most of the improvement, and the last step to 3 cm⁻¹ is the expensive part.

It is a rehearsal, so it has limits: the stand-in replaces coupled cluster, there is one way of making the correction different, and one
held-out family. It is a guide for the real step, not a guarantee.
