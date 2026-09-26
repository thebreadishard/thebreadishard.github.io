---
layout: post
title: "Which measurement next?"
date: 2026-09-26 16:11:51 +0200
categories: plan05 efficiency
---

Every molecule in this project has a table that we would love to know in full and can only afford to know in part. Its rows and columns
are the molecule's vibrations, and each cell says how strongly two vibrations influence each other. Each cell costs an expensive
calculation: for the naphthalene ion we measured one of them this week at nine and a half hours. Nobody fills that table. You measure
some cells, let the mathematics reconstruct the rest, and stop as soon as the reconstruction is good enough.

Which raises a question we had written down on 6 September and then parked: *which cell should you measure first?* The plan's answer
so far was a fixed recipe, the same for every molecule: start with the pairs of vibrations whose pitches lie close together, because
physics says those tend to interact most, then add a handful of broad, random combinations. Sensible, cheap, and blind to the molecule
in front of it.

**What we tried today.** We had, without quite noticing, already computed the full table for 289 molecules at a cheap proxy level
(two flavours of density functional theory against each other). That makes an experiment possible at zero cost in new quantum
chemistry: for a new molecule, let a small network that has only seen *other* molecules decide the order of measurement, then replay
the reconstruction cell by cell and count how many measurements it takes to get halfway to the result the whole recipe would give. The
network sees nothing of the new molecule's answer; only its cheap side: pitches, which atoms move in which vibration, the cheap
force-constant matrix. We wrote the pass line and a prediction down before the run, and we held out 97 molecules the network never
trained on, including every one of the bare aromatic rings.

**What came out.** The network's order gets a new molecule halfway with about **one fifth** of the measurements the fixed recipe needs:
ratios of 0.17 to 0.20 across three independent trainings, on 95 to 98 percent of the molecules, and just as strong on the bare rings as
on the decorated ones. The pass line was 0.80. Our own prediction, made in the morning, was 0.5 for decorated molecules and 0.7 for
bare rings; the result beat it by a factor of three. On benzene alone the numbers are almost comic: 480 measurements in the fixed order,
32 in the network's.

Because the full tables were known, we could also compute something you never get in real life: the *oracle* order, the one you would
choose if you already knew the answer. It gets halfway with about one sixteenth of the measurements. So between "a network that has
seen other molecules" and "perfect knowledge" there is another factor of three still on the table.

**Why this matters more than a nice ratio.** The expensive measurements are the project's scarce currency; the whole plan for the next
years is a budget of them. If the same budget buys five times as many molecules, or the same molecules at a fifth of the price, the
question "can a small group afford a coupled-cluster-quality spectral database?" changes shape. And the gain does not fade with
molecule size, which is where the money goes.

**The catch we found along the way, which is also the next opportunity.** Measuring across those 289 tables, only about 45 percent of
the interaction strength sits inside the "close pitches" band that the fixed recipe covers with its targeted measurements. The other
half lives between vibrations of very different pitch, presumably because they move the same atoms. The fixed recipe cannot reach that
half except by luck. A network that has learned which distant pairs share atoms could. That test, with the network allowed to choose
among *all* pairs, is running tonight on a rented server, and a second network that learns its own description of a molecule instead
of using ours is in a pre-registered search of its own. We will not judge either before the search is done.

**What this is not.** It is a proxy-level result: the tables were cheap-versus-cheap, not cheap-versus-coupled-cluster, and the real
test on measured coupled-cluster responses comes after the proposal is in. And the win exists only because we stop early: if you insist
on measuring the whole recipe anyway, every order ends at the same place. The saving is in knowing when you may stop, and asking the
right question first.

*Numbers in this post: the pre-registration and its dated amendments (`GoalGathering/notes/PreRegistration_2026-09-26_Standout_Pattern_Proposer.md`),
the read-out of the band-pool run (`modules/standout_pattern_proposer/out/sim/band_readout.md` and `band_p2_readout.md`), the in-band
share across 289 molecules (`modules/standout_pattern_proposer/out/inband_share_2026-09-26.json`), and the naphthalene-ion timing in the
project ledger (26 September, 12:3x).*
