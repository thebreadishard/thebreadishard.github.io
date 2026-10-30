---
layout: post
title: "How many teachers?"
date: 2026-10-30 08:00:00 +0100
categories: plan05 progress
---

<!-- Draft written 29–30 Oct 2026 overnight (TASKS 143; the user: 'Doe alles maar' for the night's list, which said: the anchor-price
     post as a draft only, published on the user's word). Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     modules/05_support_predictor/out/anchor_count_2026-10-29/anchor_count_read.md (commit 2c9332b6): arm (c), body1_es: crossings of
       3 cm-1 at k = 100 for ring-ip and CH-stretch, CH-oop 167 (153–177), 'other' > 200; at k = 200: 1.71 / 1.76 / 2.73 / 3.59;
     modules/05_support_predictor/out/anchor_price_2026-10-28/anchor_price_read.md: C1 (a cover beats random draws) does not hold;
     modules/05_support_predictor/out/anchor_plan/count_n167.md (commit 65e84b70): reservoir 200 = fused 89, single 67, five 44;
       167 = fused 74, single 56, five 37; cheapest ≈ 3,503 CPX62 h, ≈ EUR 729, 10–18 atoms, 39 with proxy labels; drawn ≈ EUR 3,026.
     Read set 343 and reservoir 200: PreRegistration_2026-10-29_Anchor_Count.md. The bend's CH-oop factors of the proxy's shift
     (single 0.02, fused −0.45, five −0.90): PreRegistration_2026-10-27_Anchor_Price_Arm_C.md, as registered. -->

The network in this project learns in two steps. First it learns from a large set of cheap quantum-chemistry calculations (DFT) how
the vibrations of a molecule shift between two cheap methods. Then it should learn the last step from a much better and much more
expensive method, coupled cluster. A coupled-cluster calculation of one whole molecule's vibrations is what we call an *anchor*: a
teacher's answer the network can check itself against. We have about ten. The question this post answers is how many we need, and
what they would cost.

## Why we did not ask the ten we have

The honest way to find out would be to train on some anchors and test on others. With ten, that test rests on one or two molecules
at a time, and the answer would mostly be luck. So we answered the question on a stand-in task where we can afford hundreds of
teachers.

The stand-in works like this. On the DFT data, we bend the answers the way the real coupled-cluster answers differ from DFT on the
anchors we already have. The clearest pattern there is in the out-of-plane bending of the C–H bonds. Compared with the DFT
difference the network learned first, the coupled-cluster correction to these bands is close to zero on a single ring, about half
as large and of the opposite sign on fused rings like naphthalene, and almost as large and of the opposite sign when the molecule
has a five-membered ring. The bent answers carry that pattern. Then we give the trained network the bent answers of *k* molecules,
let it adjust, and measure its error on 343 molecules it never saw. We did this for *k* = 100, 120, 150 and 170 with three random
picks of molecules each, and for *k* = 200 with the whole pool of 200, each time with three versions of the network.

## The answer: 167, for the families that can get there

The target is the one the project has used throughout: an error of at most 3 cm⁻¹ per family of vibrations.

- The in-plane ring motions and the C–H stretches get there with 100 teachers already.
- The C–H out-of-plane bends need **167** (between 153 and 177 within the spread of the runs).
- The floppy, low-frequency motions do not get there by 200: their error is 3.59 cm⁻¹ at 200 and still falling slowly.

So 167 is the number for three of the four families. The fourth is the same family the network struggles with on DFT alone, and more
teachers of this kind are not the fix for it.

## A surprise about which molecules to pick

We expected that choosing the teachers carefully would help: make sure every kind of C–H arrangement (one hydrogen alone on a ring
edge, two side by side, three, four) appears often enough. It did not. A carefully covering selection did no better than random
picks. What does matter is the mix of ring types, because that is what the correction follows. So the plan keeps the mix of the
pool the experiment drew from: 89 fused, 67 single-ring and 44 five-ring molecules out of 200, which for 167 teachers means 74, 56
and 37.

## The price, as two numbers

One anchor costs rented server time, and the cost grows steeply with the size of the molecule. With the price rule we measured on
the last wave of anchors:

- **The cheapest 167 with that mix: about 3,500 server hours, about €730.** These are small molecules, 10 to 18 atoms.
- **167 picked the way the experiment picked them: about €3,000.** The experiment drew from a pool with larger molecules too.

Both numbers are honest, and the truth for the plan lies between them. The experiment measured how many teachers are needed when
they look like its pool. Whether small teachers teach as well as a mixed set has not been measured, and the cheaper number assumes
they do.

Not in either number: molecules with a positive charge (they need a different coupled-cluster route we have not built yet), a cheap
extra step that corrects for the basis set, and the DFT calculations for the 128 chosen molecules that do not have them yet — those
are cheap and run on the laptop.

## What happens next

How the missing anchors will be made is decided in November. This post gives that decision its two inputs: how many (167, for
three of the four families) and what that costs (between about €730 and €3,000 of rented time).

The full numbers, the scripts and the lines written before the runs are in the project's repository.
