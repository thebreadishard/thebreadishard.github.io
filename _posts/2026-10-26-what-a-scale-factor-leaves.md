---
layout: post
title: "What a scale factor leaves"
date: 2026-10-26 06:07:26 +0100
categories: plan05 progress
---

<!-- Written 25-26 Oct 2026 (the user: 'Overweeg een blog voor te bereiden over de resultaten van 111 en/of 112'); published 26 Oct on the
     user's word ('Publiceer'; TASKS 128).
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/PreRegistration_2026-10-25_Scale_Factor_Residuals.md (commit 08c594fe, written before any number);
     GoalGathering/notes/Results_2026-10-25_Scale_Factor_Residuals.md;
     modules/05_support_predictor/out/scale_factor_baseline_2026-10-25.md (commit d4b48582): pool 1,073 molecules; (a) 10 molecules, (b) 39;
       (a) network 1.25 / 2.36 / 1.82 / 3.35, three-region factor 2.02 / 9.21 / 15.47 / 8.58, per-family factor 2.04 / 6.61 / 15.31 / 8.46;
       (b) network 2.57 / 3.81 / 2.62 / 6.03, three-region factor 3.86 / 9.96 / 14.19 / 14.63 (CH-stretch / CH-oop / ring-ip / other);
     probes/results_m1/scale_factor_baseline_second_route_2026-10-25.log (commit b452ef0b): ring-ip mean shift +11.61, sd 15.97 on (a);
       C-H stretch +43.59, sd 1.47;
     modules/05_support_predictor/out/fundamentals_vs_experiment_2026-10-25.md (commit e08fddc7): benzene CC harmonic - experimental
       harmonic ring-ip +2.1 / 5.5, CH-stretch -38.5; Mai scaled - fundamental rms 22.5 (benzene), 15.4 (naphthalene), benzene ring-ip
       mean -24.6; CC harmonic - fundamental CH-stretch +90.0 (benzene), +95.7 (naphthalene);
     probes/results_m1/cc_ch_bond_force_2026-10-25.log: C-H 1.0871 A at the B3LYP geometry, CC force inwards, first-order dr -0.0040 A. -->

When you compute the vibrations of a molecule with a standard quantum-chemistry method, the frequencies come out a few per cent too high.
The usual repair is simple: multiply every computed frequency by one number, a *scale factor*, slightly below one. The NASA Ames PAH
database uses three such numbers, one for each region of the spectrum (0.956, 0.952 and 0.960). The largest infrared library of PAHs to
date, 144,111 spectra published this year by Mai, Lu and Wang, uses exactly those three numbers.

This project tries to do without the scale factor. Instead of stretching the whole spectrum by a fixed amount, a neural network learns how
each vibration of each molecule should move. That only makes sense if a scale factor actually leaves something behind. This week we
measured how much, and where.

