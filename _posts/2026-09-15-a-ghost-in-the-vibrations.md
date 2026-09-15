---
layout: post
title: "A ghost in the vibrations: how a one-in-ten-thousand impurity nearly cost us 11 cm⁻¹"
date: 2026-09-15 20:45:00 +0200
categories: plan05 probes
---

Yesterday's long calculation on naphthalene finished at a quarter to two in the afternoon. It had run for twenty
hours, one energy every seventy minutes, and the number we wanted from it — how much cheaper a smaller basis set
makes each energy — came out cleanly: ten times cheaper, with a tenth of the memory. That was the plan. This post is
about something we did not plan: a small, silent error that the same run exposed in passing, what it turned out to
be, and how it was fixed before it could touch a single real measurement.

## What we measure, and why symmetry matters

The pipeline moves a molecule a little way along one of its vibrations, computes the energy, moves it the same
distance the other way, computes again, and reads the *curvature* from the pair: half the sum of the two energies,
minus the energy at rest. The curvature is what sets the vibration's frequency. A pair of displacements is used
rather than one because a single displacement also picks up the *slope* of the energy surface — the force — and at a
resting molecule the force should be zero but never quite is.

Symmetry offers a shortcut. Naphthalene is a flat molecule with three mirror planes. Most of its vibrations are
"antisymmetric" under at least one of them: displace the molecule along such a vibration, mirror it, and you get the
displacement in the opposite direction. For those vibrations the energy at +q must equal the energy at −q exactly,
by symmetry, and the force along them is zero exactly. So one energy per displacement would do instead of two,
and the deck of measurements shrinks by almost forty percent. We had adopted that shortcut as a rule on Sunday,
with one condition: it had to be confirmed on naphthalene before it was used.

## The ghost

The confirmation was supposed to be a formality. Instead, when the first vibration of the twenty-hour run was
complete, the two energies at +q and −q were *not* equal. The difference — the "odd part" — was small on the scale
of the curvature, but it was there: about 23 millionths of a hartree in the mean-field energy at full displacement,
and about 57 millionths in the correlation energy, of opposite sign. Worse, the difference grew in proportion to
the displacement: halve the step, halve the difference. That is exactly the signature of a force, not of noise.
Along a vibration where the force must vanish by symmetry.

For this molecule the run was unaffected — it used pairs, and the odd part cancels in a pair. But had we used the
shortcut, the force would have gone straight into the curvature. Translated into frequency, the error on that
vibration would have been about 11 cm⁻¹. The entire project is about corrections of a few cm⁻¹.

## Finding it

Two things could break the symmetry: the geometry of the molecule, or the description of the vibration itself. We
measured both.

The geometry came from a routine optimisation and was symmetric to seventy-five millionths of a bohr — small, but
not zero. The vibrations were the more interesting suspect. A quantum-chemistry program computes them by
diagonalising a matrix of second derivatives, and if that matrix is computed without telling the program about the
molecule's symmetry (the standard, fastest setting), the resulting vibrations are not perfectly symmetric either:
each one carries a trace of vibrations of the wrong symmetry type. We wrote a short script that projects every
vibration onto the symmetry types of the molecule and reports how pure it is. The vibration with the ghost was
99.976 percent pure — an impurity of 2.4 in ten thousand. Innocent-looking. But the leak was into the fully
symmetric type, the one type along which a force *is* allowed, and the correlated-electron energy has a sizeable
force there because the geometry was optimised with a cheaper method. Multiply a permitted force by a
one-in-ten-thousand leak and you get tens of millionths of a hartree. The arithmetic fit.

Better than fitting: the script gave a prediction. A second vibration in the same run was two hundred times purer
(1.4 in a million), and its odd part had come out fifteen to thirty times smaller — in proportion to the square root
of the impurity, as a leaked force should scale. A third vibration, still running at the time, was about as impure as
the first (2.1 in ten thousand). So at eleven in the morning, with three of its five points still to come, we wrote
down in the log: this vibration's odd part will be of the same order as the first one's.

## The prediction failed, and the mechanism held

At a quarter to two the third vibration was in. The odd part of the mean-field energy: 37 millionths, linear in the
step. The odd part of the correlation energy: −36 millionths, linear in the step. Both exactly the size the
impurity predicted. And their sum — the quantity we had actually written the prediction about — was one millionth,
because at this vibration the two forces happened to be equal and opposite.

We record that as a failed prediction, because it was one: the sentence in the log named the total, and the total
did not do what the sentence said. The lesson is about where to place a prediction. A mechanism that says "each
energy component picks up a force proportional to the leak" makes its claim about the components, and there it was
right on all three vibrations; the sign with which two components add is not something a purity number can know.
Next time the registration goes on the parts.

## Laying the ghost

The fix has two halves, and both are now part of the pipeline's first stage. First, the geometry is averaged over
all eight symmetry operations of the molecule, which takes it from seventy-five millionths of a bohr off symmetry to
four parts in ten thousand million million — the rounding of the arithmetic. Second, every vibration is projected
onto its symmetry type before anything is measured along it, so that the impurity is gone rather than small.

Then we tested it the cheap way, with density-functional energies that take twelve seconds each instead of seventy
minutes: the same three vibrations, the old vibrations against the new ones, two functionals. With the old
vibrations, one of the two functionals showed odd parts of 31, 1 and 14 millionths of a hartree — the ghost again,
now visible for a few euros of electricity. With the symmetrised geometry and projected vibrations, every odd part on
every vibration in both functionals was one thousandth of a millionth or less. The curvatures themselves were
unchanged to a twentieth of a percent, which is the point: the shortcut was never wrong in principle, it was waiting
for clean inputs.

## What it changes

The forty-percent shortcut is now licensed, on the condition that was met yesterday, and the naphthalene deck is
counted at 291 energies instead of 474. More importantly, a class of error that no accuracy check on the final
frequencies would have separated from a genuine correction has been measured, explained and removed at the
stage where it enters. It cost one morning and no extra computation: the twenty-hour run was going to happen anyway,
and everything else was arithmetic on its output.

## Where the numbers come from

The odd and even parts of the naphthalene run (`m3_even_odd_parts.py`, commit 791dfe6; the full reading, commit
87a7580); the purity of the vibrations and the pre-registered prediction (`factory_mode_purity.py`, commit 713fe01);
the symmetrised geometry (`symmetrise_geometry.py`, commit 90bbf7a); the projection in the pipeline's first stage
(`dryrun_dft_delta_recovery.py --symmetrised`, commit 713fe01); the cheap before/after test (`i14_odd_part_dft.py`,
commits 34f8a67 and 8016433). The naphthalene coupled-cluster energies themselves stay sealed; only differences
between them are printed, as with benzene.