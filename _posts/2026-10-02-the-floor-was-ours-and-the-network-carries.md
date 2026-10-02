---
layout: post
title: "The floor was ours, and the network carries"
date: 2026-10-02 17:02:42 +0200
categories: plan05 progress
---

Four days without a post, and not because nothing happened. On Tuesday a network that had been stuck at the same error for a week turned out to be
stuck on something we had built ourselves. Today it met the target we set for it, and then did something we had only hoped for: it carried part of what
it learned on cheap calculations over to the expensive ones.

**The stuck week, in one paragraph.** The model's job is to predict a *correction*: the difference between a cheap description of how a molecule's atoms
pull on each other and a better one. We measure success on molecules the model never saw, as a fraction of the error you would make by predicting no
correction at all. Zero is perfect, one is useless. For a week every architecture we tried — small, large, pretrained, equivariant, hand-crafted features —
landed at 0.40 to 0.45. When five different models agree on the same wrong number, the number is not about the models.

**It was about the target.** We had asked the network to predict the correction only on a short list of pairs of internal coordinates (bonds, angles,
the stretches that make the ring breathe), one bond apart. That list was too short to *express* the true correction. We computed what the best possible
answer on that list would score, with no learning at all: 0.31. The models had been 0.1 above a ceiling we had set ourselves. Widen the list to pairs
three bonds apart and the ceiling drops to 0.09; the same network, retrained on the wider list, went from 0.42 to 0.28 overnight, and with the full
pool of 750 molecules to 0.26. Nothing about the network changed. The lesson, which we have now written down as a rule: before judging a model,
compute what a perfect model could do on the target you gave it.

**The target we set, and met.** On Tuesday we agreed what "good enough" means for this stage: on ten held-out molecules, remove at least three
quarters of the coupling error (a score of 0.25 or better) *and* get the corrected vibrational frequencies within 3 cm⁻¹ — a cm⁻¹ being the unit in
which an infrared line's position is quoted, and 3 of them being roughly the width of a sharp line. Last night's run gave 0.22 and 2.8 cm⁻¹, over three
random seeds (0.21–0.23, 2.6–3.0). A repeat this morning, with the trained models saved to disk, gave 0.22 and 2.7. The uncorrected starting point is
23 cm⁻¹. The last ingredient was almost embarrassing: a small extra term asking the network to also get the *diagonal* of the correction right, the part
that moves each line on its own. It cost nothing on the couplings and bought 0.7 cm⁻¹.

**Then the question that matters.** Everything above is learned between two *cheap* levels of theory, because that is where we can afford hundreds of
training molecules. The point of the project is the expensive level — coupled cluster, the kind of calculation that costs a rented server a day per
molecule — of which we have exactly four finished: benzene, fluorobenzene, pyridine and naphthalene. Does a network trained on the cheap correction know
anything about the expensive one?

We tested it the only honest way with four molecules: hold one out, adapt on the other three, read the fourth. Three variants of "adapt":

| held-out molecule | no correction | scale factors fitted on the 3 others, no network | network, 9 scale factors tuned | network, last layer retrained |
|---|---|---|---|---|
| benzene | 25 | 6.4 | 6.2 | 4.4 |
| fluorobenzene | 28 | 7.8 | 5.2 | 5.2 |
| pyridine | 26 | 7.3 | 6.6 | 5.2 |
| naphthalene | 26 | 10.2 | 6.0 | 10.5 (unstable) |

Numbers are the error of the in-plane ring frequencies in cm⁻¹ on the held-out molecule, averaged over the three trained models. The "scale factors" column
is the classic chemist's trick: multiply each kind of force constant by a number fitted on known molecules. It gets you from 26 to 6–10. Give the
*network* the same nine numbers to tune and nothing else, and it gets to 5–6 on every molecule, including the fused two-ring naphthalene where the
trick alone leaves 10. Let the network retrain its whole last layer and it wins on the single rings but falls apart on naphthalene, differently for each
of the three models: 257 parameters are too many to fit on three molecules. Which is the real finding: the network's internal picture of the molecule,
learned on cheap data, contains structure of the expensive correction that no per-type scale factor has. The target for this test was 3 cm⁻¹; we are at
6. The fifth anchor, anthracene, is computing as I write (three rings, 24 atoms, six and a half hours per gradient on a 32-core machine).

**What the error map says to compute next.** With fifteen records in hand we could finally ask *which* molecules the network gets wrong. The answer is
clean: the error follows the size of the ring skeleton — benzene 0.11, three fused rings 0.20, fluorene 0.3, fluoranthene 0.4 — and hardly the group
attached to it (within one skeleton, substituents move the score by at most 0.1). Our training pool has almost no four-ring molecules. So the next
two hundred, running since this morning on a rented server, are chosen for skeleton coverage, and we wrote down beforehand what should happen: the
held-out skeletons, now at 0.33, should drop to 0.28 or better. If they drop by less than 0.03, it was the count and not the coverage, and we will say so.

**Two tools that stay.** Infrared *intensities* — how dark a line is, not only where it sits — need the derivative of the molecule's dipole with respect
to every atom's position. Our first route, nudging each atom and recomputing, would have cost about eleven hours per molecule. The second route, solving the response
equations the analytic second-derivative code already solves, costs seven seconds on water and agrees with the first to five decimals; it also caught
a silent convergence error on the way, through a sum rule that must hold exactly. And we timed where a coupled-cluster gradient spends its time:
three quarters in one stage that barely speeds up with more cores. That tells us how to lay out the next server and which piece of code to rewrite.

**What I would not claim yet.** The out-of-plane and C–H stretching corrections at the expensive level are 60–100 cm⁻¹ and nothing above touches
them; that is partly a limitation of the small basis set the anchors use, and a separate question. And six is not three.

*Code and records: plan 05 in the plan repository, the commits of 1–2 October up to
[6f726d0](https://github.com/thebreadishard/udacity-capstone-plan/commit/6f726d0) (the target-bound probe, the transfer script with its tests, the error
map, the dipole-derivative probes); the day's numbers are in the rung-C pre-registration's dated outcome sections and the investigation log of 1–2 October.*
