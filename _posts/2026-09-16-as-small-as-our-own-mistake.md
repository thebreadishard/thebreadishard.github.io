---
layout: post
title: "As small as our own mistake: the day our own stopping rule stopped us"
date: 2026-09-16 20:30:00 +0200
categories: plan05 probes
---

Every method in this project has a rule attached that says when to give up on it. The rules were written
before the measurements, which is the only time you can write them honestly. Today one of them fired, and it
fired on the part of the method I was most comfortable with. This post is about what it caught, why the
arithmetic behind it is almost embarrassingly simple, and what the alternative turns out to cost.

## Two halves of one calculation

A molecule the size of naphthalene vibrates in forty-eight distinct ways. A C–H bond stretching. A ring
bending out of the plane. The whole skeleton twisting. Our job is to work out how each of those vibrations
shifts when we move from a cheap, approximate description of the electrons to an expensive, accurate one.

That job has two halves. The first is each vibration on its own: if the ring bends, how much does that one
frequency move? Call it the diagonal, because if you lay the answers out in a grid it runs down the middle.

The second half is the couplings — how the vibrations lean on each other. When the ring bends, the C–H bond
sitting on it stretches slightly differently than it would alone. Those are the entries off the middle of
the grid, and there are a lot of them: 141 eligible pairs at naphthalene against 48 single vibrations.

Both halves worked at benzene. We had been assuming both halves would work at naphthalene.

## What the run said

The stopping rule reads one number: how much of the couplings' signal is left over after our model has had
its say. Below 0.3 we are doing well. At 1.0 we might as well not have bothered.

Here is that number as the calculation was fed more and more data, from 98 measurements up to 1,082:

| measurements | 98 | 262 | 426 | 672 | 918 | 1,082 |
|---|---|---|---|---|---|---|
| leftover | 1.000 | 0.966 | 1.628 | 1.296 | 1.103 | 0.961 |

It never falls. In the middle of the range it is worse than doing nothing at all. At benzene the same rule
had been satisfied after 210 measurements. At naphthalene there is no amount of data that satisfies it.

## Why, in one line

The couplings we are trying to measure are about **7 millionths** of an energy unit. The error our own model
makes — we describe a curved landscape with a parabola, and a parabola is not curved enough — is **6.74
millionths** of the same unit.

That is the whole story. The thing we want to measure is the size of our own mistake. It is like weighing a
letter on a scale whose needle drifts by the weight of a letter. You will get a number every time, and the
number will be meaningless.

There is a sharp illustration inside the same run. For the C–H stretching vibrations, using only the first
half of the calculation — the diagonal, each vibration alone — gets the frequencies right to **0.03 cm⁻¹**.
Adding our fitted couplings makes it **2.89 cm⁻¹**, a hundred times worse. The couplings we fitted were
mostly noise, and we were adding noise to something that was already right.

## A second problem, found while looking at the first

Our method has a setting that controls how far apart two vibrations can sit before we stop expecting them to
interact. We choose that setting automatically, by seeing which value leaves the smallest leftover.

| setting | 25 | 50 | 100 | 200 | 400 |
|---|---|---|---|---|---|
| leftover (what we select on) | 0.034 | 0.034 | 0.034 | 0.040 | **0.048** |
| worst error, in frequency | 3.37 | 3.36 | 3.36 | 3.36 | **0.82** |

The rule picks 25, because 0.034 is the smallest number in the top row. Now look at the bottom row. At 400
the number we select on is 40% worse and the thing we actually care about is four times better.

We were choosing on a quantity that does not move when the quantity that matters moves. It needs a new
design, and that is desk work rather than computing time — but it is the sort of thing you would much rather
find yourself than have someone else find for you.

## The way out, and what it costs

The same run points at an alternative. Instead of measuring energies at many geometries, measure *gradients*
— not just the height of the landscape at a point, but the direction of its slope. One gradient carries far
more information than one energy. On exactly the same data, the gradient route reaches the threshold after
**96 gradients**, with errors of 0.05 to 0.21 cm⁻¹.

So there is a way through. Today I priced it, and the answer was not the one I expected.

Naphthalene's deck is 291 calculations: 114 for the diagonal and 177 for the couplings. The gradient route
keeps the 114 and replaces the 177 with 96 gradients. So it pays only if a gradient costs less than
177 ÷ 96 = **1.84 times** an energy.

We measured that ratio on 14 September, for the two cheapest descriptions of the electrons: **2.84** and
**3.20**. Both are above 1.84. At naphthalene the clever alternative is roughly **1.45 times more expensive**
than the thing it replaces — twenty days of laptop time instead of fourteen.

It gets better with size, and that is the saving grace. The number of pairs grows much faster than the
number of vibrations, so the break-even ratio climbs: 2.60 at anthracene, 2.85 at pyrene, 4.09 at pentacene,
6.34 at phenanthrene. For the molecules this project is built to reach, a gradient costing three buys more
than it costs.

And the obvious cheap trick does not work. You can always fake a gradient by nudging the molecule slightly
in every direction and watching the energy change — but at naphthalene that is 96 energies per gradient,
which makes the route 52 times worse than the one it replaces. That door is closed by arithmetic, not by
taste.

## The honest caveats

None of the numbers above come from the expensive calculation. They come from a cheap stand-in: two
different approximate descriptions of the electrons, subtracted from one another, which behaves enough like
the real correction to test the machinery on. The real correction may sit differently against the same model
error. That is the next test, and its prediction is already written down: halve the distance we push the
molecule, and the model error should fall sixteen-fold while the signal falls only fourfold. If that works,
the cheap route survives at naphthalene. If it does not, the couplings need a bigger machine or software we
have not written.

