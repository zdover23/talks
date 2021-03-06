..
  Copyright 2015  Fraser Tweedale.

  This work is licensed under the Creative Commons Attribution 4.0
  International License. To view a copy of this license, visit
  http://creativecommons.org/licenses/by/4.0/.


************
Introduction
************

About me
========

- Developer at Red Hat

- FreeIPA identity management and Dogtag PKI

- Mostly Python and Java at work

- Mostly Haskell for other projects


This talk
=========

- Introduce *property-based testing*; motivate with examples

- Concepts will be demonstrated in Haskell using *QuickCheck_*

- A brief look at property-based testing in other languages

- Discussion of limitations

- Alternative approaches

.. _QuickCheck: http://www.cse.chalmers.se/~rjmh/QuickCheck/


Property-based testing
======================

A property-based testing framework:

#. Gives you a way to state properties of functions
#. Gives you a way to declare how to generate arbitrary values of
   your types
#. Provides generators for standard types (usually)
#. Attempts to falsify your properties and reports counterexamples.


Applications
============

- Check laws and invariants of algorithms, data, abstractions

- Check code against a model implementation

- Properties are meaningful documentation


********
Examples
********

Reversing a list
================

.. code:: haskell

  rev :: [a] -> [a]
  rev []     = []
  rev (x:xs) = rev xs ++ [x]

  prop_RevUnit :: Int -> Bool
  prop_RevUnit x =
    rev [x] == [x]

  prop_RevApp :: [Int] -> [Int] -> Bool
  prop_RevApp xs ys =
    rev (xs ++ ys) == rev ys ++ rev xs


Expression transformation
=========================


***************
Other languages
***************

Property-based testing implementations
======================================

- Most languages have at least one implementation

- Incomplete list: https://en.wikipedia.org/wiki/QuickCheck

- Some decent or popular implementations are missing
  - Python: pyqcy_
  - Java: `Functional Java`_ (``fj.test``)

.. _pyqcy: https://pypi.python.org/pypi/pyqcy
.. _Functional Java: http://www.functionaljava.org/


Python example
==============

.. code:: python

  from pyqcy import *

  def rev(l):
      return list(reversed(l))

  @qc
  def prop_rev_unit(x=int_()):
      assert rev([x]) == [x]

  @qc
  def prop_rev_app(xs=list_(of=int), ys=list_(of=int)):
      assert rev(xs + ys) == rev(ys) + rev(xs)

  if __name__ == '__main__':
      main()


***********
Limitations
***********

Randomness
==========

.. code:: haskell

  prop_verify_eq :: Password -> Bool
  prop_verify_eq s = verify (hash s) s

  prop_verify_neq :: Password -> Password -> Property
  prop_verify_neq s s' =
    not (s == s')  ==>
      not (verify (hash s) s')


Randomness
==========

- Previous slide: what if ``hash`` truncates input before hashing?

- Some bugs are unlikely to be found with random data

- Workaround: mutate or fuzz data in domain-relevant way


Randomness
==========

.. code:: haskell

  fuzz :: Password -> Gen Password
  fuzz = {- truncation / extension / permutation / etc -}

  prop_verify_fuzzed :: Password -> Property
  prop_verify_fuzzed s =
    forAll (fuzz s) (prop_verify_neq s)


Failure cases
=============

- ``Arbitrary`` is great for generating random *valid* data

- How to specify behaviour given *invalid* data?


Failure cases
=============

.. code:: haskell

  dump :: JSON   -> String
  load :: String -> Maybe JSON

  prop_dumpLoad :: JSON -> Bool
  prop_dumpLoad a = load (dump a) == Just a

  loadSpec :: Spec
  loadSpec = describe "load" $
    it "fails on bogus input" $
      load "bogus" `shouldBe` Nothing


Conclusion
==========

- Property-based testing is *true automated testing*

  - More thorough testing in less time ($$$)

  - Relieves developer of burden of finding and manually writing
    tests for corner cases

- Properties are *meaningful documentation*

- *The best test data is random test data*, but...

  - a bit of domain-specific non-randomness is sometimes useful

  - examples still have their place.


**********************
Alternative approaches
**********************

Exhaustive testing
==================

*The best test data is all of the data*

- Check that property holds for all values

- Supports *existential* properties

- Available in several languages

  - SmallCheck_ (Haskell),
    smallcheck4scala_,
    autocheck_ (C++),
    ocamlcheck_,
    `python-doublecheck`_

.. _SmallCheck: http://hackage.haskell.org/package/smallcheck
.. _smallcheck4scala: https://github.com/dwhjames/smallcheck4scala
.. _autocheck: https://github.com/thejohnfreeman/autocheck
.. _ocamlcheck: https://github.com/jamii/ocamlcheck
.. _python-doublecheck: https://github.com/kennknowles/python-doublecheck


Proof
=====

*The best test data is no test data*

- Some languages have theorem-proving capabilities

- Properties become theorems; no proof, no program

- Program *extraction* to other languages


Resources
=========

- *QuickCheck: A Lightweight Tool for Random Testing of Haskell
  Programs* (2000) Koen Claessen, John Hughes: http://is.gd/mpsY7G

- *Automated Unit Testing your Java using ScalaCheck* by Tony
  Morris: http://is.gd/j0R7qq

- UCSD CSE 230 lecture: http://is.gd/0YfxOr

- *QuickCheck: Beyond the Basics* by Dave Laing: http://is.gd/pGKnhg

- Recommended Haskell learning path:
  https://github.com/bitemyapp/learnhaskell


Thanks for listening
====================

Copyright 2015  Fraser Tweedale

This work is licensed under the Creative Commons Attribution 4.0
International License. To view a copy of this license, visit
http://creativecommons.org/licenses/by/4.0/.

Slides
  https://github.com/frasertweedale/talks/
Email
  ``frase@frase.id.au``
Twitter
  ``@hackuador``


*********
Questions
*********
