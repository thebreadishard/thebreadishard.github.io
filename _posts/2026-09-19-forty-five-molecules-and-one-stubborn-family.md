---
layout: post
title: "Forty-five molecules and one stubborn family"
date: 2026-09-19 18:00:00 +0200
categories: plan05 probes
---

Two days ago the plan had a queue: forty molecules of cheap chemistry waiting behind a run that would not
finish for a week, a cost ratio measured in one basis only, and a question about how many expensive labels a
network needs that nobody could touch until the queue cleared. This is the account of what a hundred euros,
a rented machine in Helsinki and one Saturday did to that queue — and of the one thing that refused to move.

## The queue

The expensive run — the one that decides whether the cheap basis set can stand in for the expensive one on
naphthalene — computes one energy every twelve hours and needs thirteen of them. Everything else on the
laptop waits behind it, because touching it is not allowed. So on Thursday morning the forty-molecule
collection went to a rented sixteen-core machine instead: set up over an encrypted connection in an hour,
tested on water in fourteen seconds, and left to run. It finished Saturday noon: forty-five molecules,
none failed, twenty-eight hours of machine time, about six euros. The laptop never noticed.

## What the collection is for

Each molecule in it carries two versions of the same quantity, computed with a cheap and a somewhat less
cheap method: the stiffness of every vibration. The difference between the two is a stand-in for the thing
the project really wants — the difference between cheap chemistry and the gold standard — and it is free,
so it can be used to ask a question that would otherwise cost months: **how many molecules does a network
need to see before it can predict that difference for a molecule it has never seen?**

We wrote the expected answer down the night before, per family of vibrations, so that the numbers could
not be read charitably afterwards. Then we trained the network on five, ten, twenty and thirty molecules,
three times each with different random starts, and measured its error on twelve molecules it had never seen.

## Two families learn fast, one does not

For the carbon–hydrogen stretches the network was under two wavenumbers with five molecules — but so was
the dumbest possible rule, "predict the family's median", which needs no network at all. For the
carbon–hydrogen bending motions out of the molecular plane the network reached four wavenumbers at twenty
molecules, half the median rule and well inside the margin the astronomical references can resolve. Both
matched the expectations written the night before.

The third family did not. The ring vibrations between six and nine microns — the region where the
brightest bands of these molecules sit in the sky — stayed at twelve wavenumbers whatever we did, and the
curve was nearly flat: doubling the molecules bought almost nothing. Our written expectation for this
family had been "thirty to a hundred molecules, or refused", and the flat curve says the second half of
that sentence is the more likely one.

Our first diagnosis was that the network lacked the right vocabulary. Chemists describe these molecules by
how their hydrogens sit on the rim — alone, in pairs, in threes, in fours — and by which carbons are shared
between rings; none of that was among the inputs. So we computed it and added it. The result was the most
instructive number of the day: the network's error on the molecules it had *trained* on halved, and its
error on the molecules it had *not* seen did not change at all. It had memorised the new vocabulary and
learned nothing from it. A diagnosis written down in the morning was falsified by the evening, which is
what a written diagnosis is for.

What this says, we think, is that the vocabulary should not be ours to guess. A network that is handed a
dozen numbers per vibration can only ever recombine them; a network that is handed the molecule itself —
the atoms, their positions, and how each one moves in the vibration — can build whatever description
predicts the correction, including descriptions no chemist has named. That design was written down this
evening: the object it learns is the whole correction matrix, every measurement the pipeline makes is a
projection of that matrix, and the free two-method collection contains thousands of complete examples of
it to learn from before a single expensive label is spent. It is a plan, not a result. The first test is
already stated: the ring family below five wavenumbers on the same twelve held-out molecules.

## Three smaller things, all measured

**The count holds without symmetry.** Two days ago we showed that the couplings between vibrations can be
recovered exactly from a number of gradient measurements that grows with the size of the molecule rather
than its square — for eight highly symmetric molecules. The forty-five new ones have every symmetry from
perfect to none. The count stays linear for all of them: from thirteen gradients for benzene to a hundred
and forty-five for the least symmetric, always ten to thirty-six times fewer than the energies would need.
Less symmetry costs more, but never changes the rule.

**A smarter starting point does not help.** One of the ideas kept in reserve was to begin from a better
cheap method — a functional tuned per molecule — so that the correction to learn would be smaller. Tested
against the gold standard on three benzene vibrations, the tuned method made the correction one and a half
times *larger* and changed its sign on one vibration. The idea is recorded and closed; the plan that would
have grown from it will not be opened.

**A memory wall.** We tried to measure the gradient cost ratio at the settings the project actually uses,
which the laptop could not fit. Neither could the rented machine: three attempts, three times killed at
31.5 gigabytes, on a machine with 32. The number we have — a gradient costs six energies — is measured only
at the loosest settings in the smallest basis. Whether it holds where it matters is now a question for a
machine we cannot yet rent, or for the gradient code we are about to write ourselves, which will have to
print its own memory as it goes. The rented machine was deleted at half past two; it had cost seven euros.

## Where the expensive run stands

Two points in, twelve hours each, both clean: the frozen electronic spaces travel to the displaced geometry
with an overlap of 0.99, and the third point — the undisplaced molecule recomputed from scratch — came back
at seventeen minutes past five this afternoon identical to the original to the last digit, as it must. The
first of the three vibrations completes tomorrow evening, and with it the first real answer about the cheap
basis. The conversation with the supervisor has moved from the twenty-sixth to the twenty-eighth so that all
three vibrations are in before it.

Commits: [7dcd42f](https://github.com/thebreadishard/udacity-capstone-plan/commit/7dcd42f) (the collection),
[c7dcd1d](https://github.com/thebreadishard/udacity-capstone-plan/commit/c7dcd1d) (the first curve),
[5ad56fb](https://github.com/thebreadishard/udacity-capstone-plan/commit/5ad56fb) (the vocabulary that did not help),
[44505b6](https://github.com/thebreadishard/udacity-capstone-plan/commit/44505b6) (the count),
[7da2816](https://github.com/thebreadishard/udacity-capstone-plan/commit/7da2816) (the tuned functional),
[50bf0e9](https://github.com/thebreadishard/udacity-capstone-plan/commit/50bf0e9) (the memory wall),
[0472e2f](https://github.com/thebreadishard/udacity-capstone-plan/commit/0472e2f) (the design).
