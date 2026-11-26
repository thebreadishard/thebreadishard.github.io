---
layout: post
title: "What is in a basis set?"
date: 2026-11-26 18:58:34 +0100
categories: plan05 method
---

<!-- Draft written 26 Nov 2026 on the user's word ("Kun je een blog in lekentaal voorbereiden over dit onderwerp?"). Published 26 Nov 2026 in the evening on the user's word ("Publiceer vanavond").
The benzene example was taken out the same day on the user's word (a generic lay post).
Sources:
  - shapes and function counts of 6-31G*, cc-pVDZ/TZ/QZ/5Z: counted from the files pyscf 2.15.1 ships (scratch script
    basis_shapes.py, 26 Nov): C 6-31G* (10s4p1d)->[3s2p1d]; cc-pVDZ (9s4p1d)->[3s2p1d]; cc-pVTZ (10s5p2d1f)->[4s3p2d1f];
    cc-pVQZ (12s6p3d2f1g)->[5s4p3d2f1g]; cc-pV5Z (14s8p4d3f2g1h)->[6s5p4d3f2g1h]; benzene 102 (six-component d) / 114 / 264 / 510 / 876
  - ano-pVQZ: the Basis Set Exchange file in the plan repo, probes/results_m1/ano_step_benzene_2026-11-25/basis/ano-pvqz.nwchem.txt:
    C (16s10p5d4f3g)->[5s4p3d2f1g], every contracted function over all primitives of its shape; H (8s6p4d3f)->[4s3p2d1f]
  - which functions of cc-pVnZ are blends and which are single primitives: Dunning 1989, doi 10.1063/1.456153
  - DOIs checked at Crossref on 26 Nov 2026 -->

