---
layout: post
title: "One bond further"
date: 2026-09-24 15:14:51 +0200
categories: plan05 learning
---

Yesterday's post ended with a promise: we would check whether the *real* correction — the one from the expensive coupled-cluster
calculation, not the cheap stand-in we had been training on — lives in the same place. It does. Almost.

The check was a night's work for a rented machine: seventy-two gradient calculations of benzene at coupled-cluster level, assembled into
the matrix of force constants that the cheap method gets slightly wrong. Then the same reading we used before: write the correction in
the language of bonds and angles and ask how much of it sits on the diagonal, on pairs that share an atom, and on pairs of bonds inside
a ring. For the stand-in, that pattern held 94 % of the correction. For the real thing it holds 92 %. Corrected with only that pattern,
the vibrational frequencies of benzene land within 6 cm⁻¹ of the coupled-cluster answer, where the uncorrected ones were 38 off.

So far, a confirmation. The "almost" is in the couplings — the small terms that tell how vibrations re-mix. Those came out only
partly right with yesterday's pattern. Add one more class of pairs, bonds that sit two steps apart around the ring, and they come
right too: 98 % of the correction, couplings recovered to a third of their size instead of four fifths. The cheap stand-in never needed
that class; the real correction does. The picture is simple in the end: the expensive physics reaches one bond further than the cheap
physics. Our model gets one extra kind of pair in its list, and nothing else changes.

We had written down in advance what would count as a win, and by the letter of that rule this was not one: the first criterion was
met with room to spare, the second only after the pattern was extended. So the notes say "between", and then they say why, and then
they say what the extended pattern does. That is the point of writing the rule down first: you cannot move it afterwards, and you
cannot pretend the extension was the plan all along.

Two smaller things from the same day. First, we had twenty molecules in our data set that the cheap calculation flagged as sitting at
a saddle rather than a minimum — one vibration with an imaginary frequency. We had been throwing them out. A second, independent
calculation of each one said: five of them were the calculation's own noise, the same numerical-grid problem that produced the benzene
artefact of yesterday's post; fifteen are genuine. Five molecules came back into the data set, fifteen stay out for a better reason
than before, and the rule that throws molecules out is now decided per molecule by evidence.

Second, the price. One coupled-cluster gradient of naphthalene, the next molecule up, takes hours, not the minutes benzene took, and
the calculation does not go faster when you give it more processor cores. Symmetry helps: naphthalene has only five kinds of atom, so
thirty gradients suffice instead of a hundred and eight, and we proved on benzene that the shortcut loses nothing (twelve gradients
instead of seventy-two, agreement to a twentieth of a wavenumber). Naphthalene is running now, three gradients at a time. The result
should be in before the weekend.

*Numbers in this post: E8 pre-registration and its outcome sections in the repository (`GoalGathering/notes/PreRegistration_2026-09-23_E8_CC_Correction_Locality.md`),
the second-route read-out (`modules/05_support_predictor/data/second_route/imaginary_second_route_2026-09-24.md`), and the symmetry validation
log (`probes/results_m1/e8_benzene_ccpvdz/symmetry_validation_2026-09-24.log`).*
