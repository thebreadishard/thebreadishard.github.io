---
layout: post
title: "The software under the science"
date: 2026-10-27 03:46:36 +0100
categories: plan05 software
---

<!-- Written 27 Oct 2026 on the user's request ('een blog over de softwareontwikkelingskant van ons project ... een korte inleiding en dan
     een lijst met de PR's, met een of twee zinnen per PR voor de leek'); the AI paragraph on the user's word ('Absoluut: dat de code met
     een AI-assistent is geschreven en getest'); published 27 Oct on the user's word ('Publiceer maar'), the four unsubmitted PRs kept
     at the end on purpose (the user: they show that you do not know in advance whether a contribution will succeed); to be extended later.
     Sources: the pull requests and issues themselves (gh search prs/issues --author thebreadishard, 27 Oct 2026, 03:2x); the merge dates
     are GitHub's. The four unsubmitted PRs: CapstonePlan, plans/05_delta-probed-ir-pipeline/GoalGathering/notes/PR_Drafts_2026-10-27_pyscf.md
     (benzene/cc-pVTZ 28 GB per array; H2O+/aug-cc-pVTZ peak 4.09 -> 3.38 GB; benzene+ 22 GB per array, acenaphthylene+ 830 GB; the
     benzene+/cc-pVDZ gradient at a peak of 6.1 GB). Six days lost to the CCSD lambda: CapstonePlan CLAUDE.md, 'the lambda incident of
     29 September 2026'. #3470's 16x / 14x: its title. -->

