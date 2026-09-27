---
layout: post
title: "The plan matters more than the order"
date: 2026-09-27 08:38:57 +0200
categories: plan05 efficiency
---

Yesterday's post ended with an open question. A small network had learned to choose the *order* in which we measure the cells of a molecule's
interaction table, and it needed about one fifth of the measurements the fixed recipe needed. But we had also noticed that the fixed recipe only ever
looks at pairs of vibrations with similar pitch, and that only about 45 percent of the interaction lives there. The overnight run let the network choose
among *all* pairs. Here is what came out, in three parts, in decreasing order of surprise.

**The plan matters more than the order.** When the candidate list contains every pair, the plain fixed recipe, run to the end, gets the reconstruction
error down to 0.08 on the large molecules we had held out. The band-limited plan, run to *its* end, stalls at 0.53. Same mathematics, same molecules,
same solver; the only difference is which cells were allowed on the list. Put differently: the band plan, however cleverly ordered, cannot buy a good
answer, because half of what it needs is not on the menu. Yesterday's factor of five was real, but a good part of it was the fixed recipe being blind,
not the network being brilliant. Widening the plan is the larger lever, and it is a design change, not a training run.

**On the wider plan, order still pays, modestly.** To reach a reconstruction error of 0.3, the fixed order needs about 2,950 measurements on a typical
held-out molecule, the learned order about 1,830, the oracle (which knows the answer) about 610. So the network buys a factor 1.6 over the fixed order,
and there is still a factor 3 between the network and perfect knowledge. Against yesterday's band-limited numbers this is less dramatic, but it is the
honest number for the plan we will actually use: reach every pair, let the network order them, stop when the held-out error says so.

**Where does the remaining factor 3 come from?** We tested one hypothesis overnight. The oracle has two advantages: it knows the molecule, and it sees
the answer while it measures. A real measurement campaign can imitate the second: after every batch the reconstruction says which pairs look large, and
the order can be re-ranked on the fly. We built that, ran it on the 97 held-out molecules, and got a clear answer. Feedback helps the *blind* order
(about 20 percent fewer measurements), but on top of a network that already predicts well it only improves the whole curve by a few percent and does not
move the point where you may stop. The gap to the oracle is knowledge the network lacks, not feedback it is denied. That tells us where to invest: more
molecules (the training set doubles this week), pretraining the network on a large public set of force constants, and giving it a cheap estimate of the
very quantity it has to guess, the same trick the whole project rests on, one rung lower.

**Two smaller things from the same night.** The expensive coupled-cluster labels we measured on naphthalene over the past week turned out to be smooth to
about 0.04 millionths of a hartree per energy, two parts in a hundred thousand of the signal; what a simple fit first flagged as noise was a genuine
higher-order term. And the naphthalene *ion* now has a measured price: 9.6 hours per energy on the rented machine, so both cations the project needs have
a number instead of a guess.

**What this is not.** Still proxy-level: two cheap methods against each other, not cheap against coupled cluster. The wider plan, the stop rule and the
cheap-estimate input are written down as pre-registered tests and will be built after Monday's conversation, not before.

*Numbers in this post: the pre-registration's outcome sections of 27 September (`GoalGathering/notes/PreRegistration_2026-09-26_Standout_Pattern_Proposer.md`),
the read-outs `modules/standout_pattern_proposer/out/sim/all_p2_readout.md` and `band_p2s1A_readout.md`, the noise reading
`probes/results_m1/NOISE_OPTION_B_2026-09-27.md`, and the obstacle-9 pre-registration for the cation price.*
