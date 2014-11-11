# Optional positional and named parameters in Spheres #

First of all, you will need to import core macros. For the REPL:

~~~ scheme
(##spheres-include core: base-macros)
~~~

For a Fusion project:

~~~ scheme
(sphere: "my-sphere")
(dependencies:
 (my-module
  (include (core: base-macros))))
~~~


## How does it work?

It works just like SRFI-89, but with a slight variation: __keyword parameters (named parameters) aren't followed by a variable name__, because the keyword itself is turned into the variable (thus removing the redundancy and directly translating into DSSSL):

~~~ scheme
(define* (g a (b a) (my-key: (* a b))) (list a b my-key))
;; works like:
;; (define g (lambda (a #!optional (b a) #!key (my-key (* a b))) (list a b my-key)))

(define* (h1 a (default:) (other-key: 5) . r) (list a other-key default r))
;; works like:
;; (define h1 (lambda (a #!key default (other-key 5) #!rest r) (list a other-key default r)))
~~~


## Explanation

There is no standard solution to write portable _optional and positional parameters_. There are different options, depending on the Scheme system, but there are two that are most commonly used: __[DSSSL](http://en.wikipedia.org/wiki/Document_Style_Semantics_and_Specification_Language)__ and __[SRFI-89](http://srfi.schemers.org/srfi-89/srfi-89.html)__.

In the Gambit world, the DSSSL extension is more extended, since it is implemented by the compiler. However paradoxical, SRFI-89 reference documentation is implemented for Gambit by Marc Feeley, who writes: _Unfortunately several people, including the author of this SRFI, feel that the DSSSL formal parameter list syntax is messy. Although the DSSSL syntax has widespread support among the implementations of Scheme with optional parameters we think there is a high likelihood that these implementations of Scheme may evolve to include this proposal (a majority of the implementors of these systems have confirmed that they are willing to consider implementing a new approach). Therefore this SRFI specifies a parameter passing mechanism that has similar functionality to DSSSL, but a more elegant formal parameter list syntax._

SchemeSpheres relies on a slight variation of SRFI-89, that removes redundancy and directly translates to the DSSSL equivalent. Being a macro extension, it can be modified in the future to translate to both DSSSL and SRFI-89 with ease, choosing the available or best performing option. As is the case for Gambit, it translates to DSSSL, thus removing the need of procedures at runtime.


## Differences with SRFI-89

This solution could be easily turned into SRFI-89-compatible code by adding a variable after the keyword, named exactly like the keyword. For example:

~~~ scheme
;; Turn Spheres' syntax
(define* (g a (b a) (my-key: (* a b))) (list a b my-key))
;; into SRFI-89
(define* (g a (b a) (my-key: my-key (* a b))) (list a b my-key))
~~~



