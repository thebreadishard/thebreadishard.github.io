---
layout: post
title: "The hill, eighteen times"
date: 2026-11-01 21:40:33 +0100
categories: plan05 probes
---

<!-- Written 1 Nov 2026 at the user's request ("Schrijf de blog over de geometrieterm"); published 1 Nov on the user's word ("Publiceer maar"). Numbers trace to
     (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/PreRegistration_2026-10-31_Geometry_Term.md — registered 31 Oct (commit 613d1381), amendment 1 (dd3890a3),
       amendment 2 before the read (ac7c0f44), outcome (179669fd);
     probes/results_m1/geometry_term_2026-10-31/read/geometry_term_read.md and .json (commit 179669fd): 18 molecules, 758 modes moved
       by >= 1 cm-1; rms shift 10.99, residual 2.41 (true displacement) and 2.41 (Newton step); windows below 1000 8.40 -> 2.19 (332),
       1000-1900 13.99 -> 3.10 (289), 1900-2700 21.94 -> 1.12 (6, not read), X-H stretch 8.12 -> 0.36 (131); helped 18 of 18; per
       molecule 0.72-4.69; without A_8f6ed7c002 and B_190a33a33e: 16 molecules, 680 modes, 11.16 -> 2.39; the left-out G modes at
       39.9 and 41.0 cm-1; overlap^2 below 0.5 in five molecules, lowest 0.324 (B_a41f4cff9d, residual 4.69).
     The imaginary torsions (41.1i, 43.1i cm-1): amendment 2's text. Benzene numbers: the post of 22 Sep. -->

On 22 September this notebook found out that [benzene](https://thebreadishard.github.io/spectrum-atlas/molecule/A_8448043181/)'s
carbon–hydrogen stretches had come out forty-five to sixty wavenumbers too low, and why
([The hill was not where we stood]({% post_url 2026-09-22-the-hill-was-not-where-we-stood %})). Our expensive calculations are made at
the cheap method's resting shape of the molecule, its [equilibrium geometry](/glossary/#equilibrium-geometry), not at their own. A bond
that the better method would make slightly shorter is measured while it is slightly too long, like a guitar string tuned at the wrong
tension. That post also found a way to fix this without moving anything: a short formula, which we called the *geometry term*,
predicts how far each vibration shifts when the molecule relaxes to the better method's resting shape. On benzene it worked.

That was one molecule. A rule that holds on one molecule is a hint, not a rule. This post is about the test on eighteen more.

## The question

The geometry term multiplies two things. The first is how far each atom would move if the molecule relaxed to the better method's
shape. The second is how quickly each vibration's stiffness changes when the atoms move: a third derivative of the energy, the same
numbers the anharmonic step of this project computes anyway (see [Half price]({% post_url 2026-10-14-half-price-if-you-write-it-down-first %})).
Both are cheap compared with a full new calculation at the better level.

So the question had two parts. Does the formula predict the shifts on molecules other than benzene? And does it still work if we only
*estimate* where the atoms would go, with one step from the forces at the start, instead of actually relaxing the molecule? The second
part matters for the product: one step costs one gradient, relaxing costs several.

## The test, and what I wrote down first

We used the eighteen molecules whose third derivatives we already had: [naphthalene](https://thebreadishard.github.io/spectrum-atlas/molecule/A_01f3186607/),
azulene, styrene, quinoxaline and fourteen others with 12 to 20 atoms. The cheap method is [B3LYP](/glossary/#b3lyp), the
method our whole corpus starts from. In the role of the better method we used [ωB97X](/glossary/#wb97x), a more careful DFT method.
Coupled cluster on eighteen molecules would have cost far more than this test is worth, and ωB97X is the same stand-in the network's
earlier tests used.

For each molecule the laptop computed ωB97X's vibrations twice: at B3LYP's resting shape, and again after relaxing the molecule to
ωB97X's own resting shape. The difference is the measured shift of every vibration. The formula gives the predicted shift. The
laptop needed just under a day for all eighteen.

Before the first molecule ran, I wrote down four lines ([the registration](https://github.com/thebreadishard/udacity-capstone-plan/commit/613d1381)),
counting only vibrations that move by at least 1 wavenumber:

1. With the real relaxed shape, at most 30 % of the shift may be left after the correction.
2. With the one-step estimate, at most 35 %.
3. No region of the spectrum may get worse.
4. The correction must help in at least 16 of the 18 molecules.

My prediction: all four hold.

## A bump on the way

Two molecules made trouble. Styrene and its cousin with an extra carbon–carbon triple bond both carry a small CH=CH₂ group on the ring.
At ωB97X's resting shape they lie flat, but for one motion, the group twisting about the bond to the ring, flat turns out not to be the
bottom of a valley. It is the top of a very low hill: the molecule would rather twist a little. A motion that sits on top of a hill
does not vibrate at all, and the calculation returns an imaginary number for its frequency (41i and 43i cm⁻¹ in chemists'
notation). There is no shift to measure for it.

So before reading any result, I added a rule to the registration
([the amendment](https://github.com/thebreadishard/udacity-capstone-plan/commit/ac7c0f44)): for those two molecules, that one twisting
motion is left out, and every other vibration of all eighteen molecules counts. As a check, the answer is also reported without the two
molecules at all, so that the leaving-out cannot have decided anything. The alternative was to let the two molecules twist into their
real, slightly bent resting shape. Then most of the change between the two shapes would be one large swing along the softest motion,
which a first-order formula is not meant to handle, and we would be testing something else.

## The answer

All four lines hold ([the read](https://github.com/thebreadishard/udacity-capstone-plan/commit/179669fd)). Over 758 vibrations, the
typical shift (the root mean square) is 10.99 wavenumbers. After the correction, 2.41 is left: 22 % of the shift, where the line
allowed 30 %. The one-step estimate leaves exactly the same 2.41. The correction helps in all eighteen molecules. Without styrene and
its cousin the picture is the same: 11.16 before, 2.39 after.

| region of the spectrum | vibrations | typical shift | left after the correction |
|---|---|---|---|
| below 1000 cm⁻¹ | 332 | 8.40 | 2.19 |
| 1000–1900 cm⁻¹ | 289 | 13.99 | 3.10 |
| 1900–2700 cm⁻¹ | 6 | 21.94 | 1.12 |
| C–H stretches, above 2700 cm⁻¹ | 131 | 8.12 | 0.36 |

The 1900–2700 row holds only six vibrations, too few to judge by the third line's own rule; they are shown for completeness. The
carbon–hydrogen stretches, the motions that started this story on benzene, almost disappear: 8.12 wavenumbers of shift, 0.36 left.

## What I got wrong

I had predicted that the shift would be largest in the carbon–hydrogen stretches, because those bonds change length the most between
the two methods. It is not. The biggest shifts are in the middle of the spectrum, 1000 to 1900 wavenumbers, where the ring vibrations
live. The formula handles them too, just less completely (13.99 down to 3.10). The four lines held; the reason I gave for expecting it
did not.

## What this is not

This is a test between two DFT methods, not against coupled cluster. It shows that the formula carries from one molecule to eighteen and
that the cheap one-step estimate is good enough. It does not show how well it does on the real anchors; that comes when the anchors'
own gradients are used.

In five molecules, matching each vibration at one shape to the same vibration at the other shape was uncertain: two vibrations had
partly mixed. The molecule where this was worst also has the largest left-over error, 4.69 wavenumbers. So part of what is left may be
a bookkeeping limit of the test rather than a failure of the formula.

And the formula is not yet in the product. It goes into the design now.

## What this means

A corrected vibration is still the two things the September post described: the stiffness measured at the cheap shape, plus the geometry
term. What changed today is that the second part is no longer a benzene result. For eighteen molecules it removes about four fifths
of the shift, and it does that with one gradient at the better level and third derivatives the anharmonic step computes anyway.

The full numbers, the scripts and the lines written before the runs are in the project's repository.
