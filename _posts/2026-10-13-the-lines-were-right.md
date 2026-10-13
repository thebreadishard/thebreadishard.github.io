---
layout: post
title: "The lines were right, their brightness was not"
date: 2026-10-13 06:43:40 +0200
categories: plan05 progress
---

<!-- Written 13 Oct 2026 at the user's request ("Ja, schrijf voorstel 1 als concept"); published the same day on the user's word
     ("Publiceer maar").
     Numbers trace to (CapstonePlan repo, plans/05_delta-probed-ir-pipeline):
     probes/results_m1/cc_apt_field_2026-10-12/REGISTRATION.md (lever 6: F2, F3, R3H, commit bbf24555; lever 6b read, commit 0ee66f4f),
     probes/results_m1/cc_apt_field_2026-10-12/mode_shape_diagnosis.md (exploratory, commit e3ca64b4),
     GoalGathering/notes/PreRegistration_2026-10-12_Anchor_Set_Three.md (six-anchor baseline, R-A/R-B lines, price; commit da2ff60a). -->

The last post ended a story about brightness with a promise. Our test of whether the network gets the *heights* of the infrared lines right
had been run on benzene, and benzene turned out to be too symmetric to tell us anything: its symmetry fixes the heights whatever the
correction does. The test had to move to a less symmetric molecule. It has now run on three of them, and the answer was not the one we
wanted.

**Two halves of a line.** A line in an infrared spectrum has a position and a height. The position is set by how stiff a vibration is. The
height is set by how much the molecule's electric charge sloshes back and forth while it vibrates, and that depends on two things: which
atoms move, and how far, in that vibration (its *shape*), and how the electrons' charge follows each atom when it moves. The second part is
a table with one entry per atom and direction. If you keep that table fixed and change only the shapes, any change in the heights comes from
the shapes alone. That is the test: the same expensive charge table for every column, and three sets of shapes. The true ones from the
coupled-cluster calculation, the cheap ones from DFT with no correction, and the network-corrected ones.

**A cheaper road to the expensive charges.** The charge table can be measured two ways. You can nudge every atom and watch the molecule's
dipole change, which is what our anchor runs had done for benzene. Or you can switch on a weak electric field and watch the forces on the
atoms change. By a basic symmetry of second derivatives the two give the same table, and the second way needs only six force calculations:
the field along x, y and z, in both directions. For pyridine, whose anchor run predates the dipole recording, re-running all 66 nudges would
have cost about 55 hours. The field road cost 47 minutes.

We checked the new road on benzene against the old one before trusting it. They agree to 1.0 × 10⁻⁴ of an electron charge, where we had set
the bar at 10⁻³. There is also a check that costs nothing: moving the whole molecule sideways cannot change its dipole, so each column of the
table must add up to zero. For the field road the columns add up to about 10⁻¹⁰ or less on all four molecules, for the nudging road on benzene to 3 × 10⁻⁴.
Fluorobenzene took 1 hour 44 minutes and benzonitrile 3 hours 11, far more than the 50 minutes I had estimated: each force calculation took
about twice as long as benzene's, because one inner step needed up to 26 rounds.

**The read.** When the network is adapted to the expensive anchors, it is allowed to retune its last layer. One setting, which we call λ,
says how strongly those retuned numbers are pulled back towards what the network learned on hundreds of cheap molecules. Our standard since
7 October is λ = 1, chosen then from a read of line *positions* alone. λ = 0 lets the last layer retune freely. The score is the error in the
heights, with lines paired by the shape of their motion as in the last post: 0 is perfect.

| molecule | no correction | network, standard (λ = 1) | network, free (λ = 0) |
|---|---|---|---|
| pyridine | 0.262 | 0.310 | 0.106 |
| fluorobenzene | 0.104 | 0.172 | 0.098 |
| benzonitrile | 0.258 | 0.443 | 0.252 |

Each network number is the mean of three independently trained models. On all three molecules the standard setting gives *worse* heights
than no correction at all. The rule we had written down before the run called that a fail, and it is one. The free setting is level with no
correction on two molecules and much better on pyridine.

