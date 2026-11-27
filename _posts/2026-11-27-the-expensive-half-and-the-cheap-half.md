---
layout: post
title: "The expensive half and the cheap half"
date: 2026-11-27 09:00:00 +0100
categories: plan05 method
---

<!-- Draft written 27 Nov 2026 on the user's word ("Schrijf een blog over dit onderwerp ... eerst CCSD(T). Daarna pas MP2.").
Published 27 Nov 2026 on the user's word ("Na stap 0"), after step 0's outcome.
Sources (plan repo, plans/05_delta-probed-ir-pipeline):
  - (T) part per mode and its change cc-pVDZ -> cc-pVTZ: probes/results_m1/cc_ideas_2026-11-26/read_split.md (ledger 26 Nov 15:38)
  - CCSD(T)/ano-pVDZ gradient 1,215 s, 12 gradients instead of 72: probes/results_m1/cc_idea1_oop_benzene.log; outcome 6.94 against 7.12,
    per mode: cc_ideas_2026-11-26/read_idea1.md (ledger 26 Nov 19:03)
  - CCSD gradient in cc-pVTZ about 20 min, (T) lambda 75-105 min + gradient 5-9 min: ledger 26 Nov 15:38
  - carried residual on pyridine 6.24 -> 1.28, one scale factor 1.02: cc_ideas_2026-11-26/read_transfer.md (ledger 26 Nov 14:25)
  - benzene, ano-pVQZ MP2 step: 30.2 -> 7.1, 7.6 -> 3.5, 8.9 -> 8.9; 24 min per row on the laptop: TASKS 320, PreRegistration_2026-11-25_ANO_Step_Benzene.md
  - naphthalene: probes/results_m1/ano_step_naphthalene_2026-11-26/read.md (ledger 27 Nov 06:12); 82 min per row, 15 rows, 20 h 27 min
  - fitting sets: five out-of-plane rows with cc-pV5Z fitting sets, largest move +0.10 cm-1 (line 1.0):
    ano_step_naphthalene_2026-11-26/step0_five_rows/read_five_rows.md (ledger 27 Nov)
  - Au 581: ano_step_naphthalene_2026-11-26/au581_desk.txt (ledger 27 Nov 07:56)
  - function counts 114 and 510 for benzene: the post of 26 Nov
  - scaling with the 7th and 5th power: textbook (Helgaker, Jorgensen, Olsen, Molecular Electronic-Structure Theory)
  - DOIs: Semidalas & Martin 2023 10.1080/00268976.2023.2263593; Martin, El-Yazal & Francois 1996 10.1021/jp960598q; Neese & Valeev 2011 10.1021/ct100396y
-->

A molecule vibrates at fixed frequencies, and those frequencies are what a telescope sees in infrared light. To predict them
for molecules nobody has measured, I train a neural network. The network needs examples to learn from, and those examples must
be as correct as I can make them. Each example is a calculation of how stiff a molecule is against every possible small
movement of its atoms.

That calculation has two halves. One half uses the most trustworthy method I can afford, on a small scale. The other half uses a
much cheaper method to estimate what the first half would have given on a large scale. This post takes them one at a time: first
the expensive half, then the cheap one. For each: what it is, why it costs what it costs, and what this week's tests showed.

