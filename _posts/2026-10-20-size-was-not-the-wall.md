---
layout: post
title: "Size was not the wall, and the ruler got cheaper"
date: 2026-10-20 07:00:00 +0200
categories: plan05 progress
---

<!-- Written 20 Oct 2026 at the user's request ("Ja graag", on post 24 combining the size test and the cheaper anchors); published the same
     day on the user's word ("Push de repo en publiceer"), after the plan repo was pushed so the commit links resolve.
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/PreRegistration_2026-09-25_RungC_Equivariant_vs_Pair_Model.md, chain 41's registration (11 Oct), amendment, addendum
       and outcome (19 Oct, commit a7f54e89): q5 = r_S / r_K = 0.428 / 0.406 = 1.05, lines 1.3 and 1.8; corrected omega on s5 3.97 / 3.41 /
       22.63 cm-1; control half 0.413 -> full 0.406; chain 39's q = 1.96; two s5 molecules out for imaginary modes in their FD labels;
       modules/05_support_predictor/out/read_chain41.{json,md}.
     GoalGathering/notes/PreRegistration_2026-10-19_DFMP2_Composite_Step.md and its outcome (commit 40e09a0c): W 4.6e-9 / 4.9e-8 Eh/bohr;
       swap test pyridine 0.423, naphthalene 0.460 cm-1 (line 0.5; prediction < 0.3 wrong); rows 9/33 and 30/136 s against 13/196 and
       79/1,996 s; peak RSS 3.33 GB; five anchors about 8.4 h; probes/results_m1/dfmp2_composite_2026-10-19/.
     The 98 worker-hours at 26 GB: GoalGathering/notes/PreRegistration_2026-10-12_Anchor_Set_Three.md, declaration of 19 Oct.
     Quinoxaline: probes/results_m1/dfmp2_anchors_2026-10-20/composite_quinoxaline.md (commit 190be713); registry modules/ANCHORS.md. -->

Two things held this project back in the past fortnight. One was a measurement that seemed to say our method stops working when molecules
get bigger. The other was money: the most accurate reference numbers we use, the [anchors](/glossary/#anchor), needed a rented server that we
had stopped paying for. This week both moved, and neither moved by luck: each was a test with its line written down before the result.

**The worry about size.** The method learns a correction: how a cheap calculation of a molecule's vibrations should be adjusted to look like an
expensive one. It learns from a [corpus](/glossary/#corpus) of about a thousand molecules, none with more than four fused rings and none larger
than about thirty atoms. The molecules astronomers care about are bigger, from forty atoms to a hundred and more. So the question is whether
the correction learned on small molecules still holds on large ones, where we cannot afford to teach it.

An earlier test, chain 39, said it got much worse: the error of a size-shifted test set was almost twice that of a matched control (a ratio of
1.96). But that test had a flaw we knew about: its small training molecules also had fewer rings, so a bigger molecule was always also a
molecule with a new ring count. Size and ring count were tangled together.

**Chain 41 untangles them.** We took thirty molecules with five fused rings, 32 to 40 atoms: perylene, benzo[a]pyrene, benzo[e]pyrene,
benzo[k]fluoranthene, picene and dibenz[a,h]anthracene, each with four relatives carrying small changes. One model was trained on the corpus as
it stands, at most four rings, and tested on them. A second, the control, was also given the five-ring molecules of three of the six families,
and tested on the other three. If the first model is much worse than the control, five rings must be in the training data. If they are about
equal, the correction carries over.

The measure is the share of the [ring coupling](/glossary/#ring-coupling) error that a model leaves behind, compared with making no correction
at all: 0 would be perfect, 1 would be no help. Before any number, we wrote down two lines. A ratio of the two models of 1.3 or less: the
correction carries. A ratio of 1.8 or more: steep, and the corpus would need every ring count we ever want to predict.

The result: 0.428 for the model that never saw five rings, 0.406 for the control. A ratio of **1.05** ([the read](https://github.com/thebreadishard/udacity-capstone-plan/commit/a7f54e89)). In frequencies, after the correction,
the two models are off by 3.97 and 3.41 cm⁻¹ on these molecules, where making no correction at all is off by 22.63. And giving the control
twice as many five-ring molecules barely changed it (0.413 with half, 0.406 with all), so this is not a case of the control simply having had
too few examples.

So the steep penalty of chain 39 came mostly from the new ring count, not from size itself, and here a new ring count did not hurt. Up to 40
atoms, size is not a wall.

**The cheaper ruler.** The second story is about the anchors: molecules for which we have the expensive kind of calculation,
[coupled cluster](/glossary/#coupled-cluster), the reference the network is checked and tuned against. Even that is done in two parts. The
coupled-cluster part uses a small [basis set](/glossary/#basis-set), a coarse brush, because a fine brush would be far too costly. A cheaper
method, MP2, then measures how much the answer changes from the coarse brush to the fine one, and that change is added on top.

That MP2 step was the expensive part in memory: 26 gigabytes per process, which only a rented server had. Five new anchors were finished
in their coupled-cluster part and waiting for it, about 98 server-hours, at a time when we had decided to stop renting.

There is a well-known shortcut, density fitting, which builds the huge table of electron interactions from a much smaller one. The program
we use for most of our work cannot do the derivatives we need with that shortcut; another program, psi4, can. The risk is that the shortcut
changes the answer. So the test, again written down first: take two molecules whose MP2 step we already had the old way, pyridine and
naphthalene, redo the step with the shortcut, and compare the vibrational frequencies that come out. Within 0.5 cm⁻¹ for every vibration:
adopt it. Up to 2: note the difference and carry it as an error. Beyond: refuse it.

Before that, a sanity check on water: the shortcut's derivatives agree with derivatives worked out step by step from its energies to
5 × 10⁻⁸ atomic units, against a line of 10⁻⁶.

The swap: pyridine's frequencies moved by at most **0.42 cm⁻¹**, naphthalene's by at most **0.46**. Both under the line of 0.5, so the
shortcut is adopted ([the read](https://github.com/thebreadishard/udacity-capstone-plan/commit/40e09a0c)). I had predicted less than 0.3, and that was wrong: the line holds, with less room to spare than I expected.

What it buys: one step of the fine-brush calculation for naphthalene took 136 seconds on the laptop, against 1,996 seconds the old way on a
large server. The largest memory use was 3.3 gigabytes. All five waiting anchors together come to about eight and a half hours on the laptop
we already own.

**The first one is in.** Quinoxaline, a two-ring molecule with two nitrogen atoms, went through the whole chain this morning. Its two softest
vibrations come out at 115 and 119 cm⁻¹ with the coarse brush, and at 167 and 179 with the fine-brush step added. The cheap calculation the
network starts from has them at 177 and 185. The coarse brush alone would have taught the network to pull these two down by about 60 cm⁻¹;
with the step, by about 10. Most of that big pull was the brush, not the molecule. It passed our promotion rule and is now one of seven
anchors in use ([the anchor](https://github.com/thebreadishard/udacity-capstone-plan/commit/190be713)).
The other four follow today.

**What this is not.** Chain 41 compares two density functional methods, a cheap one and a more careful one, as a stand-in for the real
target; it says the correction carries in size, not yet that the coupled-cluster correction does. It reaches 40 atoms; the 50- to
100-atom molecules are the next test, chain 42. Two of the thirty molecules left the test before any result was read, because the reference
calculations for them showed an unstable vibration; they come back when better reference calculations exist. And the density-fitting
shortcut was tested on two molecules and adopted with a margin smaller than predicted: if one of the new anchors behaves differently, the old
way is still there, on a server, when the budget allows.
