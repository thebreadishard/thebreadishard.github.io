---
layout: post
title: "The first family answers, and the cheap route closes for it"
date: 2026-09-20 19:33:00 +0200
categories: plan05 probes
---

Since Wednesday evening a laptop in a living room has been computing one number every twelve hours. Thirteen of
them make up the run; the fifth landed this evening at 18:43. Five is the smallest count that answers a
question we wrote down on 14 September, before any of the numbers existed, and tonight the first of three
families gave its answer. It said no.

## The question

The plan corrects cheap quantum chemistry with expensive quantum chemistry. The expensive part is a coupled-
cluster calculation, and its price depends brutally on the basis set: the list of mathematical functions the
electrons are allowed to use. In the basis the plan trusts, called cc-pVTZ, one energy of naphthalene costs
twelve hours on this laptop. In the next smaller basis, cc-pVDZ, it costs about seventy minutes. A factor of
ten, for the same molecule.

So we asked whether the small basis could carry most of the load. The idea was a composite: compute the
coupled-cluster energy in the small basis, and add back what the big basis would have changed, using two
cheaper methods that scale far better. Most of the basis effect sits in those cheaper layers. What remains, the
piece that only coupled cluster sees, we hoped would be a small constant per band family: measure it once on
benzene, carry it to every larger molecule. On benzene we had measured it: +7.9 cm⁻¹ for the family of C–H
out-of-plane bends, +1.0 for the in-plane bends, −6.0 for the ring stretches.

We wrote the rule before running anything. If naphthalene's value for a family lies within 2.5 cm⁻¹ of
benzene's, the small basis is licensed for that family. If it lies more than 5 cm⁻¹ away, or the sign flips,
the family stays in the big basis.

## The answer

Naphthalene's C–H out-of-plane family: −8.1 cm⁻¹. Benzene's: +7.9. Sixteen wavenumbers apart, and the sign
has flipped. Under a second, looser way of defining the same piece it comes out at +23.0, fifteen away.
Either way the rule says no.

The components tell us why, and it is a story about the small basis rather than about our method. Along this
out-of-plane motion, the cheapest correlated method, MP2, lowers the frequency by 243 cm⁻¹ in the small basis
and by 125 in the big one. A method that changes its mind by 120 cm⁻¹ when you give it more functions is not
describing the molecule; it is describing the basis. This is a known weakness: for flat aromatic molecules,
MP2 in double-zeta bases softens the out-of-plane motions so much that it can predict benzene to be bent. Our
coupled-cluster arm inherits part of that softening in the small basis, and the effect grows with the size of
the ring system, which is exactly why a constant measured on benzene cannot be carried to naphthalene.

It does not touch the in-plane motions, as far as the physics goes. The two in-plane families are still
computing: one lands around Tuesday, the other around Thursday, and each is judged on its own. The plan
licenses per family, not per molecule, so a loss on one family is a fact about that family.

## What changes

For the C–H out-of-plane bands the big basis stays. That is the family that matters most for the
astronomical question the project started from: the strongest infrared feature of these molecules, near
11 micrometres, belongs to it. The cost estimate for the naphthalene deck, and the numbers in the request for
computing time that goes to the supervisor on 28 September, keep their twelve-hours-per-energy price for that
family. The measured increments do not disappear: they become the basis-set term of the error budget, which
is where an honest number that did not transfer belongs.

Nothing about the run itself is in doubt. The reference reloaded to within 0.0002 microhartree on Thursday
morning; every energy since has arrived on schedule; the frozen orbital spaces that make the whole scheme
possible behaved exactly as the benzene tests said they would. The instrument works. It just measured
something we would rather not have found, on the first family we pointed it at.

One footnote from later the same evening. At 19:27 the laptop was restarted, and the run died with it, forty
minutes into the second family. The design absorbed it: the reference spaces and the five finished points
were sealed on disk, the restart reloaded them, checked the reference against its sealed value in one second
(the same 0.0002 microhartree), recovered a partly solved fragment from a checkpoint, and went on. The cost
was under an hour. The report now lands in the small hours of Friday instead of Thursday evening.

Which is, we keep reminding ourselves, what an instrument is for.