Two terms first. A frequency is written in cm⁻¹ ("wavenumbers"); benzene's vibrations lie between about 400 and 3,200 cm⁻¹,
and I am after errors of a few cm⁻¹. A *basis set* is the kit of building blocks from which a program assembles the cloud of
electrons around a molecule; a larger kit gives a better picture and costs more. [Yesterday's post](/2026/11/26/what-is-in-a-basis-set/)
is about that.

# The expensive half: CCSD(T)

## What it is

Electrons repel each other, so they avoid each other. Where one is, another is less likely to be. A cheap calculation ignores
most of this: it lets each electron feel only the average of the others. The rest, the avoiding, is called *correlation*, and
vibration frequencies depend on it.

Coupled cluster is a family of methods that puts the avoiding back in, in steps. The letters say how far it goes:

- **S and D** (singles and doubles): electrons are allowed to move out of their average places one at a time and two at a time.
  Two at a time is the important one: it is two electrons getting out of each other's way.
- **(T)** (triples, in brackets): three at a time. The brackets mean this part is estimated in one pass instead of being solved
  to the end like the first two.

Together that is CCSD(T). Chemists call it the gold standard, because for ordinary molecules it lands close to the exact answer
of the underlying equations, and nothing more accurate is affordable for molecules of this size.

## Why it costs so much

The work grows with the seventh power of the size of the problem. A molecule twice as large takes about 128 times as long. A
larger basis set hurts in the same way: the triples step alone grows with the fourth power of the number of building blocks per
atom.

Some numbers from my own laptop, eight processor cores, for benzene (twelve atoms):

- One CCSD(T) calculation of the forces on the atoms, in a small basis set of 114 building blocks: about 20 minutes.
- In a medium set of 264 blocks, the same calculation without the triples: about 20 minutes. The triples add 80 to 115 minutes.
- And one calculation is not enough. To know how stiff the molecule is, I push each atom a little and compute the forces again.
  Benzene's symmetry cuts that from 72 calculations to 12.

Benzene is the smallest molecule I care about. The ones I am after have 30 to 80 carbon atoms. The seventh power decides the
rest: CCSD(T) in a large basis set is out of reach for them, and it is barely in reach for the examples.

## Is the bracketed part worth it?

Yes. I computed benzene's six out-of-plane vibrations (the ones where atoms move out of the flat plane of the ring) with and
without the triples. The triples lower each of them by 20 to 37 cm⁻¹. For a target of a few cm⁻¹ that is not a detail.

## Three attempts to make it cheaper

All three ask the same question: can the expensive method stay in a small basis set and still be right?

**Keep only the triples small.** If the triples part came out the same in the small and the medium basis set, I could compute it
in the small one and spend the saving elsewhere. On five of the six out-of-plane vibrations it does: the change is 1.3 cm⁻¹ or
less. On the sixth, the puckering of the ring, it changes by 6.5. One vibration in six is enough to rule the shortcut out.

**Use a smarter small set.** There is a small basis set, called ano-pVDZ, with the same number of blocks as the usual one but
mixed with more care. I ran CCSD(T) in it and compared the result with a published CCSD(T) calculation in a very large basis
set (Semidalas and Martin, 2023). With the rest of my recipe unchanged, the error on the out-of-plane vibrations went from 7.1
to 6.9 cm⁻¹. That is no gain. Looking closer, the error had moved: three vibrations improved by up to 3.5, the ring puckering
got worse by 4.9.

**Compute the remaining error once, and reuse it.** Benzene is small enough for CCSD(T) in the medium basis set. So for benzene
I know what my recipe gets wrong. Atoms in a similar molecule sit in similar surroundings, so that error might be carried over,
atom by atom. I tried it on pyridine, which is benzene with one carbon swapped for a nitrogen: the error on its eight
out-of-plane vibrations fell from 6.2 to 1.3 cm⁻¹. But a control did as well: one single correction factor, fitted on benzene,
gave 1.0. So the carrying-over works here, and I have not yet shown that the atom-by-atom detail is what makes it work. That
needs a molecule where the errors do not all point the same way.

## What the expensive half taught me

A small basis set is a real limit for CCSD(T) on these vibrations. A better-mixed small set shuffles the error around; it does
not remove it. Two of the three shortcuts are closed, and the third is open with a question attached.

# The cheap half: MP2

## What it is

MP2 is the simplest method that accounts for electrons avoiding each other at all. It starts from the same cheap calculation
with averaged electrons, and then adds one correction for pairs of electrons, computed in a single pass. Nothing is solved to
the end, and nothing is done for three electrons at a time.

## Why it is cheap

Its work grows with the fifth power of the size of the problem instead of the seventh. And there is a standard trick that makes
it cheaper still: the most laborious quantities are approximated with the help of an extra kit of blocks, the *fitting set*.
The approximation is close, and the saving is large.

The same laptop, the same benzene: one MP2 calculation of the forces in a large basis set of 510 building blocks takes about
12 minutes. That is less than CCSD(T) needed for 114.

## What it is used for

On its own MP2 is not accurate enough to be the example a network learns from. But I do not ask it for the answer. I ask it for
a difference: how much do the results change when the basis set goes from small to large? MP2 answers that question twice, once
in each basis set, and the difference is added to the expensive result:

*expensive method, small set* + (*cheap method, large set* − *cheap method, small set*)

The idea is that a too-small basis set hurts both methods in much the same way, so the cheap one can measure the damage.

## What changed this week

Until now the "large" set in that formula was a medium one. The out-of-plane vibrations showed it was not enough: benzene's came
out 30 cm⁻¹ wrong against the published reference, and the ring puckering was the worst.

The repair is in the cheap half only: give MP2 a larger, more carefully mixed basis set (called ano-pVQZ; Neese and Valeev,
2011).

- **Benzene.** The error on the out-of-plane vibrations fell from 30.2 to 7.1 cm⁻¹. The stretching of the carbon–hydrogen bonds
  improved from 7.6 to 3.5. The remaining in-plane vibrations stayed where they were, at 8.9.
- **Naphthalene** (two fused rings, eighteen atoms). There is no published calculation of that quality to compare with, so the
  comparison is with measured frequencies. A calculated frequency of this kind should lie somewhat above the measured one, for a
  reason that has to do with how real bonds stretch. With the medium set, naphthalene's ring-puckering vibration lay 32.6 cm⁻¹
  *below* its measured value. With the larger set it lies 20.0 above, a rise of 52.6. All thirteen out-of-plane vibrations I
  judged now lie on the right side.

The price: one row of naphthalene's stiffness table took 82 minutes on a rented eight-core machine, and the molecule needs
fifteen rows. About twenty hours, on a machine that costs cents per hour. For CCSD(T) in a basis set of that size there is no
price to quote; it cannot be done.

## Two loose ends, checked

**The fitting sets.** The trick that makes MP2 cheap is an approximation, so I measured how much it matters. I repeated the five
rows of naphthalene's stiffness table that belong to movements out of the plane, each with a larger fitting set. The numbers in
those rows changed more than I had allowed for, and a test with made-up changes of that size had moved a vibration by 3 cm⁻¹. The
real changes did not: no out-of-plane vibration moved by more than 0.1 cm⁻¹. The ordinary fitting set is good enough here, and
the step costs no more than the price above.

**One measured value that cannot be right.** One of naphthalene's vibrations came out almost 50 cm⁻¹ above its listed measured
value of 581 cm⁻¹. Four different methods agree with each other and put it near 630. That vibration is of a kind that neither
infrared nor Raman spectroscopy can see directly, so the listed number cannot be a direct observation. I no longer judge
anything by it.

# What to remember

- The reference has two halves: an expensive method in a small basis set, and a cheap method that estimates what a large basis
  set would change.
- The expensive half, CCSD(T), is the accurate one. Its cost grows with the seventh power of the size, and its bracketed part,
  the triples, is worth 20 to 37 cm⁻¹ on benzene's out-of-plane vibrations.
- Shortcuts that keep CCSD(T) in a small basis set did not work: the error moved between vibrations instead of going away.
- The cheap half, MP2, grows with the fifth power and can afford a large basis set. Giving it a better one took benzene's
  out-of-plane error from 30 to 7 cm⁻¹ and moved naphthalene's worst vibration by 53.
- So the largest fault in the reference sat in the cheap half, and that is where it could be repaired. What remains sits in the
  expensive half, and is still there.

Sources: E. Semidalas and J. M. L. Martin, *Molecular Physics* (2023), [doi:10.1080/00268976.2023.2263593](https://doi.org/10.1080/00268976.2023.2263593).
J. M. L. Martin, J. El-Yazal and J.-P. François, *Journal of Physical Chemistry* 100, 15358 (1996),
[doi:10.1021/jp960598q](https://doi.org/10.1021/jp960598q). F. Neese and E. F. Valeev, *Journal of Chemical Theory and
Computation* (2011), [doi:10.1021/ct100396y](https://doi.org/10.1021/ct100396y).