At the same time the positions get better. On a score that compares whole spectra, positions and heights together, where 1 means
identical, the standard setting beats no correction for benzonitrile, 0.62 against 0.48, and the free setting does better still, 0.82. So the network moves the lines to better places while it
spoils the motions behind them. That is not a contradiction, and the reason is the most interesting part of the week.

**Where it goes wrong.** We looked at where the height error sits. On all three molecules nearly all of it sits in the vibrations that move
atoms out of the molecular plane, and inside that group mostly in one pair: the two strongest out-of-plane lines, between 680 and
770 cm⁻¹. In pyridine they sit at 718 and 758 cm⁻¹, forty wavenumbers apart.

Two vibrations that close in pitch behave like a pair of coupled pendulums. Each true vibration is a mixture of the two, and the
mixture is set by how strongly they are coupled compared with how far apart their pitches are. When the gap is small, a small error in the
coupling turns the mixture a long way. The pitches hardly notice: turning a pair inside itself moves each frequency very little. The shapes
change a lot, and with them the heights. Brightness flows from one member of the pair to the other. In pyridine the 758 line falls from 23
to about 6 (in the usual unit, km/mol), and the 718 line rises from 35 to 51. In benzonitrile the 683 line doubles, from 17 to 34, and the 771
line drops from 47 to about 27.

The fingerprint of such a turn is that both members of the pair are wrong by the same amount. They are: in pyridine each matches its true
shape with an overlap of 0.89 under the standard setting, against 0.93 for the uncorrected DFT. And the uncorrected DFT is already turned in
the same direction, by about 16 degrees in pyridine. The standard setting turns it further, to 19 or 20 degrees. The network does not
introduce the error; it makes an existing one larger.

This diagnosis was made after the read, to explain it, so it is a lead and not a result. It does fit what we already knew: the out-of-plane
family has been the network's weakest since September, and a line position can look acceptable while the pair it belongs to is turned.
Only the heights can show the turn. That is why the brightness test had to wait for a molecule with less symmetry than benzene.

**One knob, or more evidence.** The obvious move would be to switch the knob to λ = 0. The numbers invite it. On the six anchors we already
had, the free setting is also better on three of the four families of line *positions*: ring vibrations 3.4 against 4.6 cm⁻¹, out-of-plane
C–H bends 10.7 against 14.1, the remaining motions 10.0 against 13.5. Only the C–H stretches prefer the standard, 2.6 against 4.9.

The person running this project was uneasy about that move, and said so: so much should not hang on one parameter. I think the unease is
right. The knob matters this much because six anchor molecules hold too little evidence about the out-of-plane couplings, so the fine-tuning
has nothing to steer by and the knob decides in its place. Flip it and the next set of molecules may flip it back. The cure for too little
evidence is more evidence.

So the decision was more anchors. Eight new molecules with low symmetry, chosen so that their vibrations mix:
- quinoxaline, azulene and acenaphthylene first;
- then quinoline and isoquinoline;
- then indole, benzofuran and quinazoline.

They include nitrogen in the ring, a five-membered ring and the five-and-seven ring pair of azulene, all families the project needs. Each one
gets the full coupled-cluster treatment and its charge table by the field road. The first three have been computing on a rented server
since last night, for about three and a half days and under €20. All eight cost roughly €40.

What would count as success is written down before the first of them finishes, and it is read once, when all eight are in. First: with the new anchors added, the error of the
out-of-plane C–H bends on the six original anchors must fall by at least a fifth. If it falls by less than a twentieth, anchors are not the
lever for that family, and a change to how the network treats the couplings is next. Second: on pyridine, fluorobenzene and benzonitrile,
the standard setting must beat no correction on the heights for at least two of the three. If it does not, the knob gets chosen again, this
time on positions and heights together rather than on positions alone.

**What this is not.** Three molecules, one pair of lines in each, and a diagnosis made after the fact. The expensive charge table itself is
computed with the smaller of our two basis sets, the same limitation the line positions had until the composite step of last week. And the
standard setting stays the standard until the new anchors have been read, because changing it now would be choosing on the data that
prompted the change.
