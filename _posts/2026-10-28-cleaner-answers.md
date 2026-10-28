---
layout: post
title: "Cleaner answers"
date: 2026-10-28 18:45:43 +0100
categories: plan05 progress
---

<!-- Written 28 Oct 2026 (the user: 'Ja, schrijf het concept over v1.3 nu'); published 28 Oct on the user's word ('Publiceer'; TASKS 143).
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline), commit 831afd13:
     modules/05_support_predictor/out/E7_rungC_chain35_read.md (C35R, 28 Oct 04:28): both versions read on the same 862 analytic targets;
       (a) chain 35 / chain 34 read again: ring-ip 1.66 / 1.82, CH-stretch 1.15 / 1.25, CH-oop 1.99 / 2.36 (beyond 2 SE), other 3.35 / 3.35,
       coupling ratio 0.172 / 0.192 (beyond 2 SE); (b): 2.12 / 2.35, 2.50 / 2.55, 2.70 / 3.33, 3.89 / 4.66, 0.263 / 0.289 (beyond 2 SE);
       the registered 'other' line (a fall of at least 0.3 on (a)) reads 'labels not it'; promotion by decision 57 (worse on none of five,
       lower on 9 of 10);
     modules/05_support_predictor/out/E7_rungC_chain35_analytic.log / .md: analytic Hessians substituted for 862 molecules; n 750, three
       seeds; wall 14379 s;
     modules/05_support_predictor/out/E7_rungC_chain35_analytic_eval_frozenb_seed0.md: (a) 10 molecules, (b) 39;
     obstacle ledger 28 Oct 04:4x (labels lanes: 805 ok, 2 failed);
     the 3 Oct promise: _posts/2026-10-03-breadth-first.md (finite-difference noise CH-stretch 0.2, ring and skeletal 1.5, CH-oop 3.3).
     Version list: modules/05_support_predictor/MODELS.md (v1.1 = chain 34, v1.2 = chain 36 superseded, v1.3 = chain 35 carried).
     (b) of chain 34 here differs from the 26 Oct post (2.57 / 3.81 / 2.62 / 6.03): there it was read against the finite-difference
     answers of (b); C35R reads both versions against the analytic ones. -->

Our network learns from examples: for each molecule in its training set, it sees the answer it should give and adjusts itself to come
closer. If those answers are noisy, the network learns the noise along with the physics. This week we replaced the answers, and the
network got better. The vibrations we hoped would improve did not.

## Where the noise came from

The answers are [Hessians](/glossary/#hessian): tables of how stiff a molecule is against every small push of its atoms. They hold
everything the network needs to know about where the vibrations of a molecule sit. Until now we computed most of them by
[finite differences](/glossary/#finite-differences): push the atoms a tiny step, compute the forces, push back, compute again, and
subtract. It works, but every subtraction of two nearly equal numbers leaves a little noise behind.

On [3 October](/2026/10/03/breadth-first/) we measured that noise per kind of vibration. It was small for the C–H stretches (0.2
wavenumbers), larger for the ring and skeletal motions (1.5), and largest for the C–H out-of-plane bends (3.3), which is exactly the size
of the error we were aiming for. We wrote then that the honest next step was cleaner answers, computed
[analytically](/glossary/#analytic-derivative): from exact formulas for the derivatives instead of small steps.

A rented server spent the past weeks on that, and the training set now has analytic Hessians for 862 molecules. On Tuesday night the laptop trained the
network again on them, with nothing else changed: same design, same recipe, same 750 training molecules, three trainings with different
random starts. Four hours later we had version 1.3.

## How a new version earns its place

We keep a list of network versions, and only one of them is the one the rest of the project uses. A new version replaces it only if it is
worse on none of five measures, on both groups of molecules the network never sees during training
([hold-out sets](/glossary/#hold-out-set)). Ten of those molecules resemble the training set. The other 39 are built on ring frameworks
it has never met.

Four of the measures are the typical frequency error for each kind of vibration. The fifth asks how well the network gets the way the
vibrations of neighbouring rings mix: it is the share of that mixing error the network leaves over, so 0.17 means it removes 83 per cent.

To make the comparison fair, the old version was scored again against the same cleaner answers as the new one. Errors in wavenumbers,
lower is better:

| | C–H stretch | out-of-plane bend | ring, in plane | other | ring mixing |
|---|---|---|---|---|---|
| familiar frameworks, old (v1.1) | 1.25 | 2.36 | 1.82 | 3.35 | 0.192 |
| familiar frameworks, **new (v1.3)** | **1.15** | **1.99** | **1.66** | 3.35 | **0.172** |
| unfamiliar frameworks, old (v1.1) | 2.55 | 3.33 | 2.35 | 4.66 | 0.289 |
| unfamiliar frameworks, **new (v1.3)** | **2.50** | **2.70** | **2.12** | **3.89** | **0.263** |

Worse on none, lower on nine of ten. Version 1.3 is now the network the project uses
([the read](https://github.com/thebreadishard/udacity-capstone-plan/commit/831afd13)). (There was a version 1.2 in between: a different
input to the network, which was tested and rejected.)

Not every one of those nine improvements is solid. Three trainings with different random starts do not end in exactly the same place,
and most of the differences in the table are within that spread. Three are clearly beyond it: the out-of-plane bends on the familiar
frameworks, and the ring mixing on both groups. The rest point the same way, but on their own they would not convince us.

## What did not move

The out-of-plane bends improved most, which is what the noise measurement of 3 October predicted: their answers were the noisiest, so
cleaning them helped most.

The column that did not move is "other": the slow, floppy motions of the whole molecule, and the motions of side groups attached to the
rings. On the familiar frameworks it stayed at 3.35 to two decimals. Before the training we wrote down what we would conclude: if the
cleaner answers lowered that number by at least 0.3, the noise had been the problem; if not, it was not. It did not, so the noise was not
the problem. On the unfamiliar frameworks the same column did fall, from 4.66 to 3.89, but that is within the spread of the three trainings.

That is a useful negative. On 3 October we had already guessed that this family's error was a learning gap rather than a noise floor, and
now we know it. Whatever holds the floppy motions at 3.35, it is in what the network learns or in what it is shown, not in the quality of
the answers. They stay the open family on our list.

## What this means

Cleaner answers made a better network, at the cost of a few weeks of rented computing and four hours on a laptop, and the improvement landed
where the noise measurement said it would. The floppy motions are now the clearest remaining problem, and we know one thing that is not
causing it.

The full numbers, the scripts and the lines written before the training are in the project's repository.
