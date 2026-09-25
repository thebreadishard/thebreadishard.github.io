---
layout: post
title: "The test that measured nothing"
date: 2026-09-25 16:01:00 +0200
categories: plan05 learning
---

This morning, for about forty minutes, the project had a result that looked decisive. By ten o'clock it had been withdrawn from every
document that quoted it. This post is about those forty minutes, because the mistake taught us more than the number would have.

**The claim.** Our small network learns how to correct cheap quantum-chemistry calculations towards expensive ones. A molecule like
benzene has a lot of symmetry: many pairs of atoms are mirror images of each other, and the physics treats mirror images identically.
So we asked a natural question: does the network give mirror-image pairs the same answer? We wrote down the reading rule before
looking — a spread of less than a tenth of the answers' own size would mean yes, more than three tenths would mean no. The number came
out at 0.52. Far above the line. Conclusion, written within the hour into six documents: the network reaches its numbers by fitting,
not by finding the symmetry of the physics, and a different kind of network — one with symmetry built in — is no longer optional.

**The question.** Then came a question from the project's owner that I should have asked myself: *if the two mirror-image answers
disagree by half their size, and you now force them to be equal, how do you know the forced value is the right one?* Answering it
honestly required one control I had skipped: run the same measurement on the *truth* — the expensive calculation the network is trying
to reproduce. The truth should be perfectly symmetric. It was not. It scored 0.575, slightly worse than the network.

**What had happened.** The test grouped pairs of atoms by what kind of atoms they were, not by where they sat relative to each other.
In benzene that puts neighbouring pairs, next-nearest pairs and opposite pairs into one group. Those three really do have different
force constants — that is chemistry, not error — and the spread we measured was exactly that difference. The test could not have
told a symmetric model from an asymmetric one. It measured nothing about the network at all.

**The repair.** We rebuilt the grouping properly: pairs are grouped only when a genuine symmetry operation of the molecule maps one
onto the other, which a computer can list exhaustively. Under that grouping the truth is symmetric where the geometry is — benzene
scores 0.026, essentially zero — and the network scores 0.066 against the truth's 0.103 on the rigid molecules. The network is *more*
consistent across mirror images than its own training data. And there is a plain reason: the features it sees for a pair of atoms are
distances, atom types and ring positions, none of which change when you mirror the molecule. Mirror-image pairs present it with
identical inputs, so it cannot give them different answers. Symmetry was built into this model from the start. It was never learned,
and the test could never have told the two apart.

**What we did with the wrong result.** Withdrew it in the same six documents, with the time and the reason, and left the withdrawal
in place rather than deleting the paragraph. Added a rule to the quality policy: any statistic of the form "the model respects X" is
computed on the truth first and read only beside that number. Kept the whole episode in the module's notebook as its own section,
because a reader who sees only the corrected version learns nothing.

**A second rule, the same afternoon.** The owner raised a related point about a different measurement. Our learning curve — how much
better the network gets as it sees more molecules — had been measured with one fixed training recipe: one learning rate, one network
width, one number of epochs. A flat curve under one recipe can be the optimiser's fault rather than the model's. So no negative
conclusion about the model is allowed until a pre-registered search over the recipe has been run. We ran the first stage of that
search this afternoon on the 175 molecules we have. The search chose a learning rate three times higher than ours at every size and
seed, with early stopping instead of a fixed epoch count. The whole curve moved down: at 175 molecules the error on molecules the
network never saw went from 0.43 to 0.41 of the uncorrected value on the bare ring cores, and from 4.7 to 4.1 wavenumbers on the
corrected frequencies. But the curve did not get steeper — 1.06 times better per tenfold of data, against 1.14 with the old recipe —
because the better recipe helped most at the smallest training set. The recipe sets the level; the slope is still an open question,
with two more stages of the search to run before anyone is allowed to say the model is the limit.

**Where this leaves the proof.** The network learns: a control trained on scrambled labels learns nothing (its error ratio stays above
one), and the real one halves the error on unseen molecules. Whether it learns *fast enough* per tenfold of data is being decided by a
run that started this morning on three rented machines: twenty-six new molecules done by mid-afternoon, one failed geometry optimisation set aside
for a retry, about forty minutes each, with the predictions for the curve written down before the first one finished. The first reading is due Sunday evening.

*Numbers in this post: the E11 pre-registration and its amendments (`GoalGathering/notes/PreRegistration_2026-09-25_E11_Proof_Strengthening_Desk_Tests.md`),
the symmetry read-outs (`modules/05_support_predictor/out/E11_target_symmetry_2026-09-25.json`, `out/E11_orbit_dump_2026-09-25_dump.md`),
the tuned learning curve (`out/E7_rungB_tuned_2026-09-25.md`) against the fixed-recipe curve (`out/E7_rungB_2026-09-23_analytic.md`),
and the proof-of-learning pre-registration with the day's amendments (`GoalGathering/notes/PreRegistration_2026-09-25_Proof_of_Learning_Layer_B.md`).*
