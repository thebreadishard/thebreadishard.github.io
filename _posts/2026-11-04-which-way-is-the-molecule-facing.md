---
layout: post
title: "Which way is the molecule facing?"
date: 2026-11-04 19:29:20 +0100
categories: plan05 software
---

<!-- Written 4 Nov 2026 at the user's request ("Vandaag 2, morgen 1? Let ... goed op de lekentaal"). Published 4 Nov on the user's word ("Publiceer"). Numbers trace to
     (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     ledger 28 Oct 08:2x (the energy route as a second route on benzene, pyscf and psi4; production quartics come from displaced
       analytic pyscf Hessians, src/dpir/qff.py);
     ledger 28 Oct 12:1x (the lead: psi4 1.10 orients its grid by the nuclear-charge principal axes, libfock/cubature.cc OrientationMgr;
       benzene's in-plane moments 189.944 / 189.953; frame turn at q = ±0.4: mode 5 6.6°/5.2°, mode 11 3.2°/3.3°, mode 10 0.07°;
       psi4 mode 5 φ_iiii 849.7 at 99/590 against pyscf's 265.0);
     ledger 3 Nov 19:4x / 19:5x and probes/results_m1/frame_proof_142a_2026-11-03/read.md (commit c84d4ca8): pyscf 2.14.0, B3LYP/STO-3G,
       99/590, lab frame against charge frame: mode 5 −3.5 → 1092.8, mode 11 12.4 → 132.2, mode 10 132.3 → 132.5; three registered rules hold;
     ledger 3 Nov 20:3x / 20:4x (750 of 972 pool molecules on analytic labels; 222 still on psi4 finite differences, queued: TASKS 171;
       TASKS 142 (c), a psi4 issue, parked on the user's word);
     TASKS_closed 174 and ledger 4 Nov 08:4x, commits 648a001e (fix) and 63ab66bf (redo; probes/results_m1/coriolis_redo_2026-11-04/compare.md):
       found by TASKS 173's Psience bridge; water +23.0 cm⁻¹ on two modes against Psience; 4-methylimidazole ≤ 0.04, S2's 18–20-atom
       molecules ≤ 0.005, benzene 0.000; code since 21 Sep; the one Coriolis test used psi4 data in psi4's principal orientation;
       rotation test 1e-6; water within 0.003 / 0.011 / 0.012 cm⁻¹ of Psience 1.6.1; 72 reports and 28 label sets rebuilt; reads move
       ≤ 0.04 cm⁻¹; 0 verdict fields changed; one GVPT2 label (A_90df3e4aa9 mode 29) 1284.51 → 1279.00, a near 50/50 polyad member. -->

A molecule floating in space does not care which way it faces. Turn it a quarter turn, or upside down, and it is still the same molecule
with the same energy and the same [infrared spectrum](/glossary/#infrared-spectrum). A computer program, however, has to write the
positions of the atoms down as numbers along three axes, x, y and z, so it must choose which way those axes point. A good program gives
the same answer whatever it chooses.

This week we found two places where that went wrong. The first was in Psi4, someone else's program. The second was in our own code.
Neither of them changed a result of this project. Both are worth telling, because they show how a small choice about axes can hide in
a calculation for weeks.

## Part 1: a grid that turned with the molecule

### What an integration grid is

Our cheaper calculations use [density functional theory](/glossary/#dft), DFT for short. DFT finds a molecule's energy by adding up a
formula over all of the space around the atoms. A computer cannot add up infinitely many points, so it picks a finite set of points
around every atom and adds up the formula only there. That set of points is called the
[integration grid](/glossary/#integration-grid).

You can compare this with estimating the area of a pond. You lay a net of evenly spaced pins over the map and count how many pins fall
in the water. The answer is close, but not exact, and it changes slightly if you shift or turn the net. A DFT grid has the same kind of
small error, and that error also changes slightly when the grid turns against the atoms.

### Why Psi4 turns its grid

The makers of [Psi4](/glossary/#psi4) wanted the energy to stay the same when a user turns a molecule. Their solution: before
calculating, Psi4 lines its grid up with the molecule itself. If the molecule turns, the grid turns along with it, so the pins land on
the same spots and the small error stays the same.

To do that, Psi4 needs a direction that belongs to the molecule. It uses what we can call the molecule's **charge axes**. Imagine every
atomic nucleus as a small weight, heavier the more charge it has. Every object made of weights has three natural axes, the way a book
has a long side, a short side and a thickness. Psi4 points its grid along those three axes.

### Where that goes wrong: benzene is almost round

Benzene is a flat ring of six carbon atoms, a regular hexagon. Within its plane it is almost round. Its two in-plane axes are nearly
equal: in our numbers 189.944 against 189.953, a difference of less than one part in ten thousand. For a book that would mean the long
side and the short side are the same length. Which direction counts as "long" is then decided by a hair.

A molecule vibrates. In each [normal mode](/glossary/#normal-mode), all atoms move together in a fixed pattern. When benzene moves a
little along one of these patterns, the hair changes and the charge axes can swing round by several degrees, while the atoms themselves
have hardly moved. For vibration number 5, a small push turns the axes by 6.6 degrees one way and 5.2 degrees the other way. The grid
turns with them. For vibration number 10, the same push turns the axes by only 0.07 degrees.

### Why that matters for a spectrum

A vibration behaves like a spring. A spectrum needs two things from that spring: how stiff it is, and how its stiffness changes when
the atoms move further apart. The second part is called the fourth-power term. It is small, but it shifts the bands of the spectrum.

There are two ways to get these numbers. The direct way, [analytic derivatives](/glossary/#analytic-derivative), lets the program
calculate the stiffness from its formulas in one go. The indirect way, [finite differences](/glossary/#finite-differences), pushes the
atoms a little along the vibration, calculates the energy at each step, and fits a curve through those energies. The project mostly uses
the direct way. On 28 October we used the indirect way on purpose, as a second route to check benzene's numbers, once in PySCF and once
in Psi4.

The indirect way is very sensitive to bumps in the curve. If the grid turns between two steps, each energy carries a slightly different
grid error, and the curve gets small bumps that have nothing to do with the molecule. For vibration number 5 of benzene, Psi4's curve
gave a fourth-power term of 849.7, where PySCF, another program, gave 265.0 (both in [wavenumbers](/glossary/#wavenumber), cm⁻¹, the
unit of the spectrum).

### From suspicion to proof

On 28 October we had a suspicion, not a proof: the order matched. The vibrations whose axes turned most were the ones where Psi4 missed
most, and vibration 10, whose axes hardly turned, was the one where Psi4 agreed with PySCF.

On 3 November we tested the suspicion inside one program, so that nothing else could differ. [PySCF](/glossary/#pyscf) normally keeps
its grid fixed in space. We made PySCF calculate the same 25 energies twice. The first time it used its normal fixed grid. The second
time we first turned each geometry onto its own charge axes, the way Psi4 does. Turning a whole molecule does not change its true energy,
so any difference between the two runs had to come from the grid.

Before running, we wrote down what would count as proof: vibrations 5 and 11 should change a lot, vibration 10 should hardly change,
and vibration 5 should change more than 11, because its axes turn further. The results:

| Vibration | Axes turn by | Fourth-power term, fixed grid | Fourth-power term, grid on charge axes |
|---|---|---|---|
| 5 | 6.6° / 5.2° | −3.5 | 1,092.8 |
| 11 | 3.2° / 3.3° | 12.4 | 132.2 |
| 10 | 0.07° | 132.3 | 132.5 |

All three rules held. The same program, with the same method and the same grid, gives a very different curve once its grid turns with
the charge axes. Psi4's misses on benzene have the size and the direction you expect from this effect alone.

### What this means

Psi4's energies are not wrong in the usual sense. The turning trick was meant to remove a small error, and for most molecules it does.
For almost round molecules such as benzene, the trick makes the error jump from one step to the next instead.

For our project this was already handled, for two reasons. First, the project's first collection of molecules was calculated in Psi4
by finite differences, so those numbers could carry this effect. Since then most molecules that train our network get their stiffness
from PySCF by the direct way, without steps. That is true for 750 of the 972 molecules in the training set. The last 222 are queued on a
server. Second, where we do need steps, for the fourth-power terms, PySCF calculates the full stiffness directly at each pushed
geometry, and PySCF keeps its grid fixed in space, so the grid does not turn between the steps. We have not reported this to Psi4
yet. Our evidence is indirect: Psi4 has no switch to turn the orientation off, so we could only copy its behaviour in another program.

## Part 2: our own turn

### What the Coriolis term is

A molecule does not only vibrate. It also tumbles through space. When atoms move while the molecule rotates, they feel a sideways push.
This push is the same Coriolis effect that makes a ball rolled across a turning merry-go-round seem to curve. In a molecule, that push
couples the vibrations to each other a little, and it shifts the bands by a small amount.

Our method for these shifts is called [VPT2](/glossary/#vpt2). It corrects the "ideal spring" picture of a vibration for the ways real
bonds stiffen and soften. One of its terms is the [Coriolis term](/glossary/#coriolis-coupling). That term multiplies two ingredients
for each of the three axes:

- how strongly the rotation around that axis couples two vibrations;
- how easily the molecule spins around that axis (its rotational constant).

Both ingredients must refer to the same axis. Otherwise the term multiplies the coupling around one axis with the spin around another.

### The mistake

Our code calculated the couplings around the x, y and z axes of the file the molecule happened to be stored in. It took the rotational
constants around the molecule's own axes, sorted from the easiest spin to the hardest. These two sets of axes are the same only when
the stored molecule already lies along its own axes, in exactly that order.

The code had been like this since 21 September. Our only test of the Coriolis term used data from Psi4, and Psi4 always turns a
molecule onto its own axes before saving it. So in the one test we had, the molecule lay exactly right, and the mistake could not show.
Psi4's habit of turning molecules, the subject of Part 1, had hidden our own error.

### How we found it

On 4 November we ran our own calculations through a second, independent program, Psience (version 1.6.1), to check something else. For
water, two of the three bands differed from Psience by 23.0 cm⁻¹. That is far too large to be rounding, and it led straight to the
mixed-up axes.

### Why our results barely moved

The size of the Coriolis term depends on the rotational constant, and that constant depends on the molecule's size. Water is small and
light, and it spins easily, so the term is large. The molecules of this project are big rings of carbon, and they turn slowly, so the
term is small. A wrong pairing of axes therefore mattered a lot for water, and very little for our molecules. For
4-methylimidazole (12 atoms) the bands moved by at most 0.04 cm⁻¹. For our molecules of 18 to 20 atoms they moved by at most
0.005 cm⁻¹. For benzene they did not move at all.

### The repair

The repaired code calculates the couplings around the same axes, in the same order, as the rotational constants. A new test turns a
molecule through space and checks that every band stays the same to one millionth of a wavenumber. That is exactly the property the old
code lacked. Water now agrees with Psience to within 0.012 cm⁻¹ on all three bands.

Then we redid everything the term had touched: 72 stored calculations and 28 sets of band labels, followed by every reading we had made
from them, once with the old inputs and once with the new ones. The largest change in a score was 0.04 cm⁻¹. Not one verdict changed.

One band label did move by 5.5 cm⁻¹. That band is a near-even mix of two vibrations, and which of the two gets the label can flip on a
tiny change. Its underlying value did not move.

## The common lesson

Both problems were about axes. A molecule's real answer does not depend on which way it faces, so there is a simple check: turn the
molecule, and see that nothing changes. Our new test does exactly that. The pyscf experiment of Part 1 is the same check, applied to
another program's grid.

The code in both parts was written and tested with an AI assistant, Claude, under my direction, like the rest of the project's
software. The fix is in commit [648a001e](https://github.com/thebreadishard/udacity-capstone-plan/commit/648a001e), the redo in
[63ab66bf](https://github.com/thebreadishard/udacity-capstone-plan/commit/63ab66bf), and the grid experiment in
[c84d4ca8](https://github.com/thebreadishard/udacity-capstone-plan/commit/c84d4ca8).
