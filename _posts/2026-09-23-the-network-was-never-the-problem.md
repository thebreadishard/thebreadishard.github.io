---
layout: post
title: "The network was never the problem"
date: 2026-09-23 14:21:30 +0200
categories: plan05 learning
---

For a week, one part of this project refused to learn. Our small neural network could predict how much each vibration of a
molecule shifts when you move from a cheap calculation to a better one — that worked from the first day. But it could not
predict the *couplings*: the numbers that say how the vibrations re-mix under the better calculation. Whatever we fed it, the
network's best answer for every coupling was zero. Yesterday we had four times as much data as a week ago, and the answer was
still zero.

Today we found out why, fixed it, and watched the same network learn the couplings in four minutes of computer time. The
short version: the network was fine. We had been asking it for something that could not be learned from what we showed it.

## The curve that did not move

We do not decide that something "does not work" from one small experiment; that is a rule of this project. So on Friday we
had written down, in advance, a test: train on 45, then 100, then 175 molecules, and read how the coupling error changes. If
the error falls steeply with more data, the model is data-starved and more data is the cure. If it stays flat, more data is
not the cure.

It stayed flat. Three different models, three data sizes, and the coupling error sat on or above the line you get by
predicting nothing at all. The band shifts, meanwhile, kept improving. Same data, same network, one part learning and one
part stuck.

## Asking the wrong question

A coupling between two vibrations is a number whose *sign* depends on an arbitrary choice: which way round you draw each
vibration. Flip one of them and the coupling flips sign. Every description we gave the network about a vibration — how
much hydrogen moves, how localised it is, which family it belongs to — is blind to that flip. So the network was asked to
predict a sign it could not possibly see. The mathematically best answer to such a question is zero. It had learned exactly
that, to two decimal places.

There was a second problem hiding behind the first. In the language of individual vibrations, the correction between the two
calculations looks like a dense tangle: every vibration coupled a little to every other. In the language of *bonds and
angles* — the chemist's local coordinates — the same correction is sparse and simple: a bond stiffens a little, two bonds
in the same ring stiffen each other a little. Translating that simple thing into vibrations smears it over the whole
molecule in a way that differs from molecule to molecule. We had been showing the network the smeared version.

## Measuring where the correction lives

Before building anything new we measured it. Take the correction, write it in bond-and-angle coordinates, and ask how
much of it sits on the diagonal (each coordinate on its own), how much on pairs that share an atom, and how much on pairs
of bonds inside the same ring. No fitting, no free parameters — just a projection.

The diagonal alone explains almost nothing of the couplings. Pairs sharing an atom explain about forty percent of the
correction. Add the bond–bond pairs inside each ring — the way neighbouring bonds in an aromatic ring lean on each other —
and three quarters of the correction is accounted for. That is a local object with a chemical name, and it is what a
network should be asked to predict.

## Asking the right question

So we changed the target. Instead of one matrix per molecule, the network now predicts thousands of small local numbers per
molecule: for each bond, angle and torsion, and for each pair of them that shares an atom or a ring, how much the correction
changes it. Those numbers have a definite sign and a definite meaning. Afterwards we translate the prediction back into
vibrations and check it against the truth exactly as before.

Same 175 molecules. Same held-out sets, fixed in advance: ten small aromatics, and thirty-nine molecules built on two ring
skeletons the network had never seen. Coupling error relative to predicting nothing: 0.43 on the first set, 0.47 on the
second, where every previous model had sat at 1.00. The corrected vibration frequencies — the thing the rest of the
project actually consumes — come out within five wavenumbers of the truth, against twenty-three for no correction. A simple
network and a tree-based model agree to two decimals, so this is a property of the question, not of the learner.

## The one molecule that still failed

On the first held-out set, one molecule refused to fall in line: benzene, of all things, the simplest ring there is. Its
error was as large as if nothing had been learned. Nine other molecules were fine.

That smelled like data, not learning, and this project has a rule for that too: every derived number gets a second,
independent route. We recomputed benzene's two force-constant matrices with a different program and a different method
(exact derivatives instead of finite differences) at the identical geometry. The cheap-level matrix agreed. The better-level
one did not: some frequencies differed by more than a hundred wavenumbers, at a geometry that is perfectly hexagonal. In a
perfect hexagon certain vibrations must come in identical pairs; the stored matrix split them apart, the recomputed one did
not. The stored number was wrong. We had trained and tested against a phantom.

With benzene's target replaced by the sound one — and nothing else touched, not the training set, not the model — benzene's
error fell from "nothing learned" to well under half the no-coupling line, and the first held-out set as a whole from 0.81
to 0.43. We then screened all 244 molecules for the same disease. Benzene was the only one.

## What we take from today

- The network was never the limit. When it was asked for a sign it could not see, it answered zero, and more data could not
  change that. When it was asked for the local, chemically meaningful object the correction really is, it learned it on
  ring skeletons it had never seen.
- Choose the representation by measuring where the physics lives, then let the network learn.
- One wrong number in 244 sat in a held-out set and looked like a learning failure for a day. The second route found it.
  Both rules of this project — write the test down before you run it, and check every derived number by an independent
  route — earned their keep before lunch.

*Every number here comes from scripts and result files in the project repository: the learning curve
(`modules/05_support_predictor/out/E6_learning_curve_in_data_2026-09-23_excl_imaginary.md`), the local-pairs model
(`out/E7_rungB_2026-09-23_analytic.md`), the second route for benzene (`corpus/molecules/A_8448043181/analytic_check.json`),
with the pre-registered predictions and outcomes in `GoalGathering/notes/PreRegistration_2026-09-23_E7_Couplings_in_Local_Coordinates.md`.*
