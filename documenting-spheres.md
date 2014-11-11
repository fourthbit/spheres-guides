# Documenting Spheres #

This document demonstrates how Spheres should be documented, using a LAML SchemeDoc subset.

These are the rules of the format:

* A comment block is a consecutive sequence of comment lines, which are not separated by empty, blank or non-comment lines.
* The number of semicolons may vary from one comment line to another within a comment block.
* The number of semicolons used for a documentation comment is not significant.
* The __documentation comment character__ is a distinguished character: __'!'__.
* A documentation comment is a comment block which is identified by one or more documentation comment characters on the first line of a comment block.
* The documentation comment characters must appear __after the semicolon(s)__, only separated by spaces or tabs. The comment extraction facility 'eats' and eliminates a possible single white space following the __'!'__ or __';'__.
* The documentation comment character is __only recognized in the first line__ of a comment block (and next to a semicolon). Documentation comment characters located other places within a comment count as normal characters.

These are the types (hierarchy) of documentation comments:

* Comments marked with a single documentation comment character __(!)__ are used as comments of define forms. You can either place the comment before the define form or after the name/signature of the define form.
* Comments marked with two documentation comment characters __(!!)__ are used for text sections between documented forms. The first sentence (separated with '.' and a space) is taken as the title of the section.
* Comments marked with three documentation comment character __(!!!)__ are used as the introduction to a documentation document. There should, as such, only be one documentation abstraction comment in a Scheme file. The documentation abstraction comment should be the first part of Scheme source file.


~~~ scheme
;!!! .title A Scheme source file with documentation-mark comments.
;; .author Alvaro Castro-Castilla
;; This is a Scheme file with a few simple functions.
;; The functions are written and organized with the purpose
;; of demonstrating the SchemeSpheres documentation


;;!! The fac and fib functions.

;;! Calculate the factorial of n.
;; .parameter n An integer
;; .pre-condition The integer must be non-negative.
;; .returns n!
(define (fac n)
  (if (= 0 n) 1 (* n (fac (- n 1)))))

;;! Calculated the fib function.
;; Notice that this is a very inefficient
;; implementation.
;; .parameter n An integer
;; .pre-condition The integer must be non-negative.
;; .returns The n't fiabonaci number.
(define (fib n)
  (cond ((or (= n 0) (= n 1)) 1)
        (else (+ (fib (- n 1)) (fib (- n 2))))))
~~~