Before we looked at any number, we wrote down what we would compute and what would count as a pass. That note is in the repository with a
date and a commit ([the registration](https://github.com/thebreadishard/udacity-capstone-plan/commit/08c594fe)), so the results below could not be bent to fit afterwards.

## The practice task

Our network learns in two stages. In the first stage it practises on a stand-in: the difference between two cheaper methods (called
B3LYP and ωB97X), which we can compute for hundreds of molecules. The real target, [coupled cluster](/glossary/#coupled-cluster), is too
expensive for that. I wrote about the two stages [last week](/2026/10/22/rehearsing-the-last-step/).

On the stand-in, the question is easy to make fair. We fitted the best scale factors we could to 1,073 molecules — more than the network
ever sees in one training — and asked how far each vibration still ends up from where the second method puts it. Then we asked the same of
the network, on molecules neither had seen: ten held back from training, and 39 built on ring frameworks the network had never met.

We split the vibrations into four kinds:

- **C–H stretches**: a hydrogen atom bouncing in and out along its bond;
- **C–H out-of-plane bends**: the hydrogens flapping up and down through the plane of the rings;
- **ring vibrations in the plane**: the carbon framework itself stretching and bending;
- **everything else**: mostly slow, floppy motions of the whole molecule.

The typical error left over (a root-mean-square average over all vibrations of that kind), in wavenumbers, the unit spectroscopists use:

| | C–H stretch | out-of-plane bend | ring, in plane | other |
|---|---|---|---|---|
| best three-region scale factor | 2.0 | 9.2 | 15.5 | 8.6 |
| best scale factor *per kind of vibration* | 2.0 | 6.6 | 15.3 | 8.5 |
| our network | **1.3** | **2.4** | **1.8** | **3.4** |

On the 39 molecules with unfamiliar frameworks the network's errors are larger (2.6, 3.8, 2.6 and 6.0), but it still removes between a
third and more than four fifths (33 to 82 per cent) of what the scale factor leaves ([the read](https://github.com/thebreadishard/udacity-capstone-plan/commit/d4b48582)).

The ring vibrations are the striking column. A scale factor leaves about 15 wavenumbers there, and even a separate factor for every kind of
vibration does not help. The reason shows up when you look at how the ring vibrations actually move between the two methods: on average they
go up by about 12 wavenumbers, but from one vibration to the next that varies by 16. A single number cannot follow that. The network can,
because it looks at each vibration and its surroundings in the molecule.

For the C–H stretches it is the other way round. They all move by almost the same amount (about 44, give or take 1.5), so a scale factor
does nearly as well as the network. That is no surprise: it is the case scale factors were made for.

## Against experiment

The stand-in is a rehearsal. The real test is measured spectra, so we took the two molecules for which we have both a coupled-cluster
calculation and good measurements of every vibration: benzene and naphthalene. Three things came out.

**Our reference calculation is good where it should be.** For benzene, spectroscopists have worked back from the measured spectrum to the
frequencies the vibrations would have if the bonds were perfect springs. Against those, our coupled-cluster calculation is typically 5.5
wavenumbers off (root mean square) for the ring vibrations. The C–H stretches, however, come out 38 too low. The likely cause: we compute the
coupled-cluster vibrations at the shape the cheaper method gives the molecule, and its C–H bonds are slightly
longer than coupled cluster would make them: the forces in our own calculation pull every hydrogen about 0.004 ångström closer to its carbon ([the check](https://github.com/thebreadishard/udacity-capstone-plan/commit/70e9010b)).
A longer bond vibrates more slowly. We saw the same offset in an earlier check, so it looks systematic, and the final pipeline will have to correct for it.

**Scaled spectra are decent, with offsets.** Mai, Lu and Wang's scaled spectra land typically 22 wavenumbers from the measured bands of benzene and
15 from those of naphthalene. For benzene the ring vibrations are on average about 25 too low: an offset of one kind of vibration,
the kind a single number cannot remove.

**Real bonds are not perfect springs.** Our coupled-cluster frequencies are *further* from the measured bands than the scaled ones: the C–H
stretches by about 90 wavenumbers. That is not an error in the calculation. Real bonds get softer the more they stretch (anharmonicity), so
real vibrations sit lower than perfect-spring ones. A scale factor quietly folds this effect into its one number, together with everything
else. We want to keep the pieces apart, so we add it as a separate step, which we are building alongside the network ([the comparison](https://github.com/thebreadishard/udacity-capstone-plan/commit/e08fddc7)).

## What this means

On the practice task the network does clearly better than any scale factor, in every kind of vibration, and most of all for the ring
vibrations, which carry much of the structure of a PAH spectrum. Against experiment the picture is more modest, and more useful: a
coupled-cluster correction alone does not beat a scale factor; it needs its companion step for anharmonicity, and a fix for the C–H
offset that comes from the molecule's shape.

Two caveats. The first part is the stand-in, not experiment. And the second part rests on two molecules; it shows where the differences are,
not how large they will be across all PAHs.

The full numbers, the scripts and the note written beforehand are in the project's repository.
