---
layout: post
title: "One decade tighter: how we found out what the leftover error was made of"
date: 2026-09-13 12:00:00 +0200
categories: plan05 probes
---

*Draft for review — not published. Numbers from the scripts linked at the end; nothing here is a claim about
the pipeline's final accuracy, which does not exist yet.*

The first post ended with a number: when we freeze the "orbital spaces" of the local coupled-cluster
method and move the molecule along one of its vibrations, the energy curve is smooth, but its curvature is
off by a small, systematic amount compared with the full, expensive calculation. In frequency terms that
bias was +0.5, +0.03 and +0.8 cm⁻¹ on the three benzene vibrations we tested. Small — but a bias is a
bias, and the whole plan is about corrections of a few cm⁻¹, so we needed to know where it came from.
This week we found out. The answer was cheap to test and it changed two rules of the plan.

## Two suspects

A local coupled-cluster method saves time by throwing away the parts of the electron correlation that
are far away or unimportant. How much it throws away is set by a pair of thresholds. The released code
has presets; we had been running the stricter of them, called "tight". Freezing the spaces (our trick)
is a second, separate approximation. So the leftover bias had two possible sources:

1. **The freezing itself.** If holding the spaces fixed across geometries distorts the curvature, the
   bias is ours, and tightening the thresholds would not remove it.
2. **The throwing-away.** If the method at "tight" simply discards a bit too much, the bias belongs to the
   thresholds, and one notch stricter should shrink it.

The test is obvious: rerun the frozen arm with thresholds ten times stricter ("xtight", one decade below
tight) at the same 27 geometries, against the same full-calculation reference, and see whether the bias
moves. It costs about 75 minutes per geometry on the laptop, so the rescan ran overnight and finished
Saturday morning.

## What moved and what did not

| | tight | xtight (one decade stricter) |
|---|---|---|
| bias of the corrected curve, three modes (cm⁻¹) | +0.47 / +0.03 / +0.79 | **+0.11 / −0.01 / +0.23** |
| bias of the raw local energy, before correction (cm⁻¹) | +16.3 / +1.8 / +5.3 | +2.1 / +0.2 / +0.8 |
| smoothness of the frozen curve (scatter, µE_h) | 0.003–0.044 | 0.003–0.044, unchanged |

The bias dropped by a factor of three to four; the smoothness did not change at all. That is the
fingerprint of suspect 2. The freezing was never the problem: it keeps the curve smooth at either
setting, and the small remaining bias is what the method discards. Rule changed: the pipeline's
expensive step now runs at the stricter thresholds, at roughly twice the cost per point at benzene.

("Corrected curve" means the local energy plus a cheap patch — the difference between a full and a local
second-order perturbation energy — that puts back most of what the local method throws away. The raw
numbers show how much work that patch does: it takes a 16 cm⁻¹ error down to half a cm⁻¹.)

## The bigger term was hiding in plain sight

While the rescan ran, a second question came up. The expensive calculation uses a finite "basis set" of
functions to describe the electrons; ours is a triple-zeta set, the largest that fits the laptop for
benzene. How much does the answer change with a bigger set? For the full coupled-cluster method that is
unaffordable here, but two-thirds of any basis-set effect on vibrations sits in the cheap parts of the
calculation — the mean-field energy and second-order perturbation theory — and those *are* affordable
in bigger sets. So we ran them: the same 27 geometries in a quadruple-zeta set for both, and in a
quintuple-zeta set for the mean field. Nineteen minutes for 54 points.

The result dwarfs the threshold story. Going from triple to quadruple zeta moves the three benzene
frequencies by +1.8 / −2.4 / −4.6 cm⁻¹ through the mean field alone and by −1.0 / −2.4 / −8.0 through the
correlation part; quintuple zeta adds another +2.0 / −0.4 / +1.5 to the mean field. Added up, the cheap
basis terms shift the modes by **+2.8 / −5.2 / −11.1 cm⁻¹** — ten to fifty times the bias we had just
spent a night chasing.

## What we did about it

Second rule changed. Every anchor energy now carries two extra terms, each a difference of two cheap
energies at the same geometry: the quadruple-minus-triple perturbation term and the quintuple-minus-triple
mean-field term. No fitted parameter, under one per cent of the cost of a point. The comparison that
licenses the method (frozen against full, in the same basis) is untouched, because the terms are common to
both sides and cancel. What the correction cannot capture — how the small remainder, coupled cluster minus
perturbation theory, itself changes with the basis — stays an open item, honestly labelled, for a cluster
run in a bigger set.

## Two lessons that are not chemistry

**Measure the cheap thing first.** The threshold rescan took a night and improved the bias by half a
cm⁻¹. The basis line took nineteen minutes and exposed an eleven-cm⁻¹ term. We did them in the wrong
order because the threshold question was the one on the list. The cheap question should have been asked
the moment it existed.

**Do not run two heavy things on one laptop.** The follow-up measurement (the same tightening at
naphthalene, expected to take about two days) died after three and a half hours — not from the chemistry
but because I started an eight-gigabyte build of something unrelated on the Windows side while the
calculation held twenty-two gigabytes in its Linux virtual machine. Windows ran out of memory and shut the
whole virtual machine down. The run is restarted with a small layer we wrote that saves each of its 24
pieces as it finishes, so that the next interruption costs one piece instead of everything. That layer
tested exact on benzene: interrupted, resumed, same energies to the last digit.

## Where the numbers come from

Everything in this post is printed by scripts in the public plan repository: the xtight rescan and its
comparison with the sealed reference (commit 39b1764), the cheap basis line (commit 1806244), the dated
decision that put the two basis terms into the anchor and its implementation (commits 1a7c395 and
b4b478f). The benzene reference energies themselves stay sealed until the plan's pilot note, as before.

*Next time: what the naphthalene measurement says about where the rest of this project can be computed.*
