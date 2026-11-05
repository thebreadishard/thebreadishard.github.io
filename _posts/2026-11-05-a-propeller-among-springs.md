---
layout: post
title: "A propeller among springs"
date: 2026-11-05 08:00:00 +0100
categories: plan05 anharmonic
---

<!-- Written 4 Nov 2026 at the user's request ("Schrijf het concept van de methylpost maar"; TASKS 181 (2)); published 5 Nov on the
     user's word ("Publiceer morgenochtend"). Numbers trace to
     (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     ledger 3 Nov 20:4x / 21:5x (TASKS 156 (c), probes/results_m1/gridcheck_methyl_2026-11-03/read.md): imidazole+CH3 route disagreement
       5.63 cm⁻¹ at 75/302, 5.26 at 99/590, share 0.93 (LARGE AMPLITUDE: ≥ 3/4; GRID NOISE: ≤ 1/2; styrene 0.19); the torsion's pairs with the
       methyl C–H stretches stay, its pairs with ring modes fall; torsion φ_iiii ≈ 19,600 cm⁻¹; decoupling moves the methyl bends at 1508 and
       1521 cm⁻¹ by up to 5.9;
     literature report 4 Nov (reports/Methylrotor in anharmonische IR.md): Mendolicchio, Bloino & Barone, JCTC 2022 (acetic acid: curvilinear
       couplings 10–100-fold smaller, CH3 fundamentals move 0–4 cm⁻¹);
     ledger 4 Nov 07:4x (TASKS 173, Psience 1.6.1): decoupling moves mode 20 by 6.50 (Cartesian) / 7.46 (Z-matrix internals), mode 21 3.29 / 4.43;
     ledger 4 Nov 10:3x (TASKS 177, scan_read.json): relaxed scan 13 points, V3 = 227.6 cm⁻¹, F = 5.65, 1D fundamental 91.3 (VPT2 94–97,
       harmonic 108.0); microwave V3 = 317.20(14) cm⁻¹, Gougoula, Medcraft, Heitkämper & Walker, J. Chem. Phys. 151, 144301 (2019); −28 %;
     PreRegistration_2026-11-04_Methyl_Rotor_Variational.md (part B, amendments 1–2, outcomes; commits 4b598861, a294398c, 792304b2):
       24 × 11 × 11 = 2,904 points, 12-point subset moves Δ_var by 0.97 > 0.3; 48 × 11 × 11 = 5,808 points, 2,904 reused, run 17:13–20:39;
       Δ_var +1.40 / +0.10 at 48, +1.37 / +0.10 on the 24-point subset, convergence 0.033; line (iii); limits as stated;
     Design_2026-10-13_Spectral_Shape_Anharmonic_Step.md §8 (commit 9e0387ee): the policy. -->

Yesterday's post was about axes. This one is about a small part of a molecule that refuses to stay put: the methyl group. It took two
days, five calculations and rules written down in advance before we knew how to treat it. This post explains what a methyl group does,
why our usual method struggles with it, and what we decided.

## A propeller on a ring

Many of the molecules in this project are flat rings of carbon atoms. Sometimes one hydrogen on the ring is replaced by a **methyl group**:
one carbon atom carrying three hydrogen atoms, written CH₃. Seen from the ring, the methyl group looks like a small propeller with three
blades.

That propeller can turn. As it turns, each hydrogen passes close to the atoms next to it on the ring, which pushes back a little. So the
propeller has three comfortable positions per full turn, with a small hill between each pair of them. The height of that hill is called
the **barrier**. A methyl group with a high barrier mostly rocks back and forth in one valley. A methyl group with almost no barrier spins
nearly freely.

Our test molecule is 4-methylimidazole, a five-membered ring with two nitrogen atoms and one methyl group. Its barrier is low compared with
a chemical bond, but high compared with the energy needed to set the propeller rocking. In the language of the field it is a *hindered*
rotor: it rocks, and only occasionally hops over a hill.

## Why a propeller is hard for our method

To predict the positions of the bands in an [infrared spectrum](/glossary/#infrared-spectrum), we treat every vibration as a spring.
Each [normal mode](/glossary/#normal-mode) is one pattern of motion in which all atoms move together. The first guess, the harmonic one,
treats each spring as ideal. Real springs are not ideal: they stiffen or soften as they stretch, and they pull on each other. That is
[anharmonicity](/glossary/#anharmonicity), and it shifts each band by a few to tens of [wavenumbers](/glossary/#wavenumber) (cm⁻¹, the
unit of the spectrum).

Our method for these shifts is [VPT2](/glossary/#vpt2). It starts from the springs and adds small corrections. Its central assumption is
that every atom makes small motions around one resting position. For a ring that holds well. For the propeller it does not: turning the
methyl group is a large motion, around a circle, with three resting positions instead of one.

The propeller also touches other vibrations. The three hydrogens can fold together and apart like an umbrella; these are the **CH₃ bends**,
two bands near 1508 and 1521 cm⁻¹, a wavelength of about 6.6 micrometres, inside the part of the spectrum we predict. When the propeller
turns, those bends change a little. VPT2 sees this as a **coupling** between the turning and the bending, and it shifts the bends for it.
The question of this post is whether that shift is real.

## The first sign: two routes disagree

We compute some of VPT2's numbers twice, by two independent routes. If both routes give the same number, it is probably right. If they
disagree, something in the calculation is noisy. Two of the molecules in our study of these shifts, both with a methyl group, had a disagreement of 5.6 and
7.2 cm⁻¹, far above what we accept.

A disagreement like that has two possible causes. The first is numerical noise from the
[integration grid](/glossary/#integration-grid), the set of points on which the program evaluates its formulas. A finer grid removes that
kind of noise. The second is that the motion is too large for the method. A finer grid does not help then.

On 3 November we repeated the calculation for 4-methylimidazole on a finer grid. We had written down beforehand how to tell the two causes
apart: if the disagreement fell to half or less, it was grid noise; if three quarters or more remained, it was the large motion. For a
similar case in October, a vinyl group on styrene, only 0.19 of the disagreement had remained: grid noise. For the methyl group 0.93
remained. The worst pairs were all the propeller with the methyl group's own C–H bonds. The verdict: the propeller's large motion, not
the grid.

That left a practical question. A common remedy in the literature is to take the turning motion out of VPT2 altogether, so that it cannot
shift anything else. Doing that moved the CH₃ bends by up to 5.9 cm⁻¹. That is about twice the accuracy we promise per family of bands
(3 cm⁻¹). So the choice mattered, and we did not know which choice was right.

## Two coordinates, one answer

A literature search on 4 November gave a reason to doubt the remedy. Atomic motion can be described in two ways. One way uses straight-line
displacements of every atom along x, y and z. The other uses bond lengths, bond angles and twist angles. A turning propeller is naturally
an angle: in straight lines, its hydrogens would leave their circle. A 2022 study on acetic acid (Mendolicchio, Bloino and Barone) found
that in angle-based coordinates the couplings of a methyl group become 10 to 100 times smaller, while the bands themselves move by
only 0 to 4 cm⁻¹. That suggested the large couplings might be an artefact of straight lines that cancels in the full result.

We tested this with a second, independent program, Psience. We took the turning motion out of VPT2 in both kinds of coordinates. The
bends moved by 6.5 cm⁻¹ in straight-line coordinates and by 7.5 cm⁻¹ in angle coordinates. Both descriptions said the same thing, so this
test did not decide. (The same comparison also exposed an error in our own code, the subject of yesterday's post.)

## Checking the hill

Next we measured the barrier itself. We turned the propeller in 13 steps through a third of a turn and let the rest of the molecule relax
at each step. Our level of theory, B3LYP, puts the barrier at 228 cm⁻¹. A microwave measurement, which reads a molecule's rotation very
precisely, gives 317 cm⁻¹ (Gougoula and co-workers, 2019). So our method makes the hill 28 % too low. A real molecule is therefore more
hindered than our model of it. The propeller's own rocking frequency follows from the same scan: 91 cm⁻¹, far below the window of the
spectrum we predict.

## Solving it without the shortcut

VPT2 is a shortcut: it replaces the exact problem by springs plus small corrections. For three motions only, the propeller and the two
bends, we can avoid the shortcut and solve the problem directly. This is called a **variational** calculation.

It works like this. We place the atoms at many combinations of three settings: the propeller's angle, and how far each of the two bends
is folded. At every combination we compute the energy. Together those energies form a map of the landscape the atoms move in. Then we solve
the quantum mechanics of the three motions on that map, without assuming small motions. To isolate the coupling, we solve it twice: once
on the real map, and once on a map from which the link between turning and bending has been removed. The difference in the bend's band is
the true effect of the coupling. Then we compare it with what VPT2 says.

Before computing anything, we wrote down three rules:

1. **Converged.** The answer must not change by more than 0.3 cm⁻¹ when we use half as many propeller angles. Otherwise the map is too
   coarse, and there is no verdict.
2. **VPT2 is right** if the variational shift and VPT2's shift agree within 1.5 cm⁻¹ for both bends.
3. **VPT2's coupling is not real** if the variational shift is at most 1.5 cm⁻¹ while VPT2's is 3 cm⁻¹ or more.

The first map had 24 propeller angles, 15 degrees apart, and 11 settings for each bend: 2,904 energies. It was not converged. Halving the
angles moved the answer by 0.97 cm⁻¹, more than three times the allowed 0.3. So there was no verdict.

We then doubled the angles to 48, 7.5 degrees apart. Every second angle of the new map was an angle of the old one, so we reused those
2,904 energies and computed 2,904 new ones, about three and a half hours on the laptop. This time the map was converged: halving the
angles moved the answer by 0.03 cm⁻¹.

| | Variational | VPT2, straight lines | VPT2, angles |
|---|---|---|---|
| Bend near 1508 cm⁻¹ | +1.4 | +6.5 | +7.5 |
| Bend near 1521 cm⁻¹ | +0.1 | +3.3 | +4.4 |

*The shift of each CH₃ bend caused by its coupling to the turning propeller, in cm⁻¹.*

Rule 2 fails: 1.4 and 6.5 are far apart. Rule 3 holds: 1.4 is under 1.5, and both VPT2 values are above 3. So by the rule written in
advance, the coupling VPT2 puts between turning and bending is not really there for this molecule.

## How firm is that?

We wrote three limits down with the verdict.

- **The margin is small.** 1.4 lies only 0.1 under the line of 1.5. The convergence check moved the answer by 0.03, so the verdict stands,
  but not by much.
- **The map holds only the direct link.** It moves the propeller and the two bends. VPT2 also lets the propeller influence the bends
  indirectly, through all the other vibrations of the molecule. That indirect part was not tested.
- **One molecule, one method.** Everything rests on 4-methylimidazole at the B3LYP level, whose barrier is too low. A real methyl group
  here is more hindered, which points the same way, but we have not tested other molecules.

## What we decided

The project now has a rule for hindered methyl groups. The CH₃ bends get their anharmonic shift from VPT2 with the
turning motion taken out. The propeller's own rocking band comes from the scan, not from VPT2. The methyl group's C–H stretches, near
3 micrometres, lie outside the window we predict and stay as they are.

The rule says how to compute a methyl band. It does not yet say how far such a band can be trusted. The certificate that comes with every
predicted spectrum will keep marking methyl bands as outside the measured accuracy, until a test on more than one molecule, again
registered in advance, gives them an error bar of their own. A nearly free methyl group, like the one on toluene, has an even lower hill
and is not covered by the rule at all.

The code for these calculations was written and tested with an AI assistant, Claude, under my direction. The variational read is in
commit [792304b2](https://github.com/thebreadishard/udacity-capstone-plan/commit/792304b2), and the rule in
[9e0387ee](https://github.com/thebreadishard/udacity-capstone-plan/commit/9e0387ee).
