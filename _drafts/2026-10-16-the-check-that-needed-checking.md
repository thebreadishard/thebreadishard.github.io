---
layout: post
title: "The check that needed checking"
date: 2026-10-16 15:00:00 +0200
categories: plan05 progress
---

<!-- Written 16 Oct 2026 at the user's request ("Ja, schrijf post 23 maar als concept"); a draft until the user's word.
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     GoalGathering/notes/Design_2026-10-12_Residual_Checked_APT.md (the solver 7e-5 to 1e-4 off its system; benzene 0.06 cm-1 on the Hessian,
       4.3e-4 e on the charge table, heights <= 0.6 %; refined 3.75e-5; the field route's own error on water 9e-6 at F = 1e-3),
     probes/results_m1/apt_refine_install_2026-10-12/REGISTRATION.md (lines L1-L4 fixed 12 Oct before the runs; water 7.5e-6 refined against
       6e-5 unrefined; the per-column fallback of 13 Oct; L4 read and diagnosis D1 registered 16 Oct, commit a90e817e; D1 read, commit b994f21c),
     .../recompute/recompute.jsonl (49 molecules: residual before 6.3e-5 to 6.6e-4, after <= 1e-6; table change median 7.5e-4, max 4.8e-3 e;
       heights max 0.57 % of the strongest band; commit b3baf2e5),
     .../l4/read.{json,md} (the five molecules), .../l4/d1.{json,md} (slope 4.01, Richardson 5.7e-6 e),
     .../repro_2702_large.log and reply_2702_followup_draft_2026-10-16.md (16,305 s against 191,767 s; posted on pyscf/pyscf#2702). -->

Two posts ago this blog tested whether the network gets the *heights* of infrared lines right. Behind every height sits a table: for each
atom and each direction, how much the molecule's electric charge shifts when that atom moves. This post is about how that table is computed,
how we found it was quietly a little wrong, and how the check we built to prove the repair turned out to need checking itself.

**Solving by guessing better and better.** To fill the table, a program has to answer a large set of linear equations: how the electrons
rearrange when an atom is nudged. Equations that size are not solved in one go. The program guesses, plugs the guess back in, measures how
far off it is (the *residual*), improves the guess, and stops when the residual is small enough.

The program we use, pyscf, solves all the directions of a molecule together. We found that for our kind of calculation it stops while its
answer is still off its own equations by 10⁻⁴ or more, whatever accuracy you ask for, and without a warning. The line *positions* do not
notice: on benzene they move by 0.06 wavenumbers, far below anything we care about. The charge table does. On benzene it was off by
4.3 × 10⁻⁴ of an electron charge, which moves the heights of the lines by up to 0.6 %. Small, but a table that is wrong without anyone knowing
is not one to build on.

**Check the answer, not the solver.** The repair does not trust the program's "done". After the solver stops, we plug its answer back into
the equations ourselves and measure the residual. If it is above 10⁻⁶, we solve for the correction and add it. Where that stalls too, we
solve the stubborn directions one at a time, which has got there every time so far. A table whose residual stays above the bound is marked as failed
rather than used. On benzene the repaired table comes within 3.8 × 10⁻⁵ of an independent reference, against 4.3 × 10⁻⁴ before.

We then recomputed the tables of the 49 molecules our label server had already finished. Before the repair their residuals lay between
6 × 10⁻⁵ and 7 × 10⁻⁴; after it, all were below 10⁻⁶. The tables moved by 7.5 × 10⁻⁴ of a charge for the typical molecule and by
4.8 × 10⁻³ at most. No line's height changed by more than 0.6 % of the strongest line. The 49 repaired files replaced the old ones on the
server; the old ones are kept beside them.

The early stop was already a known problem in pyscf, filed as [issue #2702](https://github.com/pyscf/pyscf/issues/2702). We added our
measurements there. A proposed fix in pyscf does reach the right answer, but on our largest test molecule it took about twelve times as long
as the early-stopping solver. Our check stays in place either way: plugging the answer back in costs one extra step.

**A second road.** A repair is only as good as its proof, so before the run we wrote down a second check. As in the post on brightness, the
same table can be measured another way: switch on a weak electric field, push it one way and then the other, and see how the forces on the
atoms change. That road shares nothing with the equations above. The line: on five of the 49 molecules, chosen by a fixed rule (the two
whose tables changed most, then the first, middle and last of the rest), the two roads must agree within 10⁻⁴ of a charge.

Four of the five agreed, within 3.5 × 10⁻⁵ to 8.3 × 10⁻⁵. The fifth did not: **1-cyano-3-nitronaphthalene** disagreed by 3.0 × 10⁻⁴, three
times the line.

**Which of the two is wrong?** The tempting readings were both available: "the repair failed on this molecule", or "the check is not good
enough". Either can be argued after the fact, which is why neither was allowed to decide.

The electric-field road has an error of its own. It measures a slope by comparing a push one way with a push the other way, and the size of
the push leaves an error that grows with the push squared: double the push and that error becomes four times larger. If our table were the
one that is off, doubling the push would leave the gap roughly where it was. So, before running anything, we wrote down the test and its
reading:
- run the field road again with twice the push;
- if the gap grows about fourfold (a factor between 3 and 5), and the two pushes combined to cancel the squared error agree with our table
  within 10⁻⁴, the field road's own error explains the miss;
- if the gap stays about the same, our table is in doubt and gets solved again, a thousand times more tightly.

The result: the gap grew from 3.0 × 10⁻⁴ to 1.19 × 10⁻³, a factor of **4.01**. Combining the two runs to cancel the squared error leaves a
difference of 5.7 × 10⁻⁶ from our repaired table, well inside the line.

**Why that molecule.** 1-cyano-3-nitronaphthalene carries two groups that pull hard on the electrons. A field shifts its charge easily, and
not in proportion to the field's strength, which is exactly what makes the field road's error large. On water, the same road at the same
push was off by 9 × 10⁻⁶; here it was off by about 3 × 10⁻⁴, some thirty times more. The check was fine for the molecules it was tried on
before, and not for this one.

**What we take from it.** A check is a calculation too, with an error budget of its own. When it disagrees with what it checks, the first
question is which of the two is out of its depth, and a written-down test can answer that where an argument cannot. From now on, a
field-road check on a strongly polar molecule uses two pushes, or it is testing the field road rather than the table.

**What this is not.** Five molecules out of 49, none larger than 22 atoms, all with every electron paired. Both roads use the same
quantum-chemical method, so their agreement shows the equations were solved properly, not that the method's charges are the true ones:
that is a different test, against coupled-cluster charges, and it is the one the post on brightness was about. And the label server now
writes the repaired table for every molecule it finishes.
