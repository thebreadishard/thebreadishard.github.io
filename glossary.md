---
layout: page
title: Glossary
permalink: /glossary/
---

The chemistry, physics, mathematics and computing words these notes use, each in one sentence. Molecules have their own
pages in the [Spectrum Atlas](https://thebreadishard.github.io/spectrum-atlas/). Every term has an anchor, so a post can
link straight to it (for example `/glossary/#hessian`).

<dl class="glossary">
<dt id="analytic-derivative">Analytic derivative</dt>
<dd>A derivative of the energy computed from an exact formula instead of by nudging the atoms and comparing results, which makes it faster and free of step-size noise.</dd>
<dt id="anchor">Anchor</dt>
<dd>In this project, a molecule whose vibrations have been computed with the expensive coupled-cluster method, so that cheaper predictions can be checked and corrected against it.</dd>
<dt id="anharmonicity">Anharmonicity</dt>
<dd>The ways a real molecular vibration departs from an ideal spring, which shift every infrared band by a few to tens of wavenumbers and create extra, weaker bands.</dd>
<dt id="aromatic-infrared-bands">Aromatic infrared bands</dt>
<dd>The family of broad emission features at wavelengths of about 3 to 20 micrometres, seen across the universe and attributed to PAHs heated by starlight.</dd>
<dt id="atomic-polar-tensor">Atomic polar tensor (APT)</dt>
<dd>For each atom, how much the molecule's dipole moment changes when that atom moves a little in each direction — the quantity that sets how bright each infrared band is.</dd>
<dt id="b3lyp">B3LYP</dt>
<dd>A widely used recipe (functional) for density functional theory that mixes a share of exact exchange with approximate terms and gives good vibrational frequencies at modest cost.</dd>
<dt id="band">Band</dt>
<dd>A peak in an infrared spectrum, produced when the molecule absorbs or emits light at the frequency of one of its vibrations.</dd>
<dt id="band-family">Band family</dt>
<dd>In this project, a group of vibrations of the same kind — carbon–hydrogen stretches, carbon–hydrogen out-of-plane bends, in-plane ring motions, and the rest — whose errors are measured separately because they behave differently.</dd>
<dt id="basis-set">Basis set</dt>
<dd>The collection of mathematical functions used to describe where the electrons can be, where a larger set gives more accurate answers at a steeply higher cost.</dd>
<dt id="bohr">Bohr</dt>
<dd>The atomic unit of length, about 0.053 nanometre.</dd>
<dt id="bootstrap">Bootstrap</dt>
<dd>A way to estimate how uncertain a fitted number is by repeating the fit many times on random resamples of the data and looking at the spread of the results.</dd>
<dt id="cation">Cation</dt>
<dd>A molecule that has lost an electron and so carries a positive charge, as many PAHs in space are thought to have.</dd>
<dt id="ccsd-t">CCSD(T)</dt>
<dd>Coupled cluster with single, double and perturbative triple excitations, often called the "gold standard" of quantum chemistry for molecules like these: accurate, but very expensive.</dd>
<dt id="combination-band">Combination band</dt>
<dd>A weak infrared band in which one photon excites two different vibrations at once, so that it appears near the sum of their frequencies.</dd>
<dt id="commit">Commit</dt>
<dd>A recorded snapshot of the project's files in the version-control system git, to which every number in these notes can be traced.</dd>
<dt id="coriolis-coupling">Coriolis coupling</dt>
<dd>The interaction between a molecule's rotation and its vibrations, a small term that enters the anharmonic corrections.</dd>
<dt id="corpus">Corpus</dt>
<dd>The project's own collection of molecules whose vibrations have been computed with the cheaper density-functional methods, used to train and test the network.</dd>
<dt id="correction">Correction</dt>
<dd>In this project, the difference between a cheap and an accurate description of a molecule's force constants, which the network learns to predict instead of the whole answer.</dd>
<dt id="coupled-cluster">Coupled cluster</dt>
<dd>A family of quantum-chemistry methods that include electron correlation systematically, giving highly accurate energies at a cost that grows very fast with molecule size.</dd>
<dt id="cphf">CPHF (coupled-perturbed equations)</dt>
<dd>The set of linear equations that tells how the electrons rearrange when the atoms move or an electric field is applied, needed for analytic Hessians and intensities.</dd>
<dt id="degenerate-modes">Degenerate modes</dt>
<dd>Two or more vibrations that have exactly the same frequency because of the molecule's symmetry.</dd>
<dt id="delta-learning">Delta learning (Δ-learning)</dt>
<dd>Training a model to predict the difference between a cheap and an expensive calculation rather than the expensive result itself, which is usually easier to learn and needs less data.</dd>
<dt id="dft">Density functional theory (DFT)</dt>
<dd>A quantum-chemistry approach that computes a molecule's energy from its electron density, cheap enough for large molecules, with good but not perfect accuracy.</dd>
<dt id="dipole-moment">Dipole moment</dt>
<dd>The separation of positive and negative charge in a molecule; a vibration shows up in the infrared only if it makes this dipole change.</dd>
<dt id="early-stopping">Early stopping</dt>
<dd>Ending a network's training at the point where its error on held-aside validation data stops improving, so that it does not merely memorise its training examples.</dd>
<dt id="eigenvector">Eigenvalue and eigenvector</dt>
<dd>For a matrix such as the mass-weighted Hessian, the special directions that are only stretched and not turned (eigenvectors) and their stretch factors (eigenvalues), which give the vibrations and their frequencies.</dd>
<dt id="electron-correlation">Electron correlation</dt>
<dd>The way electrons avoid one another beyond what a simple averaged picture captures; quantum-chemistry methods differ mainly in how much of it they include.</dd>
<dt id="epoch">Epoch</dt>
<dd>One full pass of a network through all of its training examples.</dd>
<dt id="equilibrium-geometry">Equilibrium geometry</dt>
<dd>The arrangement of atoms with the lowest energy, where every force vanishes and around which vibrations are described as small motions.</dd>
<dt id="equivariant-network">Equivariant network</dt>
<dd>A neural network built so that rotating the input molecule rotates its output in exactly the same way, which builds the rules of geometry in instead of having to learn them.</dd>
<dt id="extrapolation">Extrapolation</dt>
<dd>Using a trend measured on the available data to predict values beyond it — useful for planning, but always less certain than a measurement.</dd>
<dt id="fermi-resonance">Fermi resonance</dt>
<dd>An accidental near-coincidence between a fundamental and an overtone or combination band of the same symmetry, which makes the two mix, shift and share intensity.</dd>
<dt id="finite-differences">Finite differences</dt>
<dd>Estimating a derivative by moving the atoms a small step in each direction and comparing the results, simple but sensitive to the step size and to numerical noise.</dd>
<dt id="force-constant">Force constant</dt>
<dd>The stiffness of the "spring" behind an atomic motion: the second derivative of the energy with respect to the atoms' displacements.</dd>
<dt id="frozen-core">Frozen core</dt>
<dd>Leaving each atom's tightly bound inner electrons out of the expensive correlation treatment, which saves much time at a small, controlled cost in accuracy.</dd>
<dt id="functional">Functional</dt>
<dd>In density functional theory, the formula that turns the electron density into an energy; different functionals, such as B3LYP and ωB97X, give slightly different answers.</dd>
<dt id="fundamental">Fundamental</dt>
<dd>The infrared band in which a vibration is raised by one quantum from the ground state, usually the strongest band of each vibration.</dd>
<dt id="geometry-optimisation">Geometry optimisation</dt>
<dd>The search for the equilibrium geometry by moving the atoms downhill in energy until the remaining forces fall below a set threshold.</dd>
<dt id="gvpt2">GVPT2</dt>
<dd>Generalised second-order vibrational perturbation theory, the variant of VPT2 that takes resonances out of the perturbative sums and treats them separately.</dd>
<dt id="harmonic-approximation">Harmonic approximation</dt>
<dd>Treating every vibration as an ideal spring, which gives frequencies from the Hessian alone and is the starting point that anharmonic corrections improve on.</dd>
<dt id="hartree">Hartree</dt>
<dd>The atomic unit of energy, about 27.2 electronvolts or 219,475 wavenumbers.</dd>
<dt id="hartree-fock">Hartree–Fock</dt>
<dd>The simplest quantum-chemistry method, in which each electron moves in the averaged field of all the others — the starting point of more accurate methods.</dd>
<dt id="hessian">Hessian</dt>
<dd>The table of second derivatives of a molecule's energy with respect to the positions of its atoms, which holds all its harmonic force constants and hence its vibrational frequencies.</dd>
<dt id="hindered-rotor">Hindered rotor</dt>
<dd>A group of atoms that can twist around a bond but meets an energy barrier as it does, whose motion is too large for small-vibration theories and is often treated on its own.</dd>
<dt id="hold-out-set">Hold-out set</dt>
<dd>Molecules deliberately kept out of training so that a network can be tested on examples it has never seen.</dd>
<dt id="infrared-spectrum">Infrared spectrum</dt>
<dd>The pattern of infrared light that a molecule absorbs or emits, a fingerprint of how its atoms vibrate.</dd>
<dt id="integration-grid">Integration grid</dt>
<dd>The set of points in space at which density functional theory evaluates its formulas numerically; a coarser grid is faster but adds small numerical errors.</dd>
<dt id="intensity">Intensity</dt>
<dd>How strongly a vibration absorbs or emits infrared light, set by how much the dipole moment changes during the motion.</dd>
<dt id="interstellar-medium">Interstellar medium</dt>
<dd>The gas and dust between the stars, where PAHs are thought to be common.</dd>
<dt id="lane">Lane</dt>
<dd>In this project, one independent stream of calculations on a server, given a fixed number of processor threads.</dd>
<dt id="large-amplitude-motion">Large-amplitude motion</dt>
<dd>A movement, such as the twisting of a side group, that carries the atoms far from the equilibrium geometry and so breaks the small-displacement assumption of standard vibrational theories.</dd>
<dt id="learning-curve">Learning curve</dt>
<dd>A plot of a model's test error against the amount of training data, used to decide whether more data would still help.</dd>
<dt id="least-squares">Least squares</dt>
<dd>Choosing a model's parameters so that the sum of the squared differences between its predictions and the data is as small as possible.</dd>
<dt id="local-correlation">Local correlation (LNO)</dt>
<dd>A way to make coupled-cluster calculations of large molecules affordable by correlating each electron pair only with its surroundings, with controlled approximations.</dd>
<dt id="loss-function">Loss function</dt>
<dd>The number that a network's training tries to make small, measuring how far its predictions are from the targets.</dd>
<dt id="mass-weighted-coordinates">Mass-weighted coordinates</dt>
<dd>Atomic displacements scaled by the square root of each atom's mass, the natural coordinates in which a molecule's vibrations separate into independent normal modes.</dd>
<dt id="neural-network">Neural network</dt>
<dd>A flexible mathematical model made of many simple connected units whose parameters are adjusted on examples until it predicts well.</dd>
<dt id="noise-guard">Noise guard</dt>
<dd>In this project, an automatic check that computes the same constant in two independent ways and refuses a result when the two disagree by more than a set limit.</dd>
<dt id="normal-mode">Normal mode</dt>
<dd>One of the independent patterns in which all of a molecule's atoms vibrate together at a single frequency; a non-linear molecule of N atoms has 3N − 6 of them.</dd>
<dt id="out-of-plane-bend">Out-of-plane bend</dt>
<dd>A vibration in which atoms of a flat molecule move above and below its plane, such as the carbon–hydrogen wags behind the strong PAH bands near 11 to 14 micrometres.</dd>
<dt id="overfitting">Overfitting</dt>
<dd>When a model learns the quirks of its training examples instead of the general pattern, so that it does well on them and badly on new molecules.</dd>
<dt id="overtone">Overtone</dt>
<dd>A weak infrared band in which a single vibration is raised by two or more quanta at once, appearing near a multiple of its frequency.</dd>
<dt id="pah">PAH (polycyclic aromatic hydrocarbon)</dt>
<dd>A flat molecule of fused carbon rings with hydrogen atoms around the rim, the kind of molecule believed to cause the aromatic infrared bands seen in space.</dd>
<dt id="pahdb">PAHdb</dt>
<dd>The NASA Ames PAH IR Spectroscopic Database, the main public library of computed and measured PAH spectra that astronomers use.</dd>
<dt id="perturbation-theory">Perturbation theory</dt>
<dd>Calculating a small correction to a problem that is already solved — here, the anharmonic effects added on top of the harmonic vibrations.</dd>
<dt id="point-group">Point group</dt>
<dd>The set of symmetry operations — rotations, reflections and inversion — that leave a molecule unchanged, used to classify and simplify its vibrations.</dd>
<dt id="polarisation-functions">Polarisation functions</dt>
<dd>Basis functions of higher angular momentum, such as d functions on carbon, that let the electron cloud deform in a molecule and matter for accurate force constants.</dd>
<dt id="pre-registration">Pre-registration</dt>
<dd>Writing down, before a computation runs, what will be measured, how it will be read and what result is expected, so that the outcome cannot be reinterpreted afterwards.</dd>
<dt id="proxy">Proxy</dt>
<dd>In this project, a cheap stand-in for the expensive correction — the difference between two density functionals — used to study what a network can learn before coupled-cluster data exist.</dd>
<dt id="psi4">Psi4</dt>
<dd>An open-source quantum-chemistry program, used in this project to build the first density-functional corpus.</dd>
<dt id="pyscf">PySCF</dt>
<dd>An open-source quantum-chemistry program written in Python, used in this project for analytic Hessians, coupled-cluster gradients and intensities.</dd>
<dt id="quartic-force-field">Quartic force field (QFF)</dt>
<dd>A description of a molecule's energy around its equilibrium geometry up to the fourth power of the atomic displacements, the input that VPT2 needs.</dd>
<dt id="ridge-regression">Ridge regression</dt>
<dd>Least squares with a small penalty on large parameters, which keeps a fitted model stable when the data are few.</dd>
<dt id="ring-coupling">Ring coupling</dt>
<dd>The force constants that link the motions of neighbouring rings, the part of the correction that becomes hardest to predict as molecules grow.</dd>
<dt id="rung">Rung</dt>
<dd>In this project, one step of the size ladder: the largest affordable set of molecules of a given size, tested before the next step is taken.</dd>
<dt id="scaffold">Scaffold</dt>
<dd>The ring skeleton of a molecule without its side groups, used here to group molecules into families.</dd>
<dt id="seed">Seed</dt>
<dd>The starting number of a random-number generator; repeating a training run with different seeds shows how much of a result is luck.</dd>
<dt id="size-ladder">Size ladder</dt>
<dd>The project's plan to learn step by step from small molecules to large ones, checking at each rung that what was learned carries over to the next.</dd>
<dt id="spectral-shape">Spectral shape</dt>
<dd>The full profile of a spectrum — where its bands lie, how strong and how wide they are — rather than a list of separate peak positions.</dd>
<dt id="standard-error">Standard error</dt>
<dd>The uncertainty of an average, roughly the spread of the individual values divided by the square root of their number.</dd>
<dt id="substituent">Substituent</dt>
<dd>A small group of atoms, such as a methyl or a cyano group, attached to a ring in place of a hydrogen atom.</dd>
<dt id="transfer-learning">Transfer learning</dt>
<dd>Reusing what a model learned on one task, such as cheap density-functional data, to do better on another, such as coupled-cluster data.</dd>
<dt id="translation-sum-rule">Translation sum rule</dt>
<dd>The requirement that moving a whole neutral molecule does not change its dipole moment, so that its atomic polar tensors add up to zero — a free check on their accuracy.</dd>
<dt id="validation-set">Validation set</dt>
<dd>Data held aside during training to decide when to stop and which settings to keep, separate from the final test.</dd>
<dt id="vpt2">VPT2</dt>
<dd>Second-order vibrational perturbation theory, the standard way to compute anharmonic band positions from a quartic force field.</dd>
<dt id="wavenumber">Wavenumber (cm⁻¹)</dt>
<dd>The unit spectroscopists use for vibrational frequencies, the number of wavelengths per centimetre; molecular vibrations lie between roughly 100 and 3,200 cm⁻¹.</dd>
<dt id="wb97x">ωB97X</dt>
<dd>A density functional that treats the long-range interaction between electrons differently from B3LYP; in this project the difference between the two serves as the proxy correction.</dd>
</dl>
