---
layout: post
title: "Two roads to the same number"
date: 2026-09-21 17:50:00 +0200
categories: plan05 probes software
---

Yesterday evening a computer in Helsinki finished four and a half hours of work on benzene and handed back a
spectrum that could not be right. One vibration, the breathing of the ring, came out 217 wavenumbers below its
harmonic value, where twenty would have been normal. Pairs of vibrations that symmetry says must be identical
differed by tens of wavenumbers. And the program that produced them printed, in the middle of its output,
"Checking for numerical inconsistencies... No inconsistencies found."

Today we found out why, wrote down in advance what two tests would show if we were right, ran them, and were
right. This post is about that day, because it changed one part of the plan from a worry into a settled step.

## What the calculation does

A molecule's vibrations are, to first approximation, springs: stretch a bond a little and the energy rises as
the square of the stretch. That is the harmonic picture, and it is what our label factory and our network are
built around. Real bonds are not perfect springs. Stretch further and the energy rises a bit less than the
square predicts; press two vibrations at once and each changes the other's stiffness. Those corrections are
called anharmonic, they shift every band by ten to forty wavenumbers, and no spectrum is worth comparing with a
telescope without them.

The standard way to get them is finite differences: nudge the molecule along one vibration, recompute the
stiffness matrix, nudge the other way, recompute, and read the change. For benzene that is sixty-one
stiffness matrices. Each one is itself expensive. And here is the catch we had not appreciated: the program we
use for the stiffness matrices, psi4, has no exact formula for them with our density functional. It gets each
one by nudging too, along every atom, and reading how the forces change. So the anharmonic constants were
nudges upon nudges. A nudge of one part in twenty, squared, multiplies whatever noise sits in the input by four
hundred.

## The free error bar

The thing that made the day possible is a piece of bookkeeping. Take two vibrations, call them i and j. The
constant that says how much j stiffens i can be measured by nudging along j and reading i's stiffness, or by
nudging along i and reading j's. In exact arithmetic the two are the same number. In a real calculation their
difference is the error, and it costs nothing: both routes are already in the sixty-one matrices.

We wrote a small script that assembles the constants both ways and prints the disagreement. For yesterday's
run: median 22 wavenumbers, one pair in ten above 107, the worst 1,265. The constants themselves are typically
tens to a few hundred. They were noise with a signal hidden in it. The package's own check had not seen this
because it averages the two routes first and then compares the average with itself.

## Two predictions, written down first

If the diagnosis was right, two things had to follow, and we wrote them into a note this morning before either
result existed.

First, a bigger nudge. Take the same noisy input matrices and use a step four times larger; the noise in a
second difference should fall by sixteen. Prediction: the median disagreement drops from 22 to about 1.4,
anything up to 3 acceptable, the worst from 1,265 to below 150.

Second, cleaner input. Another program, pyscf, does have the exact formula for the stiffness matrix with this
functional. Compute all sixty-one matrices again with it, at exactly the same sixty-one geometries, with the
same small step, and the disagreement should fall to whatever the numerical grid leaves: median below 1.

Both ran today in Helsinki, the second in six hours, the first in ten.

| | yesterday | bigger step | exact input |
|---|---|---|---|
| disagreement, median (cm⁻¹) | 22.4 | 2.3 | 0.1 |
| disagreement, worst | 1,265 | 110 | 47 |
| ring breathing, anharmonic shift | −217 | −28 | −16 |
| identical pairs, split by | tens | about 1 | 0.1 |

Both came out as predicted. With the exact input, the three bands we have been using as our test since the
weekend land at 852, 1006 and 1327 wavenumbers, against 849, 992 and 1310 measured in the laboratory. What is
left of the gap is the ordinary error of the cheap functional, which is exactly what the rest of the plan exists
to correct.

## The honest footnotes

The worst disagreement in the exact-input run, 47, is above the bound we had set. When we looked at where it
sat, it was entirely in pairs of vibrations that are exactly identical by symmetry, and the two members of each
pair carried precisely the same amount. Noise does not do that; a small rotation of our analysis basis inside
such a pair does. It is a limitation of our own diagnostic, not of the input, and it is on the list to fix.

The two clean runs do not agree with each other on the size of the shifts: −28 against −16 for the breathing
mode. That is the price of the bigger step, which trades noise for a systematic error of its own. A third run,
exact input with a middle-sized step, is computing as this is written; it will say which step the pipeline
should use.

And one of the day's delays was ours. A rerun that should have taken a minute took ninety, because a new
option had written a default value explicitly into the description of each task, and the cache that stores
finished tasks keys on that description. A default written out is a different key. The rule is now in the
code and in the ledger.

## What it changes

The tail of the pipeline, the part that turns corrected stiffness matrices into a spectrum, was the one
piece we had never seen produce a sensible number. Now it has, with known software and a measured error.
The two-route check has become a fixed step in the label factory: every derived quantity gets a second route
or a symmetry partner, and the difference becomes a line in the error budget. Three fixes to other people's
software that this week's work turned up, two in one package and one in another, went to their maintainers
today, the benzene numbers attached to the one that came out of this finding, after the packages' own test
suites had been run with the changes in place.

What it does not change is the question the plan actually hangs on: whether corrections measured on small
molecules carry to large ones. That answer starts arriving on Friday, from the run on the laptop in the
living room.

Two roads to the same number, and the distance between them is the error bar. It was there all along; we
only had to print it.

*Addendum, 21 September 18:2x.* The 47 in the exact-input column was, as suspected, our own diagnostic: inside pairs of identical vibrations it read the matrices in a slightly rotated basis. With the basis aligned to the actual nudges the worst disagreement for the exact input is 0.9 wavenumbers, and the three bands read 851, 1004 and 1324 against 849, 992 and 1310. The other two columns are unchanged.

*Addendum, 22 September 07:0x.* The third run finished late on the 21st: exact input with a step twice as large. It gives the same three bands to a tenth of a wavenumber (851, 1004, 1324) and the same breathing shift (−17), with the remaining disagreement between the two routes halved to under half a wavenumber. So the step did not matter once the input was exact; the −28 of the bigger-step column above was the noisy input, not the step. The pipeline uses the larger step on exact matrices from here on, and prints the distance between the two roads with every run.
