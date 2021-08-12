---
id: m81bZ5tChpkOEhUIvo3pO
title: '2021-08-12'
desc: ''
updated: 1628774178832
created: 1628773950351
---

## Worse Is Better
- source: [Worse Is Better](https://www.dreamsongs.com/WIB.html)

JSON vs XML, javascript vs anything else, the go programming language - the tech world is filled with examples of "worse is better". The title is great clickbait but also points at a truth - people like to start off with simple incomplete things then complete non-simple thing.

> A complex system that works is invariably found to have evolved from a simple system that worked. A complex system designed from scratch never works and cannot be patched up to make it work. You have to start over, beginning with a working simple system. - John Gall

### Excerpt
  I and just about every designer of Common Lisp and CLOS has had extreme exposure to the MIT/Stanford style of design. The essence of this style can be captured by the phrase the right thing. To such a designer it is important to get all of the following characteristics right:

    Simplicity -- the design must be simple, both in implementation and interface. It is more important for the interface to be simple than the implementation.
    Correctness -- the design must be correct in all observable aspects. Incorrectness is simply not allowed.
    Consistency -- the design must not be inconsistent. A design is allowed to be slightly less simple and less complete to avoid inconsistency. Consistency is as important as correctness.
    Completeness -- the design must cover as many important situations as is practical. All reasonably expected cases must be covered. Simplicity is not allowed to overly reduce completeness.
    I believe most people would agree that these are good characteristics. I will call the use of this philosophy of design the MIT approach. Common Lisp (with CLOS) and Scheme represent the MIT approach to design and implementation.

  The worse-is-better philosophy is only slightly different:

    Simplicity -- the design must be simple, both in implementation and interface. It is more important for the implementation to be simple than the interface. Simplicity is the most important consideration in a design.
    Correctness -- the design must be correct in all observable aspects. It is slightly better to be simple than correct.
    Consistency -- the design must not be overly inconsistent. Consistency can be sacrificed for simplicity in some cases, but it is better to drop those parts of the design that deal with less common circumstances than to introduce either implementational complexity or inconsistency.
    Completeness -- the design must cover as many important situations as is practical. All reasonably expected cases should be covered. Completeness can be sacrificed in favor of any other quality. In fact, completeness must be sacrificed whenever implementation simplicity is jeopardized. Consistency can be sacrificed to achieve completeness if simplicity is retained; especially worthless is consistency of interface. https://www.dreamsongs.com/WIB.html


