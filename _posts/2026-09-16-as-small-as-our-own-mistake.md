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