This project stands on software other people wrote and give away for free. Almost all of our quantum chemistry runs on
[PySCF](/glossary/#pyscf), a Python library kept up by a group of researchers and volunteers. Next to it we use a handful of smaller
packages: one for anharmonic corrections, one for geometry optimisation, one for a fast approximate method.

Software that many people use still has corners that nobody has walked into yet. Our calculations are large and unusual enough that we
walked into several. Each time we had two choices: work around the problem in our own code, or fix it at the source so that the next
person does not hit it. Where we could, we fixed it at the source. Each fix goes to the maintainers as a *pull request*: a proposed
change, usually with a test that fails without the change and passes with it. The maintainers read it, ask questions, and decide whether to
take it.

One thing should be said plainly. The code in these pull requests was written and tested by an AI coding assistant, Claude Code,
which works on this project with me. I decide what we take on and hand in the pull requests; tracking down the cause, writing the
change and proving it with tests is its work.

So far that has been nineteen pull requests to six projects. Eleven have been accepted, two are waiting for review, two we withdrew
because a better fix came along, and four are ready but not yet submitted. Below is each of them, in a sentence or two.

## PySCF

- **[#3469](https://github.com/pyscf/pyscf/pull/3469), accepted 6 October.** When you ask PySCF for the
  [gradient](/glossary/#analytic-derivative) of a [CCSD(T)](/glossary/#ccsd-t) energy without first computing one particular
  intermediate, it quietly used the intermediate of the simpler CCSD method. The result looked fine but was the slope of a different
  energy. We lost six days of computing before we noticed; now PySCF computes the right intermediate by itself.
- **[#3470](https://github.com/pyscf/pyscf/pull/3470), accepted 6 October.** Two of the slowest steps of the CCSD(T) gradient were
  written in Python. We rewrote them in C, which made them 16 and 14 times faster on benzene.
- **[#3477](https://github.com/pyscf/pyscf/pull/3477), accepted 12 October.** On its way to the gradient, the calculation builds the
  electron density, and from that density the [dipole moment](/glossary/#dipole-moment) follows almost for free. PySCF threw the
  density away. PySCF now keeps it, so every gradient also gives the dipole moment, which decides how bright an infrared line is.
- **[#3507](https://github.com/pyscf/pyscf/pull/3507), accepted 16 October.** A computed [Hessian](/glossary/#hessian) should be
  symmetric, but small numerical errors make its two halves differ slightly. PySCF's frequency routine read only one half, so the
  answer depended on which half happened to carry the error. It now averages the two halves first.
- **[#3531](https://github.com/pyscf/pyscf/pull/3531), accepted 22 October.** Inside a Hessian calculation a large set of equations is
  solved step by step ([CPHF](/glossary/#cphf)). That solver never wrote its progress to the log, so a run that was stuck looked exactly
  like a run that was working. It now reports every step. We had first reported the problem as
  [#3530](https://github.com/pyscf/pyscf/issues/3530).
- **[#3508](https://github.com/pyscf/pyscf/pull/3508), withdrawn.** The same CPHF solver sometimes stops before it has reached the
  accuracy it was asked for, a problem another user had reported in [#2702](https://github.com/pyscf/pyscf/issues/2702). We
  added measurements to that report and proposed a fix, then withdrew it in favour of a fix by another contributor that repairs the
  solver itself, for every part of PySCF that uses it.

## PySCF-forge (PySCF's add-ons)

- **[#212](https://github.com/pyscf/pyscf-forge/pull/212), accepted 30 September.** The add-on for
  [local correlation](/glossary/#local-correlation) methods stopped working with the newest versions of PySCF and of NumPy, the
  number-crunching library underneath. Two small changes made it work again.
- **[#213](https://github.com/pyscf/pyscf-forge/pull/213), accepted 9 October.** When you install the add-ons, the build looks up
  where PySCF lives. It could ask a different Python than the one doing the installing, and then build against the wrong copy of
  PySCF. It now asks the right one.

## PySCF properties

- **[#16](https://github.com/pyscf/properties/pull/16), waiting for review since 11 October.** The infrared routine crashed when you
  handed it your own orbitals instead of letting it compute them; only the default route worked.
- **[#17](https://github.com/pyscf/properties/pull/17), waiting for review since 11 October.** The existing tests of the infrared
  intensities compare with a commercial program. This PR adds a test that needs no other program: it checks the routine against
  [finite differences](/glossary/#finite-differences), small steps of the atoms with the dipole moment computed at each step.

## pyVPT2 (anharmonic corrections)

- **[#59](https://github.com/philipmnel/pyvpt2/pull/59), accepted 19 October.** pyVPT2 has a built-in check that is meant to warn
  when its numbers are noisy. The check compared two numbers that were equal by construction, so it could never fire. It now compares
  two independent routes to the same number. This PR replaced our first attempt, [#58](https://github.com/philipmnel/pyvpt2/pull/58),
  which only reported the problem; the report itself is [#57](https://github.com/philipmnel/pyvpt2/issues/57).
- **[#61](https://github.com/philipmnel/pyvpt2/pull/61), accepted 19 October.** When two vibrations share almost the same energy they
  mix (a [Fermi resonance](/glossary/#fermi-resonance)), and such groups have to be solved together. The solver could crash, or treat
  one connected group as two separate ones. It now merges them. Report: [#60](https://github.com/philipmnel/pyvpt2/issues/60).

## optking (geometry optimisation)

- **[#116](https://github.com/psi-rking/optking/pull/116), accepted 13 October.** An optimisation could hang forever, using a full
  processor core and printing nothing, when an angle close to zero was mistaken for a straight line. optking now recognises that case
  and starts again cleanly. Report: [#115](https://github.com/psi-rking/optking/issues/115).

## dxtb (a fast approximate method)

- **[#278](https://github.com/grimme-lab/dxtb/pull/278), accepted 23 October.** The forces on the atoms came out wrong when the
  calculator reused results it had stored for an earlier geometry. It now clears those stored results when it gets a new geometry.
  Report: [#277](https://github.com/grimme-lab/dxtb/issues/277).

## Ready for PySCF, not yet submitted

These four are about *[cations](/glossary/#cation)*, molecules that have lost one electron. Many PAHs in space are cations, and the
most accurate method we use, CCSD(T), ran out of memory on every cation beyond the smallest. These four PRs have not been reviewed
yet, so they may still change or be turned down.

- **Empty block.** The gradient code works through the [basis functions](/glossary/#basis-set) in blocks. When an atom's first group of
  functions was wider than one block, the first block came out empty and the calculation crashed. We met it on benzene with a large
  basis set.
- **CCSD(T) tables on disk.** PySCF has a route that is meant to keep the biggest tables on disk instead of in memory. For the CCSD(T)
  gradient it still built three of them in memory, 28 GB each for benzene with a large basis set. They now go to disk.
- **The same for cations.** The cation version of the same calculation had the same problem. On a small test molecule the memory peak
  drops from 4.1 to 3.4 GB.
- **CCSD(T) for cations in pieces.** The cation CCSD(T) gradient built several tables of about 22 GB each for the benzene cation, and
  830 GB each for a three-ring cation. The work is now done in blocks small enough to fit. The whole gradient of the benzene cation
  ran on a laptop with a memory peak of 6.1 GB.
