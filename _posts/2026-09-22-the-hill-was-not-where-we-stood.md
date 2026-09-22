---
layout: post
title: "The hill was not where we stood"
date: 2026-09-22 17:20:00 +0200
categories: plan05 probes
---

This morning a run that had taken thirty hours handed back twenty corrected vibration frequencies for benzene, and some of
them were wrong by a margin that could not be waved away. The carbon–hydrogen stretches, the highest-pitched motions
of the molecule, came out forty-five to sixty wavenumbers below the best numbers in the literature. The ten in-plane motions
had landed within a few wavenumbers. Something in the recipe treated one kind of motion differently from another.

## What the run does

The plan corrects a cheap calculation of how a molecule vibrates with a few expensive measurements. The cheap calculation
gives the stiffness of every vibration; the expensive method, a coupled-cluster calculation that would be unaffordable for the
whole molecule, is asked only about the stiffness *along each vibration*, one direction at a time, using orbital spaces that
we freeze at the starting geometry and carry along. Yesterday's post was about the noise in such stiffnesses; today's is about
their meaning.

We first suspected the frozen spaces. The expensive method keeps only the electron pairs that matter locally, and the
threshold for "matters" could have been too loose for a stiff C–H bond. So we reran the two worst cases with the threshold
ten times tighter. Five and a half hours later the numbers were the same to a tenth of a wavenumber. Whatever it was, it was
not that.

## Where you stand changes how steep it is

The answer was simpler and more instructive. The literature numbers describe each method at its *own* resting geometry. Our
deck measures every method at the cheap method's resting geometry, because that is where the cheap vibrations were defined.
For most motions the two geometries are so alike that it does not matter. For a C–H bond it does: the expensive method
prefers the bond a few thousandths of an ångström shorter, and the stiffness of a bond depends steeply on its length. Stand a
little down the slope and the hill looks less steep than it is.

We tested this with a method where the effect had to be large, plain Hartree–Fock, which likes its C–H bonds shorter by a
hundredth of an ångström. Measured at the cheap geometry its C–H stretch reads 3225 wavenumbers; measured at its own resting
point, 3336. A shift of 111. Then the part that made the afternoon worthwhile: the correction can be *predicted* without
moving anything. The stiffness change per unit of displacement is a third derivative of the energy, and we already had those
numbers from yesterday's two-route force field. Multiply them by the displacement and the prediction is 114. For six motions
of very different kinds the prediction stayed within about ten percent of what we measured.

Applied to the real run with the coupled-cluster method's own gradient, which the deck had recorded without our noticing, the
four stretches move from forty-five to sixty wavenumbers low to within seven to twenty-three of the literature. Two
out-of-plane motions do not improve and stay on the open list, honestly marked.

## What changes in the plan

A corrected frequency is now two things added together: the stiffness measured at the cheap geometry, and a geometry term
that says how far the expensive method would move the resting point and what that does to the stiffness. The second part costs
one gradient per molecule instead of a whole new set of expensive calculations, and it uses derivatives we compute anyway.
It went into the design today as a fixed step, on both the side that makes training labels and the side that turns a corrected
molecule into a spectrum.

The general lesson is the same one this notebook keeps relearning in new clothes. A number is only as good as the question it
answers. "How stiff is this bond?" has no answer until you say where you are standing when you ask.
