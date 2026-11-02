---
layout: post
title: "Six stations"
date: 2026-11-02 17:08:32 +0100
categories: plan05 pipeline
---

<!-- Written 2 Nov 2026 at the user's request ("Ja, schrijf het concept maar"; TASKS 167). Published 2 Nov on the user's word ("Publiceer maar"). Numbers trace to
     (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     probes/pipeline_show_summary.py and its record probes/results_m1/pipeline_show_summary_2026-11-02.md (commit efd796cb): the 38 runs the
       show links to, per role: hold-out (a) 8 runs, 12–26 atoms, median rms 1.64, largest 3.22; hold-out (b) 4 runs, 23–28 atoms, median
       2.39, largest 2.73; catalogue only 26 runs, 22–29 atoms, median 1.48, largest 4.60; median overlap 0.993 / 0.984 / 0.993; laptop
       0.8–2.5 s per run; corpus steps 3–4 7–10,914 s, median 5,669 s; four runs above 3 cm⁻¹ (3.22, 3.09, 3.90, 4.60);
     modules/08_industry_synthesis/m08/show.py and website/export/pipeline_show.py (commit 63063265): the six stations and their tools;
     probes/results_m1/pipeline_holdout_a_2026-11-02 (commit 344ff57b): hold-out (a) through the pipeline;
     ledger 30 Oct 01:0x (atom order: another SMILES writing moves phenanthrene's corrected bands by up to 2.92 cm⁻¹, B3LYP by 1e-11),
       30 Oct 02:3x (phenanthrene at pipeline 0.2: 4,545 s, 66 bands; all modes network 1.17 / B3LYP 23.48 cm⁻¹ against ωB97X),
       30 Oct 02:4x (the dihedral wrap: ≤ 0.12 cm⁻¹ of rms on hold-out (a)),
       30 Oct 15:4x (the seen check: 2 of hold-out (a)'s 10 molecules trained on as their layer-B twins; hold-out (b) clean, 0 of 39);
     decision 65 (licence: a family is licensed where the error is ≤ 3 cm⁻¹ on both hold-outs; 'other' 3.62 / 3.89, not licensed). -->

The [Spectrum Atlas](https://thebreadishard.github.io/spectrum-atlas/) has a new page:
[the pipeline show](https://thebreadishard.github.io/spectrum-atlas/pipeline/). It follows one molecule from its name to its predicted
[infrared spectrum](/glossary/#infrared-spectrum), step by step, and ends with a certificate that says how far the prediction can be
trusted. This post explains what you see on that page and why each step is there.

## What the page shows

The page has three columns. On the left you pick a molecule, sorted by its number of carbon atoms. In the middle the pipeline is drawn
as a metro line with six stations, and the stations light up as the molecule passes through them. On the right the spectrum appears,
with the certificate under it.

Every number on the page is read from the files of a real run. The page itself contains no numbers that someone typed in. If a run had
gone wrong, the page would show the wrong result, not a nicer one.

Molecule pages in the Atlas now link to the show. Where the Atlas lists a molecule that went through the pipeline, its page has a card
"Through the pipeline" that opens the show at that molecule. Phenanthrene is
[one example](https://thebreadishard.github.io/spectrum-atlas/molecule/A_541c53d1a5/).

## The six stations

**1. Molecule.** The input is a short line of text that describes the molecule, a SMILES string. The pipeline first checks whether the
molecule is one it may answer for: neutral, with paired electrons, and made only of elements the network has seen in training. That
check happens before any computation, so a molecule outside the network's range costs nothing.

**2. Start geometry.** A free chemistry library, RDKit, turns the text into a first three-dimensional shape.

**3. Optimisation.** A quantum chemistry program, [psi4](/glossary/#psi4), moves the atoms until the forces on them vanish. This gives
the molecule's resting shape, its [equilibrium geometry](/glossary/#equilibrium-geometry), with the cheap method
[B3LYP](/glossary/#b3lyp).

**4. Hessian.** A second program, [PySCF](/glossary/#pyscf), computes how stiff the molecule is in every direction, the
[Hessian](/glossary/#hessian), and how much each vibration changes the molecule's charge distribution. The stiffness decides where the
bands of the spectrum lie. The charge change decides how strong each band is.

**5. The network.** Here the project's own part comes in. The [neural network](/glossary/#neural-network) predicts a
[correction](/glossary/#correction) to the cheap stiffness. Three copies of the network, trained from different random starts
([seeds](/glossary/#seed)), each give their correction, and the pipeline adds their average to the B3LYP Hessian. Where the three copies
disagree, the certificate shows the spread.

**6. Spectrum and certificate.** From the corrected stiffness the pipeline computes the positions of the bands, and from step 4 their
heights. Then it writes the certificate: for every band, which family of vibrations it belongs to, how large the network's error was
on molecules it never saw, and whether that family is licensed.

## What the network corrects towards

The network does not yet correct towards the expensive reference this project is about, [coupled cluster](/glossary/#coupled-cluster).
For now it corrects B3LYP towards [ωB97X](/glossary/#wb97x), a more careful [DFT](/glossary/#dft) method that we can afford to compute for
hundreds of molecules. ωB97X plays the role of the better method, a [proxy](/glossary/#proxy), so that we can test whether the whole
machine works before we pay for the real reference.

That choice also gives every molecule in the show a known right answer, because the [corpus](/glossary/#corpus) holds an ωB97X
calculation for each of them. So the certificate can put the network's spectrum next to the right answer and give the difference.

## How close it comes

The show holds 38 molecules. None of them was used to train the network. They fall into three groups.

Eight molecules belong to the first [hold-out set](/glossary/#hold-out-set), hold-out (a): the molecules we set aside before training
to measure the error. They have 12 to 26 atoms. Their band positions are on average 1.64 [wavenumbers](/glossary/#wavenumber) from the
ωB97X answer (the median over the eight), and 3.22 in the worst case.

Four belong to the second hold-out set, hold-out (b), with 23 to 28 atoms. Their median is 2.39 wavenumbers, their worst 2.73.

The other 26 are in the catalogue but were never part of training or of either hold-out set. Their median is 1.48 wavenumbers, their
worst 4.60, for a naphthalene with two nitro groups.

To give a sense of scale: for phenanthrene, B3LYP alone is 23.5 wavenumbers from ωB97X on average. With the network's correction the
distance is 1.2.

Thirty-four of the 38 molecules come within 3 wavenumbers on average. That average includes the bands the certificate does not license.
The licence works per family of vibrations, not per molecule. A family is licensed where the network's measured error is at most
3 wavenumbers on both hold-out sets. Three families pass: the ring vibrations in the plane, the C–H stretches and the C–H bends out of
the plane. The fourth family, "other", holds the vibrations that fit none of the three, most of them slow motions of the whole skeleton
at the low end of the spectrum. It misses the line with 3.62 and 3.89. So the certificate
marks those bands "not licensed", even where they happen to be right.

## Three things the pipeline taught us

Running whole molecules through the chain found three faults that tests on single parts had missed.

**The order of the atoms mattered.** The same molecule can be written as several different SMILES strings, and each writing numbers the
atoms differently. B3LYP does not care: its bands moved by less than a billionth of a wavenumber. The network did care. For phenanthrene
another writing moved single corrected bands by up to 2.9 wavenumbers. Until we find the cause, the pipeline always starts from one
fixed writing, the canonical SMILES, so the same molecule always gets the same answer.

**Two of our test molecules had been seen.** The certificate now also says whether the network trained on the molecule. That check found
that two of the ten molecules in hold-out (a) were in the training set under another name. So part of hold-out (a)'s error had been
measured on molecules the network knew. Those two are now out of the training for the next version, and the show uses only the eight
clean ones. Hold-out (b) was clean: none of its 39 molecules had been seen.

**A flat molecule sits on a seam.** In a flat molecule many angles lie at exactly half a turn, where the number jumps from plus to minus.
A tiny change in the shape can flip such an angle to the other side, and the network then sees a different input. We measured how much
this costs: at most 0.12 wavenumbers on the average error of hold-out (a). So it is a reproducibility flaw, not a large error.

## What is not in the show yet

The 38 runs in the show did not compute steps 3 and 4 on the laptop. The corpus had already done exactly those calculations at the same
settings, so the pipeline takes them from there and says so on the station. That is why a run in the show takes less than three seconds.
When the corpus computed steps 3 and 4, they took about an hour and a half for a typical molecule (the median) and three hours for the
longest. A new molecule pays that time. A full run of phenanthrene on 30 October, with every step computed, took 76 minutes.

The page and the pipeline were written and tested with an AI assistant, like the rest of the project's code.

The scripts, the runs and the records behind every number are in the project's repository.
