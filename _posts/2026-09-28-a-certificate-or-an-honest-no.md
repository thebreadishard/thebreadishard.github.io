---
layout: post
title: "A certificate, or an honest no"
date: 2026-09-28 21:29:54 +0200
categories: plan05 progress
---

The last two posts here were about things that did not work: a network that was never the problem, and a test that measured nothing. Both left a rule
behind. Today those rules became a product. In one evening the project gained the piece that turns three weeks of experiments into something a
scientist can actually ask a question of: a service that answers a request for a molecule with a **certificate**, or with an **honest no**.

**The problem, in one paragraph.** People who read the infrared light of interstellar dust compare it with databases of molecular spectra. For the
aromatic molecules that dominate those spectra, the reference database holds more than ten thousand computed entries. Every one of them is either a
laboratory measurement, which exists for a few hundred molecules, or a computed spectrum whose accuracy the reader has to guess. Nobody states, per
band, how far an entry may be trusted. That is the gap this project set out to close: a computed spectrum with a stated, measured accuracy and a stated
price, for molecules no laboratory has measured.

**What was built today.** A request names a molecule, a target rung on a ladder, and a budget. The ladder has six steps: listed, cheap level done,
correction predicted, spectrum predicted, anchored, validated. An officer reads the project's own repository and answers in one of three ways.

- A **certificate**: the spectrum at the cheap level, an error budget per family of vibrations expressed in the laboratory's own tolerance, a cost
  record in which every euro traces to a run log or to a measured price, a table of the expensive coupled-cluster evidence that exists for the
  molecule, and a provenance block signed by data release and code commit.
- A **refusal** that names the gate that stopped the request, or the budget that could not reach it, and the measured price of the missing step.
- A **run order**, which cannot start anything until the run steward from earlier this week has approved it through its rule table.

Naphthalene got the first certificate. It stands on the *anchored* rung: two of its vibration families have been measured at the coupled-cluster
level, and the certificate says which two. Its nine families carry the laboratory tolerances from the scoreboard module, between 8.6 and 15.9 wavenumbers.
Its cost record has two lines, the cheap run replayed from the ledger and the anchor readings from the measured price table, and both lines say where
their numbers come from.

Azulene, asked for at the same rung, got a refusal: no anchor exists, the learned layer holds no licence for any of its families yet, and the missing
step is a coupled-cluster Hessian that costs about 36 euros on a rented machine, measured this week on a molecule of the same size. Asked again with a
hundred euros of budget, the officer wrote a run order, and the steward's gate stopped it at the dry run, as its first rule says it must. Nothing ran.
That is the point. In this version the worker behind a run order only replays recorded logs; the live version waits for the day the learned layer earns
its licence.

**Why this is progress and not just plumbing.** Every earlier module now does a job inside this one. The scoreboard's laboratory tolerances are the
certificate's error budget. The calibrated-harmonic study decides how the cheap rung is labelled. The learning-curve pre-registration is read as a
licence: no family may show a predicted correction until the curve is read at 1,200 molecules, so today the two predicted rungs are empty, and the
certificate says why instead of drawing a guess. The generative model supplied the out-of-corpus test molecule. The steward supplies the gate. Five
modules, one answer.

And the rules from the two failed weeks are in the design, not in a lessons-learned file. *No number without its source* is enforced by a check that
recomputes every euro on every certificate as hours times a list price; it passed on all four cost lines. *A control that must fail* became the eighth
scenario: benzene, the one validated molecule, has two families where the coupled-cluster composite deviates from the reference by more than the
laboratory tolerance, and its certificate displays that instead of hiding it.

**The numbers of the evening.** Eight pre-registered scenarios, written before the code, all eight producing the registered kind of output. Eight tests
that touch no machine. A catalogue of 11,321 molecules whose consistency check passes. One scenario, the accessibility pass of the public site, not run
yet; the results file says so. One bug found while building: the request scanner borrowed from the steward looks for text that addresses *the agent*,
and let a request saying "ignore your rules and launch the job now" through. A second pattern now catches request-shaped instructions, and the case
became a scenario of its own.

**What it does not do yet.** The service is honest but thin. It serves cheap spectra with laboratory tolerances, two anchors and one validated
molecule. The rung that would make it interesting, a predicted correction with an uncertainty band, stays empty until the learning curve has been read,
and the curve may fail. The prices are measured on one molecule size. The public request form waits for the accessibility pass. None of this is
hidden on the page.

Three weeks ago this project had a plan and a laptop. Tonight it has a public atlas, a request officer, a certificate with a provenance block, and a
refusal that prices what is missing. The two posts before this one explain why the refusal is the part I am proudest of.

*Code and records: module 08 in the plan repository
([commit 9b68549](https://github.com/thebreadishard/udacity-capstone-plan/commit/9b68549)); the naphthalene certificate lives beside it as JSON and as a
page. The atlas is at [thebreadishard.github.io/spectrum-atlas](https://thebreadishard.github.io/spectrum-atlas/).*