Also: none of this touches the diagonal. The larger half of the work, and the half our go/no-go decisions
rest on, came through the same run in good shape.

## The point

A stopping rule you never let stop you is decoration. This one cost us a comfortable assumption on a
Wednesday afternoon, on cheap data, before a single expensive calculation had been spent on couplings. That
is exactly the transaction it was written for.

Everything here is in
[commit fab1fff](https://github.com/thebreadishard/udacity-capstone-plan/commit/fab1fff) — the finding, the
raw numbers, and the script that reproduces the pricing table.

---

**Addendum, 16 September, later the same evening.** The section above about the band-width setting is
wrong in its mechanism, and I would rather correct it than let it stand. I wrote that the rule picks 25
because 0.034 is the smallest number in the top row. It does not. That top row selects a different knob
entirely. The width is chosen by a different test: take the smallest width whose worst frequency error
clears a tolerance of 5 cm⁻¹. At width 25 the error is 3.37, which clears 5, so the search stops there and
never looks at 400.

That makes the fault worse rather than better, in two ways. First, the test measures the error against the
right answer — which we have in this rehearsal, because it is a rehearsal, and which we will not have on a
real molecule. As written, the rule cannot be run in production at all. Second, its shape is "smallest
setting that is good enough", so it stops at the first acceptable answer instead of the best available one.
A tolerance is a floor. This rule was treating it as a target.

The conclusion of that section is unchanged: the setting needs a new design, and the redesign is what I am
working on now.

**Second addendum, 17 September.** The pricing section above is wrong, in the direction that matters,
and the error was mine: I priced the wrong version of the alternative.

Measuring gradients can be done two ways. You can assume nothing and solve for all 1,128 possible
couplings, which needs 96 gradients — that is the number I quoted. Or you can use the fact that a
symmetric molecule only allows certain pairs to couple at all, which cuts 1,128 down to 141 and needs
**18 gradients**, recovering every element exactly rather than by fitting. An experiment from four days
ago had already measured this; I quoted its dense row and missed its symmetry row.

The difference is not small. Instead of replacing 177 calculations with 96 gradients, you replace the
entire deck of 291 with 18. The break-even cost ratio moves from 1.84 to **16.2**, against a measured
ratio of about 3. So at naphthalene the gradient route is roughly **five times cheaper** than the
energy route, not 1.45 times more expensive.

Two things this does not settle. The exact recovery assumes exact gradients; how measurement noise
propagates through that construction is untested. And the cost ratio for the expensive method we
actually use has never been measured, because neither way of measuring it fits on this laptop. But the
bar it has to clear moved from 1.84 to 16.2, which is a different conversation entirely.

**Third addendum, 17 September, evening.** Both tests this post promised have now run, on the same
cheap stand-in data, with their thresholds written down before the numbers existed.

The amplitude test — push the molecule half as far, so that the model's own error should fall
sixteen-fold while the signal falls only fourfold — did not work. On all 616 coupling patterns, matched
one for one against the full-amplitude set, the signal fell by exactly the predicted factor of four and
the leftover did not move at all: 0.96 before, 1.03 after, at every band width. That is neither of the two
outcomes I had written down. A leftover that scales with the signal is not noise and not the model's
curvature error; it means the design itself — hundreds of energies against more than a thousand unknown
couplings — cannot pin the couplings down however hard or gently you push. The energy route to the
couplings is closed at naphthalene. The diagonal, each vibration on its own, is untouched.

The second test asked whether that matters for what anyone can measure. Above naphthalene the best
laboratory references resolve 5 to 17 cm⁻¹, so I asked: if you drop the couplings entirely, does the
*shape* of the spectrum change at that resolution? For the C–H stretches, no — under 2 % of the peak.
For the fingerprint region between 6 and 9 µm, where the strongest astronomical PAH bands sit, yes: the
couplings mix neighbouring vibrations, one band keeps only 83 % of its identity, positions move by up to
21 cm⁻¹ and intensities by up to 28 %, and the smoothed spectra differ by a third of the peak at 5 cm⁻¹
and by a fifth at 13. No reference is blurry enough to hide that.

So the two tests close the same door from both sides. The couplings cannot be skipped, and they cannot
be had from energies at this size. They come from gradients: the 18-gradient construction of the second
addendum, which recovers every coupling exactly and, measured today, costs about 2.7 times less than the
energy deck it replaces. One question remains before it becomes the plan of record — whether the
gradient code we can run computes the same quantity our own energies do — and its test is written and
starting tonight.

Commits: [610bfcb](https://github.com/thebreadishard/udacity-capstone-plan/commit/610bfcb),
[ff54669](https://github.com/thebreadishard/udacity-capstone-plan/commit/ff54669).

**Fourth addendum, 17 September, late.** One number in the third addendum needs correcting, and the
reason is worth a sentence. The "about 2.7 times less" rested on a cost ratio for gradients that I had
read off the wrong attribute of the software: it reported the small triples correction on its own rather
than the whole correlation energy. The mistake surfaced within the hour, when the next test compared that
number against our own code and found a discrepancy twenty times too large to be physics. Re-measured on
the right quantity, the ratio is 6.0 instead of 5.7, and the saving at naphthalene is **about 2.5 times**,
not 2.7. Nothing else in the argument moves. The lesson is the same one this whole post is about: an
attribute named like the method is not the method's energy — read the property, not the name.
