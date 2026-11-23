---
layout: post
title: "The same molecule, written down twice"
date: 2026-11-23 09:12:04 +0100
categories: plan05 method
---

<!-- Draft written 23 Nov 2026 on the user's word ("Ja, schrijf het concept"); published the same day on the user's word ("Publiceer de blog maar").
Sources, all in the plan repo (plans/05_delta-probed-ir-pipeline):
  - the audit of 19 Nov: probes/results_m1/frame_audit_2026-11-19/table.md (17 places; 9-methylanthracene mirror 9.95, listing 10.00,
    rotation 0.000, gauge part 10.08, value part 0.18; "ideal" 0.000 for every molecule; the record's largest spread without a linear
    group 2.85 against the line of 3.0)
  - the linear group: ledger 19 Nov 19:3x and probes/results_m1/linear_angle_rotation_2026-11-19/README.md (diphenylacetylene 11.20 cm⁻¹
    over eight rotations; with an axis taken from the molecule 1.5e-5; 947 molecules without a linear group unchanged)
  - what the product measures: aromir src/aromir/own_spread.py (8 rotations, mirror image, swaps, 4 listings; benzonitrile 1.75 with
    three rotations, 2.78 with eight and with twenty-four) and the session note of 19 Nov (benzene 0.88, benzonitrile 2.78,
    biphenyl 2.85, 9-methylanthracene 9.95, diphenylacetylene 13.54 cm⁻¹; "a sample, not a bound")
  - the earlier incidents: the posts of 2 Nov ("Six stations") and 4 Nov ("Which way is the molecule facing?")
  - network version v1.4; the repair is TASKS 287, one change to the inputs and one retraining, after the running search -->

A molecule does not know which way it is facing. It does not know which of its atoms we wrote down first, and its mirror
image has exactly the same vibrations. So a method that predicts those vibrations must give the same answer however the
molecule is handed to it. Last week we checked whether ours does. For many molecules the answer barely moves. For one of
them, the mirror image moved a predicted band by 10 cm⁻¹.

## What should not matter

Our project predicts the [infrared spectrum](/glossary/#infrared-spectrum) of soot-like molecules: the frequencies at
which a molecule absorbs light. A frequency is given as a [wavenumber](/glossary/#wavenumber), in units written cm⁻¹, and
our aim is to place each band within 3 cm⁻¹ of a more accurate calculation.

A [neural network](/glossary/#neural-network) does part of that work. A fast quantum calculation gives the stiffness of
the molecule, and the network predicts how that stiffness differs from the result of a slower, better calculation.

To a computer, a molecule is a list: atom 1 is a carbon at this position, atom 2 is a hydrogen at that position, and so
on. The same molecule can be written as many different lists. We can turn the molecule in space, which changes every
position. We can list the atoms in another order. We can write down its mirror image, which for the molecules in this
post is the same molecule again. None of these changes the physics, so none of them may change the spectrum.

## What we found

On 19 November we took molecules whose answer we already had and asked the network again, with the same molecule written
down differently each time.

Turning the molecule changed nothing for most of them: the largest band shift was 0.000 cm⁻¹. The mirror image and the
atom order were a different story. For 9-methylanthracene, three fused rings with one small side group, the mirror image
moved one band by 9.95 cm⁻¹, and another atom order moved a band by 10.00 cm⁻¹. For benzene the same test gave 0.08.

A second molecule failed in another way. Diphenylacetylene has two rings joined by a straight rod of four carbon atoms.
Turning this molecule in space, and nothing else, moved a band by up to 11.2 cm⁻¹.

Ten cm⁻¹ is more than three times the accuracy we claim. And 9-methylanthracene is not an exotic case: it is the kind of
molecule the method is meant for.

## Where the difference comes from

The network itself was not the cause. The network is an [equivariant network](/glossary/#equivariant-network): it is
built so that turning or mirroring the input turns or mirrors the output in exactly the same way. We tested that part
alone and it returned 0.000 cm⁻¹ for every molecule.

The cause sat in the numbers we prepare for the network before it starts. We describe a molecule not only by positions
but also by its bond lengths, its angles and its twists. Two kinds of those numbers turned out not to be fixed by the
molecule alone.

The first kind is a sign. A twist around a bond is clockwise or anticlockwise, and which of the two we call positive is a
convention. In the mirror image every clockwise twist becomes anticlockwise, so the sign flips. The sign can also depend
on which neighbouring atom happens to come first in the list. Our network read some numbers that carried such a sign.
For 9-methylanthracene we split the 9.95 cm⁻¹ into its two possible sources. The signs alone reproduce the effect, at
10.08 cm⁻¹; everything else together gives 0.18.

The second kind is a direction. A straight rod of atoms can bend in two directions, and to tell them apart one needs a
reference direction. The open-source library we use for these coordinates takes that reference from the axes of the
coordinate system, not from the molecule. Turn the molecule and the reference stays behind. When we took the reference
from the molecule instead, the 11.2 cm⁻¹ for diphenylacetylene dropped to 0.00002 cm⁻¹, and the 947 molecules in our set
without such a rod did not change at all.

## Why we had not seen it

We had tested this before. A record of seven molecules sat in the product, and the largest shift in that record, for
molecules without a straight rod, was 2.85 cm⁻¹: under the line of 3. On that record the product would have told a user
that the bands of 9-methylanthracene were accurate to 3 cm⁻¹, while they move by 10 when the molecule is written down
another way.

Seven molecules were too few, and none of them happened to have the combination that fails. The mistake was to read
"none of our seven shows it" as "it does not happen".

Direction and order had also caught us before. Earlier this month a quantum-chemistry program placed its grid of points
along axes of its own, and a fault of ours depended on the order of the atoms. Each time we repaired the one place where
it showed. This time we stopped repairing one place at a time and checked every place where a direction, a sign or an
order enters the method. There were seventeen such places, and the two causes above came out of that check.

## What the product does now

The repair is known but not made yet. The repair changes what the network reads, so the network has to be trained again,
and a search for the next version of the network is still running. Repair and retraining come after that search.

Until then the product measures the problem instead of assuming its absence. For every molecule it is given, it asks the
network again with the molecule turned eight ways, mirrored, and listed in other atom orders. No new quantum calculation
is needed for that. The largest shift of each band is its spread, and a band whose spread is above 3 cm⁻¹ is printed
without the accuracy claim, with the reason beside it.

Some results of that measurement: benzene 0.88 cm⁻¹, benzonitrile 2.78, biphenyl 2.85, 9-methylanthracene 9.95,
diphenylacetylene 13.54. The first three keep their claim, the last two lose it.

The number of turns matters too. With three turns benzonitrile showed a spread of 1.75 cm⁻¹; with eight it showed 2.78,
and with twenty-four still 2.78. So eight turns is what the product uses. Eight turns are still a sample: a spread found
this way can be too small, never too large.

A method earns trust by the questions it can answer about itself. "Would you say the same if I had written this molecule
down differently?" is one of the simplest. For some molecules the honest answer today is: not yet, and here is by how
much.
