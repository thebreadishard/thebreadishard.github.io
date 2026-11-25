---
layout: post
title: "The day the goal moved"
date: 2026-11-25 17:00:00 +0100
categories: plan05 method
---

<!-- Draft written 25 Nov 2026 on the user's word (a summary of the supervisor meeting, told from the moment before the
reference calculation was written out for her; that outcome is kept for a later post); published the same day on the user's word ("Ja, publiceer"). The supervisor is not named, the slides
are not shown and nothing is quoted from the conversation.
Sources, all in the plan repo (plans/05_delta-probed-ir-pipeline):
  - what was said: GoalGathering/notes/Meeting_2026-11-25_Supervisor_Chat.md; the goal as adopted: README.md, decision 67
  - the network against the stand-in on the 48 test molecules: 0.8–2.4 cm⁻¹ per kind of vibration (the product-error record of
    v1.4, modules/05_support_predictor/out/v14/E7_rungC_v14s3clean_c00_control_product_error.json)
  - the error per spectral region (TASKS 307, read 25 Nov): probes/results_m1/supervisor_chat_2026-11-25/error_by_region.txt —
    the three target kinds inside 500–3400 cm⁻¹: 1.23 on the 8 small test molecules and 1.35 on the 40 substituted ones; 89 % of
    the bands in that range within 2 cm⁻¹ on both sets; 700–1000 cm⁻¹ on the 8: 2.60; below 500 cm⁻¹: 2.18 and 2.64
  - the molecules: plan_elements.py and carbonyl_count.py in the same folder — 81 of the 167 planned reference molecules hold F
    or Cl; no carbonyl group in any planned or finished reference; 298 of the 1,082 neutral training molecules hold one; 16 of
    the 1,132 training entries are unsubstituted pure hydrocarbons; 68 of the 122 molecules of the next batch hold F or Cl
  - the four calculations taken off the list: TASKS 291 (25 Nov)
  - size: 7.9 cm⁻¹ on four molecules of 24–42 carbon atoms (slide notes; chain 42's read), nothing tested above 42
  - the list of detected molecules: the Cologne database's 'Molecules in Space' page as of October 2026 -->

Today the project was shown, for the first time, to someone whose daily work is the kind of spectrum we are trying to
predict. I had sent the slides two days before. She had read them and came with questions, one slide at a time.

I expected questions about the method. Most of them were about something else: what the method is for. By the end of the
afternoon the goal of the project had changed in three ways, and none of the three had needed a single new calculation to
find.

## What we brought

A short reminder of what the project does. A molecule's infrared spectrum is a set of bands, and each band has a position,
given in wavenumbers (cm⁻¹). A cheap calculation puts those bands in roughly the right place; an expensive one puts them
much closer. We train a neural network on the difference between the two, so that for a new molecule only the cheap
calculation is needed.

Until today the target was: every band within 3 cm⁻¹ of the better calculation, for each of four kinds of vibration. On 48
molecules the network had never seen, it gets within 0.8 to 2.4 cm⁻¹, depending on the kind. The better calculation in that
test is a stand-in, a second cheap method, because the truly expensive one exists for only a few dozen molecules so far.

## First change: a sharper goal, on fewer bands

Her target is 2 cm⁻¹, not 3. That sounds like bad news. It came with a second sentence that made it less so: not for every
vibration. Astronomers look at a particular set of bands, between about 3 and 20 micrometres in wavelength. Three of our
four kinds of vibration make those bands. The fourth kind, the slow bending and twisting of the whole carbon skeleton,
matters less.

That fourth kind is exactly where the network is worst. So we had been spending worry, and planned money, on the part of
the spectrum that counts least.

A piano tuner who is told the piece uses two octaves does not have to get all eighty-eight keys right. He does have to get
those two octaves very right.

The same afternoon we measured our error again, this time sorted by where a band lies instead of by what kind of vibration
it is. Inside her range, for the three kinds she named, the network is within 1.2 cm⁻¹ on the eight small test molecules
and 1.4 on the forty larger ones. Nine bands in ten in that range are within 2 cm⁻¹. One stretch of the range is not there
yet: between 700 and 1000 cm⁻¹ the small molecules come out at 2.6.

Two cautions belong beside those numbers. They are still against the stand-in, not against the expensive calculation and
not against a measured spectrum. And below 500 cm⁻¹, outside her range, the error is 2.2 to 2.6.

## Second change: we were computing the wrong molecules

The expensive reference calculations cost real money, so we had a plan for which molecules to do: 167 of them, chosen
cheapest first. Nothing in that rule asks whether a molecule is interesting.

She asked which elements were in them. We counted during the conversation. Of the 167, 81 contain fluorine or chlorine.
Molecules with those atoms tend to be cheap to compute, which is how they got there. They are also, for an aromatic molecule in
space, close to irrelevant. What occurs there is carbon, hydrogen, nitrogen, oxygen and sulfur.

'Cheapest first' had looked like a neutral rule. It was a rule with a taste for halogens.

There was a gap in the other direction too. She is interested in small side bands that sit next to the main aromatic bands,
one of them near 6.0 micrometres, which may come from a carbon–oxygen double bond. Our training set has 298 molecules with
such a group. Our list of expensive references has none.

So the plan changed on the spot. Four reference calculations that were still waiting in the queue, all on molecules with
fluorine or chlorine, were taken off the list; nothing that was already running was stopped. The next batch of 122, of which
68 had a halogen, is on hold until the list is drawn up again.

We also looked at the public list of molecules that have actually been detected in space. The aromatic ones on it are few:
benzene, benzonitrile, indene, some cyano-substituted relatives of naphthalene and pyrene. Her comment on that list was the
most useful sentence of the day for anyone who likes data: it is biased. Most of those detections were made with radio
telescopes, and a radio telescope only sees a molecule with an uneven distribution of charge. So the list shows the
lopsided relatives and says almost nothing about the symmetric majority. A list of what was seen is not a list of what is
there.

## Third change: how large is large

The molecules that matter to her have thirty to eighty carbon atoms. Ours are small. The largest we have tested has 42, and
on the few molecules between 24 and 42 carbon atoms our error is about 8 cm⁻¹, against 1 to 2 on the small ones.

The whole idea of the project is to learn on small molecules and carry the lesson to large ones. That carrying is, so far,
the weakest part of the evidence. She said starting small is fine. It is still the distance we have to cover.

## What we could not answer

Two questions had no answer today.

What would it cost to include charged molecules? We have not computed a single expensive reference for one, so we do not
know.

And how close is all this to a measured spectrum? Every number above compares one calculation with another. The comparison
with experiment has been made for two molecules and for our reference calculations only, not for what the product prints.

## What stays

The method did not change today. The network, the test, the numbers are what they were this morning.

What changed is what they are measured against: a tighter line, drawn around fewer bands, on molecules chosen for what
they are instead of for what they cost. All three changes came from one person asking what the result is for, and each of
them was visible in data we already had. We had just never been asked to sort it that way.
