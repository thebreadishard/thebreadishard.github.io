---
layout: post
title: "The five-membered ring"
date: 2026-10-09 17:49:00 +0200
categories: plan05 progress
---

<!-- Published 9 Oct 2026 on the user's word ("The five-membered ring. Publiceer.").
     Numbers trace to (CapstonePlan repo): chain 38's read 97286ef (out/read_chain38_{ablation_fivering,control}_2026-10-09.md and the per-seed
     eval json), the five-ring pool list and price ab77121 / deb53ad (corpus/five_ring_pool.py), chain 39's read 607287c8, the DFT timings
     83e28387 (pool 3 runner logs), the spot-check coordinates 7cf1dd6, the intensity read-out and its pairing defect dfcb704 / ecb950f,
     the cation gate fea9b388. -->

The last post ended with a hunch. Two hundred new molecules had not helped the network on two families it had never seen, fluorene and
fluoranthene, and we noted that both contain a five-membered ring while the new molecules did not. That was an observation, not yet a result.
This week we tested it.

**How we score the network.** The network corrects a cheap calculation of how a molecule vibrates, and those vibrations decide where the lines of
its infrared spectrum fall. We score it by how much of the cheap calculation's error is left after the correction: 0 would be perfect, 1 means no
better than not correcting at all. We test it on molecules from families it has never been trained on, because that is what it will meet in
practice.

**The five-membered ring.** Most of the molecules we train on are built from six-membered carbon rings. A few families also contain a ring of
five: acenaphthylene, and three families in which one atom of that ring is nitrogen, oxygen or sulphur. We trained the network twice, once with
those 59 molecules taken out and once on a pool of exactly the same size with them in, and wrote down beforehand that a difference of 0.03 would
count. On the unseen fluorene and fluoranthene families the score went from 0.317 with them to 0.410 without them, a difference of 0.09, and the
three repeats of each training did not overlap. Inside a second test set of ten unrelated molecules, exactly two moved: the parent molecules of
fluorene and fluoranthene themselves (0.25 to 0.39 and 0.21 to 0.30). The other eight changed by less than 0.06.

So the network learns a family it has never seen from other families that share its ring shape, even when they differ in everything else. It did
not learn it from more molecules of other shapes, such as last week's children of the four-ring pyrene. The next step follows from this: 181 more
molecules from those four five-ring families, already listed and costed from the times we measured last week. They will run on the laptop and,
once it is free, on the rented server that is computing the current batch, for at most about €30.

**Small does not teach large.** The molecules astronomers care about are larger than the ones we can afford to compute exactly. So we asked how
well the network carries what it learned from small molecules to large ones. Trained only on molecules of up to 20 atoms, it left 0.69 of the
error on molecules of 27 atoms and more. Trained on the same number of molecules spread over all sizes below 27, it left 0.35. In frequency terms
that is about 5.6 against 3.7 cm⁻¹, where no correction at all leaves 23. One caution: in our collection the small molecules have one or two
rings and the large ones three or four, so this test cannot tell size from ring count. Given the five-ring result, ring count is probably a large
part of it.

The good news is that the cheap level reaches far. Half of one rented server computed both cheap calculations for perylene, 32 atoms, in four
hours, and for a 38-atom molecule in under eight. The training collection can therefore contain the large families themselves, and it will.

**Checking the network where we cannot afford the answer.** The expensive reference calculations stop at about 26 atoms. Above that we plan
spot checks: the reference value for a few chosen directions of motion of perylene and one other large molecule, compared with the network's
prediction. The directions are picked by a fixed rule, and for perylene they were drawn today, before any prediction for it exists, so the choice
cannot be steered by the answer.

**A number that was too tidy.** Infrared lines have a position and a height. We had a test of whether the network's correction also gets the
heights right, for benzene at the expensive level. It gave 0.73 in every column, including the column with no correction at all. A test that
cannot tell a corrected molecule from an uncorrected one is measuring something else. It was: the test lined up the lines in order of position,
and in benzene a bright line and a dark line, both near 700 cm⁻¹, swap places between the cheap and the expensive calculation. The test was
comparing a bright line with a dark one. Paired by the shape of the motion instead, every column is within about two per cent of the true heights. That also
shows benzene is too symmetric to test this question: its symmetry fixes the heights whatever the correction does. The test moves to a less
symmetric molecule. On the cheap level, where we have ten such molecules, the corrected pairing still shows the network halving the error in the
heights, 0.13 against 0.30.

**Charged molecules need the exact route.** Some of the strongest interstellar infrared bands come from molecules that have lost an electron. We
compute vibrations either by nudging atoms and differencing, or from exact formulas. For charged molecules the nudging route turned out too
noisy for two groups of vibrations (errors of 3.0 and 2.3 cm⁻¹ where we allow 1.5), so all sixty charged molecules now get the exact route. That
costs a week of the laptop, not money.
