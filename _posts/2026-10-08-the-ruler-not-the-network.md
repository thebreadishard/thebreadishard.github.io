---
layout: post
title: "The ruler, not the network"
date: 2026-10-08 17:15:00 +0200
categories: plan05 progress
---

<!-- Published 8 Oct 2026 on the user's word ("Publiceer maar").
     Numbers trace to: modules/05_support_predictor/out/composite_test2_benzene_2026-10-06.md (de41a5b),
     GoalGathering/notes/Design_2026-10-04_Composite_Anchor_Level_DZ_TZ.md (test 1, MP2 against B3LYP tracking),
     out/composite_test3_anthracene_2026-10-07.md (2a1c84b), the rung C pre-registration outcome of chain 33c (9d38944),
     chain 37's read (dc81721), the LNO follow-up outcome (3eb8c77). -->

For two weeks the network had one weak spot we could not talk away. It learned the in-plane vibrations of an unseen molecule well, and the
out-of-plane ones badly: errors of 18 to 35 cm⁻¹ where we want 3. This week we found out why, and it was not the network.

**What an anchor is.** The network is trained on cheap calculations (density functional theory, DFT) for about a thousand molecules. A handful
of molecules are also computed with CCSD(T), the method chemists use as the reference when nothing better is affordable. Those are the anchors:
the network is tuned on them and tested on the one it has not seen. That test is where the out-of-plane error sat.

**The ruler was short.** A CCSD(T) calculation is only as good as the set of functions it is allowed to describe the electrons with, the basis
set. Ours was the small one, cc-pVDZ, because the larger one costs far more. For benzene we paid for the larger one, cc-pVTZ, once,
and compared. Measured against it, the small-basis CCSD(T) frequencies were off by 25 cm⁻¹ (root mean square) for the ring vibrations, 138 for the
C–H stretches, 70 for the out-of-plane C–H bends and 41 for the rest. The cheap DFT we were trying to correct was off by 14, 39, 15 and 13. In every
family the reference was further from the truth than the thing it was supposed to correct.

**A cheaper way to the larger basis.** Chemists have handled this for decades by splitting the work: do the expensive method in the small basis,
and add the change from small to large basis computed with a cheaper method that changes in the same way. The question is which cheaper method
follows CCSD(T) when the basis grows. We tested two on three displacements of benzene. MP2, the simplest correlated method, moved 0.89 to 0.97 as
much as CCSD(T) did; B3LYP moved 0.28 to 0.73 as much. So the anchors became CCSD(T)/cc-pVDZ plus the MP2 step to cc-pVTZ. For benzene that
combination sits within 3.3, 4.4, 7.3 and 3.3 cm⁻¹ of the full large-basis CCSD(T) answer in the four families, and removes 94 per cent of the
small-basis error. The MP2 step for the five other anchors took about a day on one rented server.

**Two molecules that were not flat.** Anthracene's small-basis CCSD(T) calculation said the molecule is not at a minimum: one out-of-plane
vibration came out imaginary (51i cm⁻¹) and the next at 7 cm⁻¹. That is a known failure of correlated methods in small basis sets, which can make
flat aromatic molecules look bent. The basis-set step repaired it: 86 and 115 cm⁻¹, against 105 and 126 from DFT. Benzonitrile had the same
problem and the same repair.

**The network, measured again.** With every anchor on the same, larger-basis level, we repeated the test that had failed. The out-of-plane error
fell on all four molecules we could compare: naphthalene 34.6 → 21.5 cm⁻¹, pyridine 24.7 → 12.0, benzene 18.8 → 11.6, fluorobenzene 18.1 → 10.4.
That is still above 3, so the work is not finished, but most of what we had read as the network's weakness was the reference it was measured
against.

**Two smaller things this week.**

*Neighbours by size are not neighbours.* The last post said that the children of a related family help, but do not replace a family's own
children. We have now tested the reverse. Two hundred new molecules went into training, among them twenty-four children of pyrene, a four-ring
molecule. The prediction was that they would help the unseen fluorene and fluoranthene families. They did not: the error on those families went
from 0.330 to 0.325 of the no-correction baseline, where we had written down that a real effect would be at least 0.03. Fluorene and
fluoranthene both contain a five-membered ring; pyrene does not. That is an observation, not yet a result.

*A cheaper reference for larger molecules.* Full CCSD(T) becomes unaffordable beyond about 26 atoms. A local variant, which treats distant
electron pairs more cheaply, reproduced the curvatures of naphthalene to within 0.3 per cent (about 1 cm⁻¹ in a frequency) in all three
directions we tested. That keeps a route open to anchors the size of the molecules we eventually want to predict.