Every calculation in this project starts with a choice that sounds like a detail and is not one: the
[basis set](/glossary/#basis-set). Names such as 6-31G\*, cc-pVTZ and ano-pVQZ appear in many posts here, usually
without a word about what is inside them. This post opens the box. By the end you should be able to say how these sets differ
in the way they are built, and what one has that another lacks.

## Why a calculation needs building blocks

A molecule is a handful of atomic nuclei with a cloud of electrons around them. The whole job of a quantum-chemical calculation
is to find the shape of that cloud, because the shape decides everything else: how strong the bonds are, how the molecule
vibrates, which infrared light it absorbs.

A computer cannot store an arbitrary shape. So the programme is given a fixed collection of simple shapes and may only decide
how much of each one to use. A painter who owns twenty tubes of paint works the same way: every colour in the painting is a
mixture of those twenty, and a colour that cannot be mixed from them cannot be painted. The collection of simple shapes is the
basis set. Each shape in it is called a basis function.

Everything below is about what is in the tubes.

## The raw material: bells

The simplest basis function is a bell: highest at the nucleus of an atom and fading smoothly with distance. Its mathematical
name is a Gaussian. Bells are used for one practical reason: the sums a programme has to work out between millions of pairs of
functions can be done quickly with them.

A single bell is a poor picture of the electrons around an atom. The real cloud is sharply peaked at the nucleus and fades slowly
far away. A bell is too flat in the middle and dies too fast at the edge. The cure is to add several bells of different widths:
narrow ones for the region near the nucleus, wide ones for the outskirts.

That gives the first distinction to remember.

- A **primitive** is one raw bell with one width.
- A **contracted function** is a fixed blend of several primitives, mixed once by the people who designed the basis set. The
  calculation uses the blend as a single building block. It may decide how much of the blend to use, but it may not change what
  is in it.

In the painter's terms: primitives are pigments, contracted functions are the tubes the factory filled. Fewer tubes means a
faster calculation. How well the factory mixed them decides how much accuracy that speed costs.

## Shapes with directions

A bell is the same in all directions. Electrons in a molecule are not: they pile up between atoms and lean towards neighbours.
So basis sets also contain functions with a direction built in. They are named by letters:

- **s**: round, one function;
- **p**: a dumbbell, in three orientations;
- **d**: a four-leaf clover and its relatives, five orientations;
- **f**, **g**, **h**: ever more lobes, in seven, nine and eleven orientations.

A lone carbon atom only needs s and p. The d, f and g functions are there for the atom inside a molecule: they let its cloud bend
and shift when a neighbour pulls on it. They are called polarisation functions. Each step up the alphabet adds more functions
than the last one, which is why large basis sets grow so fast.

## Three questions that tell basis sets apart

With those words in hand, every basis set can be described by three questions.

1. **How many functions does each outer electron orbital get?** The outer (valence) electrons make the bonds. One function per
   orbital gives the cloud a fixed size. Two let it swell or shrink, three or four give finer control. The jargon is double,
   triple and quadruple zeta: the D, T and Q in the names.
2. **Which directional shapes are on board?** Only d on carbon, or f and g as well? Anything at all on hydrogen?
3. **Who mixed the blends, and for which method?** The same number of functions can be mixed for a simple method or for an
   accurate one, from few primitives or from many.

Here is carbon in the sets this project uses, and what that adds up to for benzene, a ring of six carbons and six hydrogens.

| basis set | contracted functions on carbon | built from primitives | functions on benzene |
|---|---|---|---|
| 6-31G\* | 3 s, 2 p, 1 d | 10 s, 4 p, 1 d | 102 |
| cc-pVDZ | 3 s, 2 p, 1 d | 9 s, 4 p, 1 d | 114 |
| cc-pVTZ | 4 s, 3 p, 2 d, 1 f | 10 s, 5 p, 2 d, 1 f | 264 |
| cc-pVQZ | 5 s, 4 p, 3 d, 2 f, 1 g | 12 s, 6 p, 3 d, 2 f, 1 g | 510 |
| ano-pVQZ | 5 s, 4 p, 3 d, 2 f, 1 g | 16 s, 10 p, 5 d, 4 f, 3 g | 510 |
| cc-pV5Z | 6 s, 5 p, 4 d, 3 f, 2 g, 1 h | 14 s, 8 p, 4 d, 3 f, 2 g, 1 h | 876 |

(Each p counts three times, each d five times and so on, for the orientations. 6-31G\* counts its d six times by an old
convention.)

## 6-31G\*: lean, from the 1970s

The name is the recipe. The inner electrons of carbon, which take no part in bonding, get one function blended from 6
primitives. Each outer orbital gets two functions: one blended from 3 primitives and one single wide primitive. That is the
"31", and it is the double-zeta idea. The star adds one set of d functions on carbon
([Hehre, Ditchfield and Pople 1972](https://doi.org/10.1063/1.1677527);
[Hariharan and Pople 1973](https://doi.org/10.1007/BF00533485)).

What it does not have: hydrogen gets two round functions and nothing with a direction, and carbon has no f. The set was designed
to be cheap with the simpler methods of its day, and with them it still does good work on large molecules.

## The cc-pV*n*Z family: a ladder

Thom Dunning's sets of 1989 were designed for the accurate methods, the ones that follow how electrons avoid each other
([Dunning 1989](https://doi.org/10.1063/1.456153)). "cc" stands for correlation consistent, "p" for polarised, "V" for valence,
and the letter before the Z is the rung: D, T, Q, 5.

Two things set this family apart from 6-31G\*.

First, hydrogen gets directional functions too. In cc-pVDZ it has a p, in cc-pVTZ a d as well.

Second, the family is a ladder with a rule. Each rung adds one more function of every shape already present and one new shape
on top: DZ stops at d, TZ adds f, QZ adds g, 5Z adds h. Dunning chose the additions so that all functions that come in on the
same rung matter about equally for the result. Because the steps are regular, the answers move regularly too, and one can watch
them settle from rung to rung.

Inside, the blends are sparse. In cc-pVQZ carbon has 55 functions. Only three kinds of them are real blends: two s functions
and one p set, each mixed to match an orbital of the lone carbon atom as a simple method sees it. Everything else, including
every d, f and g, is a single raw bell with a well-chosen width.

## ano-pVQZ: the same count, mixed differently

Now compare the two rows of the table with 510 functions. cc-pVQZ and ano-pVQZ put exactly the same number of functions of
exactly the same shapes on each atom. A calculation with either costs nearly the same in the expensive part. They differ only in
the third question: how the blends were mixed
([Neese and Valeev 2011](https://doi.org/10.1021/ct100396y)).

- **More pigment.** ano-pVQZ draws on 16 s primitives where cc-pVQZ has 12, on 10 p where cc-pVQZ has 6, and on 3 g where
  cc-pVQZ has 1.
- **Every function is a blend.** Each of the five s functions is a mixture of all 16 s primitives. Each g function is a mixture
  of all three. No function is a single bell.
- **Mixed for the accurate method.** ANO means atomic natural orbital, an idea of
  [Almlöf and Taylor (1987)](https://doi.org/10.1063/1.451917). The designers first describe the lone atom with an accurate
  method in the full set of primitives. That calculation itself shows which blends the electrons use most. Those blends are kept,
  in order of use, and the rest is dropped.

So cc-pVQZ gives the calculation mostly raw bells and lets it do its best. ano-pVQZ hands it 55 functions per carbon that were
each shaped in advance by an accurate picture of the atom. The price is preparation work: every function drags all its
primitives along, so the first stage of the calculation takes longer. The number of building blocks in the costly stage stays the
same.

## Why not always take the largest?

Because of the price. The accurate methods slow down steeply as the number of functions grows: doubling the functions on the
same molecule makes such a calculation more than ten times longer. Benzene has 114 functions in cc-pVDZ and 876 in cc-pV5Z.
A calculation that takes an afternoon in the first can be out of reach in the second.

So the choice of a basis set is always a trade. A lean set lets one treat a large molecule or use an accurate method. A rich
set gives the electron cloud the freedom it needs, for a molecule small enough to afford it. And between two sets of the same
size, the question is which one was mixed with more care for the job at hand.

## What to remember

- A basis set is a fixed box of building blocks for the electron cloud. The calculation chooses amounts, never the blocks.
- Sets differ in how many functions each outer orbital gets (D, T, Q, 5), in which directional shapes they carry (d, f, g, h,
  and whether hydrogen gets any), and in how their blends were mixed.
- 6-31G\* is lean: two functions per outer orbital, one d on carbon, nothing directional on hydrogen.
- The cc-pV*n*Z family is a ladder built for accurate methods. Each rung adds a function of every shape and one new shape.
- ano-pVQZ has the same count as cc-pVQZ. It differs in that every function is a blend of many primitives, mixed from an
  accurate picture of the atom.
- A larger set costs steeply more, so the choice is a trade between the molecule's size, the method and the accuracy.
