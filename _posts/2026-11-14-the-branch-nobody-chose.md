---
layout: post
title: "The branch nobody chose"
date: 2026-11-14 09:40:36 +0100
categories: plan05 method
---

<!-- Draft written 14 Nov 2026 on the user's word ("Ja"), after they rejected a weather-forecast comparison as a hook.
Sources, all in the plan repo at 754fced2:
  - the route rule and its reading: GoalGathering/notes/PreRegistration_2026-11-04_Anharmonic_Route_Rule.md,
    probes/results_m1/labels_domain_2026-11-13/route_rule/route_rule.md (commits bab18472, 4b82aa21)
  - the printed-position registration: GoalGathering/notes/PreRegistration_2026-11-14_Printed_Band_Position.md (cefb3972)
  - the label swap, its registration and its amendment: GoalGathering/notes/PreRegistration_2026-11-14_Low_Mode_Label_Swap.md,
    probes/low_mode_swap.py, probes/results_m1/low_mode_swap_2026-11-14/swap_dry_run.json (e9b27e84)
  - the second route of 12 Nov on B_0f06474656: ledger entry 12 Nov 21:2x.
Published 14 Nov 2026 on the user's word ("Publiceer"). -->

This week we came close to publishing a number we already believed was wrong. Not because we had measured badly, and not
because anyone decided to. It happened because of a single word in a rule we had written ourselves: *otherwise*.

## What the number is

Our project predicts the infrared spectrum of a soot-like molecule: the list of frequencies at which it absorbs light, and
how strongly. A frequency in this field is given as a [wavenumber](/glossary/#wavenumber), in units written cm⁻¹; you can
read it as "how fast the atoms swing", and a difference of a few cm⁻¹ is the kind of detail an astronomer compares against
a telescope spectrum.

Getting one of those frequencies right takes two steps. The first treats every vibration as an ideal spring — the
[harmonic approximation](/glossary/#harmonic-approximation) — and that is what our neural network improves: it predicts
how a cheap, fast quantum calculation of the molecule's stiffness differs from an expensive, accurate one. The second step
is the correction for the fact that real chemical bonds are not ideal springs. They soften as they stretch, so a real band
sits a little lower than the harmonic one. That is [anharmonicity](/glossary/#anharmonicity), and for the bands we care
about it moves a line by a few to a few tens of cm⁻¹.

We have a small separate model for that second step. The question was whether to use it.

## The rule we wrote first

On 4 November, before either of the two measurements that would decide it had been read, we wrote down the rule. We do
this on purpose — a rule written after you have seen the numbers follows the numbers. A rule written first can tell you
something you did not want to hear. The blog has a word for it: [pre-registration](/glossary/#pre-registration).

The rule offered three outcomes per family of vibrations. A family is just a group of similar motions: the ring
breathing in the plane of the molecule, the hydrogen atoms flapping out of the plane, and so on. For each family the
pipeline could take the learned model, or take the family's average shift, or take neither. The threshold was 3 cm⁻¹:
a route had to be that accurate on our test molecules to be used.

On 13 November the measurement came in, and the answer was the same for all three families: neither route reached
3 cm⁻¹, so neither was allowed. Under the rule, the third outcome won, and the third outcome was "no anharmonic step" —
print the harmonic frequency and say so.

## What the third outcome costs

Writing the reading up, I added a column that was not in the rule: if we take no step, how far from the truth is the
harmonic position itself? The answer turned the whole thing over.

| family | modes | learned model | family average | no step at all |
|---|---|---|---|---|
| in-plane ring | 56 | **3.37** | 8.47 | 26.23 |
| other | 23 | **3.84** | 4.34 | 8.20 |
| C–H out of plane | 22 | **8.38** | 9.27 | 12.48 |

Root-mean-square error in cm⁻¹ against the true shift, on the bands of coronene and tetracene between 550 and 2000 cm⁻¹.

The refused model is the best of the three in every family. In the in-plane ring family it is wrong by 3.37 cm⁻¹ where
doing nothing is wrong by 26.23 — nearly eight times worse. The rule, read exactly as written, told us to publish the
worst of the three options.

## How a sensible rule produced that

Two of the three outcomes were choices. We had weighed the learned model against the family average, we knew what each
would cost, and the threshold decided between them. The third was not a choice; it was an *otherwise*. It was what the
rule would do if the first two failed, and because it looked like a fallback rather than an option, nobody had ever
measured what it costs. The column above had never been computed.

That is the general lesson, and anyone who has written code or a protocol will recognise it: the dangerous branch of a
rule is the one you wrote as "otherwise", because it is the only one you did not price.

Underneath sat a second confusion, which the first hid. The threshold of 3 cm⁻¹ answers the question *may we claim this
accuracy?* That is a question about a promise to a reader. "Which number is our best estimate?" is a different question,
and the two have different answers here. By letting the failed promise decide what we print, we had quietly answered the
second question with the first one's answer.

So the pipeline now prints the learned anharmonic estimate, states the measured error of the family beside it, and claims
no accuracy at all. The threshold stays exactly where it was written; it simply no longer decides what is shown. Both
decisions are registered, the old one unchanged and the new one dated 14 November.

Two honest limits belong with that table. The errors are measured on two molecules, coronene and tetracene, and coronene
carries nearly all of the failure: in the C–H out-of-plane family it is 11.91 against tetracene's 4.55. And the numbers
say nothing yet about molecules unlike those two.

## The same pattern, caught in ten minutes

The other half of this week shows what writing rules down first is actually worth.

We are replacing some of the reference data the network learns from. A handful of molecules have very soft vibrations,
and for those the standard quantum-chemistry recipe is numerically shaky, so we recomputed them a slower and more
reliable way. Before swapping a single file I wrote the acceptance rules: what gets replaced, what gets kept, and four
conditions a molecule has to satisfy. One of them said that no vibration above 20 cm⁻¹ may move by more than 2 cm⁻¹ —
the repair is supposed to touch the soft modes only.

Then I ran it as a rehearsal, writing nothing, and it refused seven files.

Reading the refusals took ten minutes and showed that the criterion was wrong. In each refused file exactly one vibration
had moved, and it was the molecule's own lowest one, sitting at 51 to 63 cm⁻¹; everything else had moved by at most
0.37 cm⁻¹. That one vibration is precisely what the repair exists to correct. My criterion had been meant to catch
collateral damage and had instead caught the repair itself.

The deciding evidence was already in the drawer. For one of those molecules we had computed the slow, reliable answer in
full back on 12 November: 57.98 cm⁻¹. The old value was 55.42 and the repair gives 58.02. The repair is right, and my
rule would have thrown it away.

The criterion is now written the other way around — the modes the repair targets may move, everything else is held to
0.5 cm⁻¹ — and the amendment is in the registration with the reason, dated, above the original text, which stands. The
rehearsal then passed: 114 of 116 files, with the largest untargeted change at 0.467 cm⁻¹ against the 0.5 line. The two
files left out belong to a molecule still being computed.

Writing a rule down first does not stop you from being wrong. It makes being wrong cheap, and it makes it visible on the
day instead of in six months. One of this week's two mistakes was caught by a rehearsal before a file moved. The other
had been sitting in a rule since 4 November, in the branch nobody chose, and was only found because somebody computed a
column that nobody had asked for.
