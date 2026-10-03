---
layout: post
title: "Breadth first: what thirty molecules buy, and what they do not"
date: 2026-10-03 19:46:05 +0200
categories: plan05 progress
---

<!-- Published 3 Oct 2026 on the user's word ("Laat de zin staan en publiceer de blog").
     Numbers trace to: chains 28–31 (pre-registration outcome 3 Oct), chain 24 (E7_rungC_carried_kd_750_saved_2026-10-02),
     probes/rungC_family_floor_ceiling.py (out/…_2026-10-03.md), benzene CC APT (results_m1/e8_benzene_ccpvdz_dip_2026-10-03), pyscf/pyscf#3477,
     the LNO curvature check (results_m1/e8_naphthalene_ccpvdz_tlambda_2026-10-02/lno_curvature_check_2026-10-03.json). -->

Last night the network answered a question we had been asking the wrong way. We had been counting molecules. The right count is families.

**The experiment.** The training pool holds about seven hundred and fifty molecules: benzenes, naphthalenes, pyridines, a few hundred fluorenes, and
a hundred and thirty molecules with three or more fused rings. We asked what those hundred and thirty were worth by taking them out and training
again on what remained. On the ten unseen test molecules the error went from 0.23 to 0.37 of the no-correction baseline; on the three-ring molecules
among them it went from 0.15 to 0.50. A control pool of the same size with the three-ring molecules kept in scored like the full pool, so it was the
family that mattered, not the number. Then we put them back in steps: none, thirty-one, sixty-two, all hundred and twenty-three children of the
three-ring parents. The error on those parents fell 0.45, 0.22, 0.17, 0.15.

**What the curve says.** The first thirty children of a family buy three quarters of everything that family will ever give. The second thirty buy most
of the rest. Beyond sixty the curve is flat, and what remains is the model's own floor, which no amount of data of that kind will move. Neighbours
help but do not replace: a three-ring molecule whose own children were missing still got two thirds to five sixths of the benefit from the children
of its neighbours (phenanthrene read 0.20 without its own children, phenanthridine 0.27). Families with a nitrogen in the ring leaned harder on
their own children than the pure hydrocarbons did. And seven molecules of a family, the number of pyrenes we had, carried nothing at all.

**So thirty is enough?** No. Thirty is where coverage starts to pay, sixty is where it stops paying. Whether that is enough depends on what we want
from the family. For a family we only need to recognise, thirty children. For the families we want to be good at, sixty. After that the money goes
to the next family, not to the hundredth naphthalene. That is now a written rule in the plan, and it is also a price list: a molecule from a family we
have never computed is quoted as "its family first", thirty molecules at the cheap level, before anything is promised about it.

**The morning's correction to ourselves.** The target we had set for this network was an average over all vibrations: under three wavenumbers, and
we had met it. Averages hide things. Split by the kind of motion, the ring vibrations stood at 2.2 to 2.5 wavenumbers, the C–H stretches at 1.3 to
1.9, the out-of-plane C–H bends at 2.4 to 3.2, and the skeletal and substituent motions at 3.6 to 4.7. Two of four families were not under the line.
The person running this project wants the network to learn *all* the relevant physics, so the target is now per family, and it is open again.

Before training anything new we measured two things. First, how well the network's output can represent each family at all: better than 0.6
wavenumbers everywhere, so the model's reach is not the problem. Second, how noisy our training targets are per family. The cheap reference
calculations we train on come from finite differences, and their noise is not the same for every motion: 0.2 wavenumbers for the C–H stretches,
1.5 for the ring and skeletal motions, and 3.3 for the out-of-plane bends. That last number sits exactly on our target line. For the out-of-plane
family the honest next step is not a cleverer loss function but cleaner targets, computed analytically, which we have queued. For the skeletal
family, whose floor is 1.5 and whose error is 3.6 to 4.7, the gap is a learning gap, and a change to the loss is registered with its lines before it
runs. Measure the floor and the ceiling first, then decide what to build. We wish we had done it a week earlier.

**The second term.** A spectrum has two halves: where the lines are and how bright they are. Everything above was about where. On brightness we
had been using the cheap method's answer unchanged, with no idea how far it was from the expensive one. Yesterday we computed, for the first time,
the expensive brightness for benzene, at coupled-cluster level, as a by-product of the forces we were already computing. The correction turned out
to be real and uneven: the C–H stretches lose about a quarter of their brightness, the out-of-plane bends gain a tenth, the ring modes hardly move.
Weighted by how much each line matters, the cheap brightness is off by about a fifth. The overall shape of the spectrum, as a number between zero
and one, scored 0.98 against the expensive one, so the picture is right and the details are not. That is the same pattern as the line positions,
which is encouraging: it suggests one network can learn both halves.

The trick that made this cheap is worth a sentence. The quantum chemistry code already builds, inside its force calculation, exactly the electron
density one needs for the brightness, and then throws it away. We kept it, and sent the change upstream to the pyscf project as a pull request. The
first automated test run failed, not on the physics but on test order: an older test in the same file moves an atom and leaves it moved. Our tests now
build their own molecule. That is the kind of mistake you only find by running the whole file, which we had not.

**The cost of an anchor.** The expensive reference calculations that anchor all of this scale steeply with molecule size, and we have long hoped that
a cheaper "local" variant of the method, which treats distant electron pairs approximately, could give us anchors for molecules of pyrene and
coronene size. Today we measured, for the first time, whether the local method reproduces the quantity we actually need: the curvature of the energy
surface, which is what a vibrational frequency is. On two coordinates of naphthalene, one in the molecular plane and one out of it, the local
method's curvatures came out 0.29 and 0.27 percent above the exact ones. That sounds small. It is not: the two exact routes we compare against each
other agree to a few parts per million, so the deviation is four hundred times our measurement noise, and it is the same size and sign on both
coordinates, which means it is a property of the approximation, not scatter. In frequencies it would be a shift of one to four wavenumbers,
systematic, across the spectrum. The rule we wrote down before the run said: above a tenth of a percent in curvature, the approximation itself is the
problem, not the noise, and the anchors stay exact. So they do. Larger anchors will be computed exactly, on bigger machines, and the request we are
preparing for university computing time asks for exactly that. One follow-up could still change the picture, with tighter thresholds and the
localisation held fixed between geometries; it is written down and waits for a free machine.

**What comes next.** The two hundred molecules now computing close the four-ring gap. After them the pool lacks three *axes*, not families: charge
(we have no cations, and cations carry some of the strongest interstellar bands), nitrogen inside large ring systems, and size beyond four rings.
The next pool is sixty radical cations on scaffolds the network already knows, thirty aza-four-rings and thirty five-ring molecules, each new family
with a few parents held out so that it is read against its own children count. The questions, predictions and decision lines are written down
before a single one is computed. We have learned, this week more than once, that this is cheaper than the alternative.
