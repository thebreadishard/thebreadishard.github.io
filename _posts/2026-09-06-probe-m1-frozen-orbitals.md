---
layout: post
title: "Freezing the orbitals: what the first real measurement of the project told us"
date: 2026-09-06 16:00:00 +0200
categories: plan05 probes
---

This is the first post, so it starts with the problem and then gets to the number we
measured this week. If you know some high-school chemistry and physics you should be able
to follow all of it.

## The problem in one paragraph

Molecules vibrate. Each way a molecule can vibrate has its own frequency, and a molecule
absorbs infrared light at exactly those frequencies. A recording of which frequencies are
absorbed is called an infrared spectrum, and it works like a fingerprint. Astronomers see
such fingerprints coming from clouds of gas and dust all over the universe, and they are
fairly sure the molecules responsible are large, flat carbon molecules called PAHs. The
trouble is that for the big PAHs nobody has a laboratory spectrum, so if we want to know
what a particular molecule would look like, we have to compute it.

## Why computing it is hard

A spectrum is decided by how the energy of the molecule changes when you push its atoms
a little out of place. Push the atoms along one vibration, compute the energy at a few
positions, and the curvature of that energy curve gives you the frequency. The catch is
the word "compute". The method that is accurate enough for our purpose is called
coupled-cluster theory, CCSD(T) for short. It is very precise and very slow. For benzene,
the smallest molecule we look at, one energy takes about a minute on my laptop. For the
molecules we actually care about it would take far longer per point, or it simply would
not fit in the laptop's memory.

There is a cheaper version of the same method, called a local coupled-cluster method.
It saves time by looking only at the electrons that are close to each other, and it
decides for itself which electron pairs matter. That is fine when you want one energy.
It is a problem when you want a smooth curve. Every time you move the atoms a little, the
method makes its choice again, and sometimes it chooses slightly differently. The energy
then jumps by a small amount that has nothing to do with the molecule. Think of measuring
the slope of a hill with a ruler that gets a slightly different length each time you pick
it up. The jumps are tiny compared with the total energy, but the curvature we need is
also tiny, so the jumps can spoil it.

## The idea we wanted to test

The idea is simple to say. Let the local method make its choices once, at the resting
position of the molecule, and then carry those choices along unchanged as we push the
atoms. We call this "freezing the spaces". The hope is that the jumps disappear because
nothing gets chosen anew. The fear is that the frozen choice becomes a bad choice as the
atoms move away, so the curve is smooth but wrong.

Whether the hope or the fear wins is not something you can decide by arguing about it.
You have to measure it. That is what probe M1 does.

## How we measured it

We took benzene, because for benzene we can still afford the slow, exact method as a
reference. We chose three of its vibrations: one where the hydrogen atoms bob up and down
out of the ring, one where they bend sideways in the plane of the ring, and one where the
carbon ring itself stretches. Along each vibration we computed the energy at nine
positions.

At each of the 27 positions we ran four calculations:

- **Arm A**, the frozen version: choices made once at the centre and carried along.
- **Arm B**, half frozen: some choices carried along, the rest made anew at every point.
- **Arm C**, nothing frozen: the cheap method as it comes, choosing anew every time.
- **The truth line**: the slow, exact method at the same 27 positions.

Then we asked two questions of each arm. First, how jumpy is it? We fit a smooth curve
through the nine points and look at how far the points scatter around it. Second, how
biased is it? We compare the curvature of the arm's curve with the curvature of the truth
line and turn the difference into the unit spectroscopists use for a band position,
the wavenumber, written cm⁻¹. A difference of a few wavenumbers is the kind of error that
decides whether a computed band matches a laboratory band or not.

## What came out

The scatter is the clearest result. In arm A the points scatter around the smooth curve by
about 0.002 to 0.06 micro-hartree, depending on the vibration. In arms B and C, the ones
that keep re-choosing, the scatter is 7 to 11 micro-hartree. That is a factor of a hundred
to a few thousand. A micro-hartree is a very small energy: one micro-hartree corresponds
to about 0.2 wavenumbers. So arm A is smooth to well below anything we could ever see in
a spectrum, and arms B and C are not.

The bias needed more care, and this is the honest part of the story. Our first full run of
arm A gave curvatures that were off by 23, 46 and 147 wavenumbers on the three vibrations.
That is hopeless. The cause turned out to be a technical one: the frozen orbitals have to
be handed to the method in a particular arrangement, and we had skipped that step. One
extra line of code fixed it, and the rerun gave biases of 2.6, 0.5 and 1.8 wavenumbers,
when we include a standard correction that the method's own literature uses.

We then tightened the method's internal settings, which makes it keep more electron pairs
and costs almost twice the time. With the tight settings the biases dropped to 0.14, 0.03
and 0.36 wavenumbers, while the scatter of arm A stayed exactly as small as before. The
re-choosing arms also got quieter with the tight settings, but they still scatter ten to
a thousand times more than arm A.

In short: freezing the spaces gives a curve that is both smooth and, with tight settings,
almost unbiased. The hope won, on benzene, at the smaller of the two basis sets we use.

## What this does not tell us yet

Benzene is small and very symmetric, and the run used the smaller basis set, which is
the set of building blocks the calculation uses to describe the electrons. The same scan
with the larger basis set is running on my laptop as I write this. It takes about two hours
per point, so 27 points plus the truth line will take until Tuesday. After that comes
naphthalene, two rings instead of one, which is the first molecule where the cheap and the
expensive method really part ways in cost.

None of this says yet that the pipeline will produce useful spectra for the big molecules.
It says that one specific fear, that freezing makes the curve wrong, did not come true on
the first molecule. That is the smallest possible step, and it is the one we wanted to
take first, because if it had failed there would have been no point in building the rest.

## Where the numbers come from

Everything above was printed by the script `probes/m1_frozen_spaces.py` and its companion
`probes/m1_canonical_truth.py` in the project repository. The rerun with the fix is
commit [51bf492](https://github.com/thebreadishard/udacity-capstone-plan/commit/51bf492)
and the tight-settings run is commit
[c1bc4e0](https://github.com/thebreadishard/udacity-capstone-plan/commit/c1bc4e0).
The result files are in `probes/results_m1/`. The project plan has not been reviewed by my
supervisor yet, so read this as a student's lab notebook, not as a finished result.

## Correction, 10 September 2026

Every bias in this post is twice too large. The script that compared each arm with the truth line
reported the change in the *curvature* of the energy curve and I converted that straight into
wavenumbers. In the coordinates we use, a change in curvature is twice the change in frequency, so
the numbers that matter for a spectrum are half of what is written above: the first, broken run was
off by 12, 23 and 74 wavenumbers (not 23, 46 and 147); the fixed rerun by 1.3, 0.25 and 0.9 (not
2.6, 0.5 and 1.8); and with tight settings by 0.07, 0.015 and 0.18 (not 0.14, 0.03 and 0.36). The
scatter figures in micro-hartree were never affected, and the conclusion stands: the frozen curve is
smooth and, with tight settings, almost unbiased. I checked the factor against an identity in the
project's own DFT data before writing this, and the correction is recorded in the repository
(research note, erratum at the head of §2). I leave the original text as it was; that is the rule of
this notebook.
