# Object Systems available in SchemeSpheres


## define-structure (native in Gambit)

### Summary of the object system

* Simplest object model available in Gambit.
* Very easy to use: this model is very similar to C structures.
* No inheritance.
* Native: no overhead.


### Syntax and procedures

~~~
(define-structure type-name <field>...)
~~~

The define-structure expands into a set of definitions of the following procedures:

* __make-name__: A k argument procedure which constructs a new record from the value of its k fields.
* __name?__: A procedure which tests if its single argument is of the given record type.
* __name-field__: For each field, a procedure taking as its single argument a value of the given record type and returning the content of the corresponding field of the record.
* __name-field-set!__: For each field, a two argument procedure taking as its first argument a value of the given record type. The second argument gets assigned to the corresponding field of the record and the void object is returned.

Record data types have a printed representation that includes the name of the type and the name and value of each field. Record data types can not be read by the read procedure.

### Examples

~~~ scheme
(define-structure point2d x y)
~~~

Creates the type __point2d__ and the following procedures:

* __make-point2d__: constructor
* __point2d?__: predicate
* __point2d-x__: accesor for field x
* __point2d-x-set!__: modifier for field x
* __point2d-y__: accesor for field y
* __point2d-y-set!__: modifier for field y




## define-record-type (native implementation of SRFI-9 in Gambit)

### Summary of the object system

* Slightly more complex: it allows customization of the names for the generated procedures.
* It doesn't support inheritance.
* The names of the record type, predicate, constructor and accessors/mutators are all listed explicitly in the source.
* Native: no overhead.


### Syntax and procedures

~~~
<record-type-definition> = (define-record-type
                            <type-name>
                            [ <constructor> <predicate> ]
                            <field> ...)

<constructor> = <constructor-name>
              | (<constructor-name> <field-tag> ...)

<predicate> = identifier

<field> = <field-tag>
        | (<field-tag>)
        | (<field-tag> <accessor-name>)
        | (<field-tag> <accessor-name> <modifier-name>)

<field-tag> = identifier
<*-name> = identifier
~~~

An instance of __define-record-type__ is equivalent to the following definitions:

* __\<type name\>__ is bound to a representation of the record type itself. Operations on record types, such as defining print methods, reflection, etc. are left to other SRFIs.
* __\<constructor name\>__ is bound to a procedure that takes as many arguments as there are __\<field tag\>__s in the __(\<constructor name\> ...)__ subform and returns a new <type name> record. Fields whose tags are listed with __\<constructor name\>__ have the corresponding argument as their initial value. The initial values of all other fields are unspecified.
* __\<predicate name\>__ is a predicate that returns #T when given a value returned by __\<constructor name\>__ and #f for everything else.
* Each __\<accessor name\>__ is a procedure that takes a record of type __\<type name\>__ and returns the current value of the corresponding field. It is an error to pass an accessor a value which is not a record of the appropriate type.
* Each __\<modifier name\>__ is a procedure that takes a record of type __\<type name\>__ and a value which becomes the new value of the corresponding field; an unspecified value is returned. It is an error to pass a modifier a first argument which is not a record of the appropriate type.

Please, refer to the [SRFI-9 document](http://srfi.schemers.org/srfi-9/srfi-9.html) for more information.

### Examples

~~~ scheme
(define-record-type :point2d
  (build-point2d x y)
  is-point2d?
  (x point2d-get-x point2d-set-x)
  (y point2d-get-y point2d-set-y))
~~~

Creates the type __:point2d__ and the following procedures:

* __build-point2d__: constructor
* __is-point2d?__: predicate
* __point2d-get-x__: accesor for field x
* __point2d-set-x__: modifier for field x
* __point2d-get-y__: accesor for field y
* __point2d-set-y__: modifier for field y



## define-type (native in Gambit)

### Summary of the object system

* More advanced and configurable.
* Extends the functionality of define-record-type.
* Basic single inheritance through extension mechanism: ancetors' predicates return true for inherited, inherits fields.
* Native: it's the most lightweight object system that supports building object hierarchies via some form of inheritance.

### Syntax and procedures

~~~
<type-definition> = (define-type <type-name> [<attribute> ...] <field> ...)

<attribute> = id: <uuid>
            | constructor: <constructor-name>
            | constant-constructor: <constant-constructor-name>
            | predicate: <predicate-name>
            | extender: <extender-name>
            | prefix: <prefix-name>
            | macros:
            | type-exhibitor: <type-exhibitor-name>
            | implementer: <implementer-name>
            | opaque:
            | <field-attribute>

<field> = identifier
        | <field-attribute> ... <field> ...
        | (identifier [<accessor-name> [<modifier-name>]])
        | (identifier <field-attribute> ...)

<field-attribute> = read-write:
                  | read-only:
                  | equality-test:
                  | equality-skip:
                  | printable:
                  | unprintable:
                  | init: <expr>

<*-name> = identifier
~~~

Setting this option will allow you seeing generated code for define-type:

~~~ scheme
(set! ##define-type-expansion-show? #t)
~~~

Attributes:

* __id: <uuid>__ specifies an identifier that is used to name this precise type uniquely, which is used during (de)serialization via object->u8vector and u8vector->object. When used, the type definition is called non-generative and two programs can share it.
* __constructor: <constructor-name>__ specifies the name of the generated constructor function. The definition of the constructor (and predicate) can be avoided by specifying #f as its name. This is useful when only one instance of this type is needed (such as simple exception objects, the empty list, etc).
* __constant-constructor: <constant-constructor-name>__ specifies a constructor that creates constants (available at compile-time). Can be used only with non-generative type definitions.
* __predicate:__ <predicate-name> specifies the name of the generated function/macro for testing if a value has this type.
* __extender: <extender-name>__ specifies the name of the definition form to use to define a subtype.
* __prefix: <prefix-name>__ will prefix all generated functions/macros names.
* __macros:__ All operations generated are expressed as macros (except that in the case of a generative type definition the binding of the type descriptor to a variable is done with a "define").
* __type-exhibitor: <type-exhibitor-name>__ if specified will create a function with no arguments that returns the type descriptor of this type without the need of creating an instance.
* __implementer: <implementer-name>__ adds a definition of the parameterless macro "implementer-name" that expands to all non-macro definitions produced by the define-type. This is useful to distinguish the two binding times (compile-time and run time). It can be used to avoid duplicate definitions when a file containing a define-type is included in multiple files (I think this problem should be solved through the module system, but this is how the problem can be solved without a module system). For example, let's say the file point-def.scm contains the definition of the point type, and files a.scm and b.scm need to manipulate points.
* __opaque:__ This flag causes a change in the behavior of equal? and equal?-hash. When the opaque: flag is not used, two structures are equal? if they have the same type and each field is equal? in both structures. When the opaque: flag is used, two structures are equal? only if they are eq? (i.e. the same instance).


Field attributes:

* __read-write:__ Default option. A setter procedure/macro is generated.
* __read-only:__ No setter is generated, effectively rendering the field read-only after it is set by the constructor.
* __equality-test:__ Default option. Testing type instances using equal? will check the values of this field as part of that test.
* __equality-skip:__ Testing type instances using equal? will ignore the values of this field.
* __printable:__ Default option. The field will be printed when the type instance itself is.
* __unprintable:__ The field will not be printed when the type instance is printed.
* __init: <expr>__ Set the initial value to literal value cst. Note that this does not make the field optional in the constructor: the field won't be provided using the constructor.


All field attributes except init: can be specified at define-type level and will affect all fields. A field can specify its own field attributes to override default attributes. Field may have non-conventional getter and setter names, specified after field name in that order. The attributes can be specified inside the field definition or can prefix a field definition (in which case they apply to all fields from that point):

~~~ scheme
(define-type point
 read-only:                                 
 x
 (y unprintable:))
~~~

is equivalent to

~~~ scheme
(define-type point
 (x read-only:)
 (y read-only: unprintable:))
~~~

Non-generative type definitions are obtained by explicitly specifying a "globally unique identifier" (GUID) for the type. This way, even different programs which use the same type definition can agree on the identity of that type. The type descriptor is not stored in a variable because is is now constant and can be denoted with a literal here needed (i.e. the expression '#<type #2 point>). The type-id is also known and used directly in the definition of point?. A "constant constructor" is also defined (as a macro), so that constants can be created. All evaluations of a given constant constructor yield the same (eq?) object. The arguments of the constant constructor must be self-evaluating or quoted constants.

Instances of non-generative types can be communicated (write then read) by dumping all their fields, including the type descriptor. Here is an example for the object (make-point 44 55):

~~~ scheme
(output-port-readtable-set!
 (current-output-port)
 (readtable-sharing-allowed?-set
  (output-port-readtable (current-output-port))
  'serialize))

(define-type point x y)
(pretty-print (make-point 44 55) (current-output-port))
~~~

Most of the explanations are taken from [this document](https://mercure.iro.umontreal.ca/pipermail/gambit-list/attachments/20090226/af2ee44c/attachment-0001.txt), which you can use to further clarification.

### Examples

#### Attributes

~~~ scheme
(define-type point
 constructor: make-point
 predicate: is-a-point?
 read-only: x
 read-write: (y get-y set-y!))
~~~

#### Inheritance

~~~ scheme
(define-type point2d
 extender: define-type-of-point2d
 x
 y)

(define-type-of-point2d point3d z)

(define p3 (make-point3d 11 22 33))

(point2d? p3) ;; -> #t
(point3d? p3) ;; -> #t
~~~

#### Combine custom field accessor/modifier with an initializer

~~~ scheme
(define-type point
 (x get-x set-x init: 9)
 y)
~~~

#### The _opaque:_ flag

~~~ scheme
(define-type foo
  opaque:
  a
  b)

(define x (make-foo 11 22))
(define y (make-foo 11 22))

(pp (equal? x y)) ;; -> #f
(pp (equal?-hash x)) ;; -> 2
(pp (equal?-hash y)) ;; -> 3

(define-type foo
  a
  b)

(pp (equal? x y)) ;; -> #t
(pp (equal?-hash x)) ;; -> 206628681
(pp (equal?-hash y)) ;; -> 206628681
~~~



## SRFI-99 Records __(object: record)__

### Summary of the object system

These system is implemented as a module in SchemeSpheres __(object: record)__, and uses the low-level facilities of Gambit for defining record types. These are the main features that brings to the native Gambit systems:

* Adds data inheritance and inspection to SRFI-9 functionality.
* High flexibility: you can manually generate each procedure and customize the methods.
* Uses the native functionality to create the structures adding minimal overhead.
* It's defined in three layers: _procedural_ (generation of: constructor, predicate, accessor, mutator), _inspection_ (query structures' internals), _syntactic_ (syntactic sugar for structure definition).

Most of the work for bringing SRFI-99 to Gambit has been done by Arthur T Smyles.


### Syntax and procedures

This is the syntax brought by the syntactic layer, but is not necessary to use it (you can generate the procedures manually via the generators).

~~~
<record-type-definition> = (define-record-type <type-spec>
                            <constructor-spec>
                            <predicate-spec>
                            <field-spec> ...)

<type-spec> = <type-name>
            | (<type-name> <parent>)

<constructor-spec> = #f
                   | #t
                   | <constructor-name>
                   | (<constructor-name> <field-name> ...)

<predicate-spec> = #f
                 | #t
                 | <predicate-name>

<field-spec> = <field-name>
             | (<field-name>)
             | (<field-name> <accessor-name>)
             | (<field-name> <accessor-name> <mutator-name>)

<parent> = <expression>

<*-name> = identifier
~~~

These are the procedures in the procedural layer (rtd stands for _record type descriptor_):

~~~ scheme
(make-rtd <name> <fieldspecs> [<parent>])
(rtd? <obj>)
(rtd-constructor <rtd> [<fieldspecs>])
(rtd-predicate <rtd>)
(rtd-accessor <rtd> <field>)
(rtd-mutator <rtd> <field>)
(rtd-deconstructor <rtd> <field>) ; extension function, will return all fields as values
~~~

Finally, these are the procedures in the inspection layer:

~~~ scheme
(record? <obj>)
(record-rtd <record>)
(rtd-name <rtd>)
(rtd-parent <rtd>)
(rtd-field-names <rtd>)
(rtd-all-field-names <rtd>)
(rtd-field-mutable? <rtd> <field>)

;; extensions of the srfi-99
(rtd-uid <rtd>)
(rtd-sealed? <rtd>)
(rtd-opaque? <rtd>)
(rtd-field-flag-printable? <flags>)
(rtd-field-flag-mutable? <flags>)
(rtd-field-flag-equality? <flags>)
(rtd-field-flag-init? <flags>)
~~~

For more information on the standard API to manipulate the generated records as well as manually generate procedures, please refer to [SRFI-99 documentation](http://srfi.schemers.org/srfi-99/srfi-99.html).

### Examples

#### Using the procedure generators and shadowing parent's fields

~~~ scheme
(define :point
  (make-rtd 'point '#((mutable x) (mutable y))))

(define make-point (rtd-constructor :point))

(define point? (rtd-predicate :point))
(define point-x (rtd-accessor :point 'x))
(define point-y (rtd-accessor :point 'y))
(define point-x-set! (rtd-mutator :point 'x))
(define point-y-set! (rtd-mutator :point 'y))

(define p1 (make-point 1 2))
(point? p1) ; => #t
(point-x p1) ; => 1
(point-y p1) ; => 2
(point-x-set! p1 5)
(point-x p1) ; => 5

(define :point2
  (make-rtd 'point2 '#((mutable x) (mutable y)) :point))

(define make-point2
  (rtd-constructor :point2))
(define point2? (rtd-predicate :point2))
(define point2-xx (rtd-accessor :point2 'x))
(define point2-yy (rtd-accessor :point2 'y))

(define p2 (make-point2 1 2 3 4))
(point? p2) ; => #t
(point-x p2) ; => 1
(point-y p2) ; => 2
(point2-xx p2) ; => 3
(point2-yy p2) ; => 4
~~~

#### Custom constructor with the constructor generator

~~~ scheme
(define make-point/abs
  (let ((maker (rtd-constructor :point)))
    (lambda (x y)
      (maker (abs x) (abs y)))))
~~~


## Type-classes __(object: type-class)__

### Summary of the object system

In essence, type-classes define a set of functions. Then, using this "interface", other functions can be defined (called __type-class polymorphic functions__), similar to multimethods. Type-classes are parameterized types, acting like "types of types" in a sense: they allow you to describe the constraints a type should obbey (__type-class constraints__), and any instance of the typeclass (a type) must conform with them. In other words, type-classes correspond to the set of types which have certain operations defined for them, and type class polymorphic functions work only for types which are instances of the type class(es) in question.

Type-classes can be used with records. They are similar to interfaces in OOP. Both define a set of types which implement a specified list of operations. However, there are a couple of important ways in which type classes are more general:

* Generally in OOP, when a class is defined any interfaces it implements must be declared. Type-class instances, on the other hand, are declared separately from the declaration of the corresponding types, and can even be put in a separate module.
* The types that can be specified for type class methods are more general and flexible than the signatures that can be given for interface methods, especially when multi-parameter type-classes enter the picture.

While the previous object systems are focused on defining structures made of compound data (records), type-classes are focused on the functions defined for these structures. However, both type-classes and records can be combined to model more traditional OOP and extend its capabilities through the parameterized types.

The type-classes module can be summarized as follows:

* The model is inspired by Haskell's type-classes.
* It introduces first-class types (the _type-class instances_), which brings powerful parametric types through type-class constraints.
* It's a way of creating generic functions (_type-class polymorphic functions_), defined for different types of the same type-class. You don't need a function with a different name for each type. Furthermore, how this function should work for each type doesn't need to be described in one place (as you could achieve with _case-lambda_). New data types can be added later without modifying previous code.
* It offers a level of abstraction superior to CLOS-style generic functions and OOP in certain respects.
* It's compatible with record types.

The layer of type-classes adds an indirection to funtion calls: all functions are wrapped by extra lambdas.

### Syntax and procedures

~~~
(define-class <field-form> ...)

(define=> (<procedure-name> <class-form> ...) . body)

(lambda=> (<class-form> ...) . body)

(with (<instance-form> ...) . body)

(import-instance <instance-form> ...)

<field-form> = field-label
             | (<superclass-name> field-label)

<class-form> = <class-name>
             | (<class-name> <prefix-symbol>)

<instance-form> = (<class-name> <instance-expr>)
                | (<class-name> <instance-expr> <prefix-symbol>)
~~~

### Further explanation

This is an extract from the code and explanations by the original author (__André Van Tonder__), with some modifications to adapt it to SchemeSpheres:

Type classes provide a level of abstraction superior to CLOS-style generic functions in certain respects. For example, a collection abstraction:

~~~ haskell
class (Collection a c) where
  empty : c
  insert : a c -> c
  ...
~~~

cannot be neatly expressed with OO or generic functions. The problem is the signature of *empty* -- generic functions cannot dispatch on the expected result type. The same is true for *return* in the signature

~~~ haskell
class Monad m a where
  return : a -> m a
  ...
~~~

Unfortunately type classes rely on static type inference to resolve these ambiguities, which makes their implementation problematic in Scheme. However, they can be simulated by "dictionary passing" as happens under the hood in Haskell. Although in its raw form, this technique is very burdensome to the programmer, it can be substantially alleviated by a few macros.

In compensation, since the instances (dictionaries) of "type classes" are now first class, the Scheme programmer gets a much more powerful abstraction tool. Indeed, Haskell type classes have some serious shortcomings, making them unsuitable for expressing many very natural abstractions. For example, the integers form a monoid under addition, and also under multiplication. In Haskell, the integers can only be an instance of a monoid class in one way. This problem does not arise with first class instances. Consider also

~~~ haskell
class (Field f) (Abelian g) => Vectorspace f g where
  dimension : Integer
  ...
~~~

This cannot be expressed in Haskell, since the type of __dimension__ would be ambiguous. Also, it is impossible to express common operations such as taking the direct sum or tensor product of two vector spaces. Again, this is no problem with first class instances as defined below. 

Here is a short tutorial: 

~~~ scheme
;; We define an equality and a collection class, and a set class which inherits from both:

;; class (Eq a) where
;; egal? : a a -> boolean
;; not-egal? : a a -> boolean = \x y -> not egal? x y
(define-class <Eq> egal?
                   not-egal?)

(define (default-Eq egal?)
  (make-<Eq> egal?
             (lambda (x y) (not (egal? x y)))))

;; class (Collection a c) where
;; empty : c
;; insert : a c -> c
;; ...

(define-class <Collection> empty
                           insert
                           fold)

;; class (Eq a) (Collection a) => (Set a s) where

(define-class <Set> (<Eq> eq)
                    (<Collection> coll))

;; We can now define a qualified *set-member?* function.
;; Notice that in the scope of define=> we can automatically
;; use operations defined in all superclasses of <Set>:
;; (in this case fold from <Collection> and egal? from <Eq>):

;; set-member? : (Set a s) => a s -> Bool
(define=> (set-member? <Set>)
 (lambda (a c)
    (call/cc (lambda (break)
               (fold (lambda (x seed)
                       (if (egal? a x)
                           (break #t)
                           #f))
                     #f
                     c)))))   

;; Just for fun, let's define a heterogenous union.
;; Notice how we can specify a prefix to append to
;; the imported operations so as to disambiguate:

;; heterogenous-union : (Set a sa) (Set b sb) => sa sb -> sa
(define=> (heterogenous-union (<Set> a.) (<Set> b.))
  (lambda (x y)
    (b.fold (lambda (elem accum)
              (a.insert elem accum))
            x
            y)))

;; To illustrate, let's define some instances.
;; As opposed to Haskell, our instances are first class
;; entities, which can be named. 

;; num-Eq = instance Eq Num where ...
(define num-Eq (default-Eq =))

;; eqv-Eq = instance Eq a where ...
(define eqv-Eq (default-Eq eqv?))

;; list-Set = instance (Eq a) => Set a [a] where
;; empty = '()
;; ...
(define (list-Set eq)
  (let ((srfi-fold fold))
    (letrec* ((empty '())
              (fold srfi-fold)
              (insert (lambda (x s)
                        (if ((set-member? this) x s)
                            s
                            (cons x s))))
              (this (make-<Set> eq
                                (make-<Collection> empty
                                                   insert
                                                   fold))))
             this)))

;; num-Set = instance Set Num [Num]
(define num-Set (list-Set num-Eq))   
;; eqv-Set = instance Set a [a]
(define eqv-Set (list-Set eqv-Eq))

;; Examples of use. With opens the instance dictionary in
;; lexical scope:
(with ((<Set> num-set))
  (insert 1
          (insert 2
                  (insert 3
                          (insert 1 empty))))) ;==> (2 3 1)

;; Import, on the other hand, imports the bindings in the dictionary
;; into the toplevel. As with *with* and *define*, we can specify
;; prefixes to disambiguate:
(import-instance (<Set> num-Set num.)   
                 (<Set> eqv-Set))       

(define num-test (num.insert 1 (num.insert 2 num.empty)))
     
(define eqv-test (insert 'a (insert 'b empty)))

((heterogenous-union eqv-Set num-Set) eqv-test num-test) ;==> (2 1 a b)
~~~

### Examples


#### Object-orientation example, combining with simple records

~~~ scheme
;;; Simple Shapes OO example

;; class (Shape a) where ...
;; get-x : a -> Number
;; get-y : a -> Number
;; set-x : a x -> void
;; set-y : a y -> void
;; draw : a -> void
(define-class <Shape> get-x get-y set-x! set-y! draw)
 
(define-structure point x y)

;; draw-position : (Shape a) => a -> void
(define=> (draw-position <Shape>)
  (lambda (a)
    (display "Shape (")
    (display (get-x a))
    (display ", ")
    (display (get-y a))
    (display ")\n")))

;; instance Shape point where ...
(define point-shape
  (make-<Shape> point-x 
                point-y
                point-x-set!
                point-y-set!
                (lambda (a)
                  ((draw-position point-shape) a))))

(define-structure circle x y radius)

;; instance Shape circle-data
;; where ...
(define circle-shape
  (make-<Shape> circle-x
                circle-y
                circle-x-set!
                circle-y-set!
                (lambda (c)
                  (display "Circle: ")
                  ((draw-position circle-shape) c)                   
                  (display " radius = ")
                  (display (circle-radius c))
                  (display "\n"))))


;;; Tests

(define test-point (make-point 1 2))

(with ((<Shape> point-shape))
      (draw test-point))

(define test-circle (make-circle 7 7 7))

(with ((<Shape> circle-shape))
      (draw test-circle))

;; draw-shapes : [exist a. ((Shape a) and a)] -> void
(define (draw-shapes lst)
  (for-each (lambda (sa.a)
              (with ((<Shape> (car sa.a)))
                (draw (cdr sa.a))))
            lst))

(draw-shapes (list (cons point-shape test-point)
                   (cons circle-shape test-circle)
                   (cons point-shape test-point)
                   (cons circle-shape test-circle)))

;;; Extending the Shape class

(define-class <Shape+> (<Shape> shape) translate)

;; translate : (Shape a) => a dx dy -> void
(define=> (translate <Shape>)
  (lambda (a dx dy)
    (set-x! a (+ (get-x a) dx))
    (set-y! a (+ (get-y a) dy))))

(define point+
  (make-<Shape+> point-shape
                 (translate point-shape)))

(define circle+
  (make-<Shape+> circle-shape
                 (translate circle-shape)))

;;; Tests

(with ((<Shape+> circle+))
      (translate test-circle 7 7)
      (draw test-circle))

(import-instance (<Shape+> circle+))

(translate test-circle 7 7)
(draw test-circle)
~~~

#### More complex types

~~~ scheme
;;; Equality

;; class (Eq a) where
;; egal? : a a -> boolean
;; not-egal? : a a -> boolean
(define-class <Eq> egal?
                   not-egal?)

(define (default-Eq egal?)
  (make-<Eq> egal?
             (lambda (x y) (not (egal? x y)))))

(define num-Eq (default-Eq =))
(define eqv-Eq (default-Eq eqv?))
(define chr-Eq (default-Eq char=?))

;;; Collections

;; class (Collection a c) where
;; empty : c
;; insert : a c -> c
;;   ...
(define-class <Collection> empty
                           insert
                           fold)

;; contains? : (Eq a) (Collection a c) => a c -> Bool
(define=> (contains? <Eq> <Collection>)
  (lambda (a c)
    (call/cc (lambda (break)
               (fold (lambda (x seed)
                       (if (egal? a x)
                           (break #t)
                           #f))
                     #f
                     c)))))

;; class (Eq a) (Collection a) => (Set a s) where
(define-class <Set> (<Eq> eq)
                    (<Collection> coll))

;; set-member? : (Set a s) => a s -> Bool
(define=> (set-member? <Set>)
  (contains? eq coll))   

;; instance (Eq a) => Set a [a]
(define (list-Set eq)
  (let ((srfi-fold fold))
    (letrec* ((empty '())
              (fold srfi-fold)
              (insert (lambda (x s)
                        (if ((set-member? this) x s)
                            s
                            (cons x s))))
              (this (make-<Set> eq
                                (make-<Collection> empty
                                                   insert
                                                   fold))))
             this)))

;; instance Set Num [Num]
(define num-Set (list-Set num-Eq))

;; instance Set a [a]                            
(define eqv-Set (list-Set eqv-Eq))

;; instance Set char string where ...
(define chr-Set
  (letrec* ((empty "")
            (fold (lambda (f seed s)
                    (let loop ((acc seed)
                               (i 0))
                      (if (= i (string-length s))
                          acc
                          (loop (f (string-ref s i) acc)
                                (+ i 1))))))
            (insert (lambda (x s)
                      (if ((set-member? this) x s)
                          s
                          (string-append (string x) s))))
            (this (make-<Set> chr-Eq
                              (make-<Collection> empty
                                                 insert 
                                                 fold))))
           this))

;; list->Set : (Set a s) => [a] -> s
(define=> (list->set <Set>)
  (lambda (lst)
    (foldl (lambda (x s) (insert x s))
           empty
           lst)))

;; heterogenous-union : (Set a sa) (Set b sb) => sa sb -> sa
(define=> (heterogenous-union (<Set> a.) (<Set> b.))
  (lambda (x y)
    (b.fold (lambda (elem accum)
              (a.insert elem accum))
            x
            y)))

;;; Extending the Set class

;; class (Set a s) => Set+ a s where
;; union : s s -> s
;; ...
(define-class <Set+> (<Set> set) union
                                 member?
                                 list->set)

;; default-Set+ : (Set a s) -> (Set+ a s)
(define (default-Set+ sa)
  (make-<Set+> sa
               (heterogenous-union sa sa)
               (set-member? sa)
               (list->set sa)))

(define num-Set+ (default-Set+ num-Set))
(define chr-Set+ (default-Set+ chr-Set))

;;; Tests

(with ((<Set> num-Set))
      empty) ;==> ()

((heterogenous-union eqv-Set chr-Set)
 '(1 2 3 4 5)
 "abcde") ;==> (#\e #\d #\c #\b #\a 1 2 3 4 5)

(with ((<Set+> num-Set+ num.)
       (<Set+> chr-Set+ chr.))
      (values
       num.empty
       chr.empty)) ;==> () ""

(import-instance (<Set+> num-Set+ num.)
                 (<Set+> chr-Set+ chr.))

(num.union '(1 2 3 4 5)
           '(3 4 5 6 7)) ;==> (7 6 1 2 3 4 5)

(chr.list->set '(#\a #\b #\c #\d #\a)) ;==> "dcba"

(import-instance (<Set+> num-Set+))

empty ;==> ()

(union '(1 2 3 4 5)
       '(2 3 4 5 7)) ;==> (7 1 2 3 4 5)

(list->set '(1 1 2 3 4 3 4)) ;==> (4 3 2 1)
~~~

#### Extensible interpreter

~~~ scheme
;; Uses variant types as defined here

(define-syntax define-type
  (syntax-rules ()
    [(_ type (name field ...) ...)
     (begin
       (define-constructors type ((name field ...) ...)))]))

(define-syntax define-constructors
  (syntax-rules ()
    [(define-constructors type ((name field ...) ...))
     (define-constructors type ((name field ...) ...) (name ...))]
    [(define-constructors type ((name field ...) ...) names)
     (begin
       (define-constructor type (name field ...) names)
       ...)]))

(define-syntax define-constructor
  (syntax-rules ()
    [(_ type (name field ...) names)
     (define (name field ...)
       (cons 'type
             (lambda names
               (name field ...))))]))

(define-syntax cases
  (syntax-rules ()
    [(_ type x [(name field ...) exp]
          ...)
     ((cdr x) (lambda (field ...) exp)
              ...)]))

(define (type-of x) (car x))
 
;; class Interpreter Exp a where
;; interpret : Exp a -> Number
(define-class Interpreter interpret)

(define-type base-expression (base))

;; instance Interpreter base-expression where
;; interpret (base) = error "No semantics"
(define base-interpreter
  (make-Interpreter
     (lambda (exp)
       (cases base-expression exp
         ((base) (error "No Semantics"))))))

;; type abel-expression a = base base-expression
;;                        | num Number
;;                        | plus a a         
(define-type abel-expression
  (base base-exp)
  (num val)
  (plus lhs rhs))

;; instance (Promise (Interpreter a)) => Interpreter abel-expression a where
;;    interpret (base base-exp) = interpret base-exp
;;    interpret (num val) = val
;;    interpret (plus lhs rhs) = + (interpret lhs) (interpret rhs)
(define (abel-interpreter inta)
  (make-Interpreter
   (lambda (exp)
     (with ((Interpreter (force inta)))
       (cases abel-expression exp
         ((base base-exp)
          (with ((Interpreter base-interpreter))
                (interpret base-exp)))
         ((num val) val)
         ((plus lhs rhs)
          (+ (interpret lhs) (interpret rhs))))))))

(define-type ring-expression
  (abel abel-exp)
  (mult lhs rhs))

;; Instance (Promise (Interpreter a)) => Interpreter ring-expression a where
;;    ...
(define (ring-interpreter inta)
  (make-Interpreter
   (lambda (exp)
     (with ((Interpreter (force inta)))
       (cases abel-expression exp
         ((abel abel-exp)
          (with ((Interpreter (abel-interpreter inta)))
                (interpret abel-exp)))
         ((mult lhs rhs)
          (* (interpret lhs) (interpret rhs))))))))


;; type final = ring-expression final
;; ::::: implies, by the above, that
;; instance Interpreter final 
(define final-interpreter
  (ring-interpreter (delay final-interpreter)))

;; Test
(with ((Interpreter final-interpreter))
  (interpret
     (mult (abel (num 2)) (abel (num 2)))))
~~~



## Prototypes (object: prototype)

### Summary of the object system

Prototypes are a different approach to object modeling, based on the Self/Javascript model. It can be used instead of records and type-classes. In this approach, behaviour reuse (inheritance) is performed cloning existing objects that serve as prototypes. The language feature that supports prototype-based programming is called __delegation__. This is the process in which the right function is selected to be dispatched for a given message passed to an object.

Objects in prototype-based programming can encapsulate both data and functionality, and serve as prototypes for other objects. The runtime of the system takes care of dispatching the correct method or finding the right piece of data simply by following a series of delegation pointers (from object to its prototype) until a match is found.

In a prototype-based object system, an object is just a set of slots. A slot has a name and a value, or a handler procedure which reacts on messages associated with the slot. Some slots are special, so-called parent slots, whose use will become apparent shortly.

Objects receive messages, consisting of a selector (a symbol), and zero or more arguments. When an object receives a message, the object searches for a slot whose name is equal (tested with eq?) to the message selector. When it finds such a slot, it invokes the slot's handler, or returnes the slot's value, as appropriate. When the slot is not in the object, all objects in parent slots are queried for that slot. That is the process mentioned earlier called _delegation_.

An object is created by cloning an existing object. The new object is empty except for a single parent slot, which points to the cloned object. This way, the new object behaves exactly like the old one until its particular behaviour or data are changed by the programmer.

The main features of this system are:

* It's a system that seems more familiar to OO programmers: everything is an object, and slots can hold data or procedures. It's inspired by Self and Javascript prototypes.
* It's a very flexible and a fully dynamic system, but at the same time extremely lightweight.
* On the other hand, delegation introduces indirection and some runtime checks when compared with plain records, thus bringing and inevitable performance hit.
* Uses Self-like delegation semantics (build prototype, class, multiple-inheritance, whatever OO style you want)
* Minimal syntax, minimal mechanism.
* Naming is not required and it uses no global tables.

The prototypes system is a port of the project called TinyTalk, by __Kenneth A Dicke__.


### Syntax and procedures

~~~
($ <message> <object> <arguments>) -- send a message to an object
(<< <object> <message> <arguments>) -- alternate syntax

(object (<field-spec> ...) <method-spec> ...) -- produces an object instance
(prototype-object? thing) -- universal predicate
(define-predicate <pred?>) -- defines a universal predicate
(string<< thing) -- returns a string describing thing


($ field-names <obj>) -- names of setter/getter field value access
($ shallow-clone <obj>) -- shallow-clone object (does *not* clone delegate(s))
($ deep-clone <obj>) -- deep-clone object (does clone delegate(s))
($ add-method! <obj> <name-sym> <proc>)
($ remove-method! <obj> <name-sym>)
($ methods-alist <obj>) -- all (name . method) pairs
($ lookup <obj>) -- symbol-> method or #f [single level (no delegate)]

($ ->string <obj>) -- descriptive string (should be implemented by user)
~~~


### Examples

This is a tutorial by the original author, Kenneth A Dickey (edited):

~~~ scheme
;; Make a point -- just an anonymous object.
(define p1 (object ((x 1)(y 2))))

;; Look at the object with the default "printer"
(string<< p1) ;; -> "#[instance x: 1 y: 2]"

;; Do the same thing as a message
($ ->string p1) ;; -> Unhandled exception

;; We should add an implementation of ->string!
($ add-method! p1
   '->string
   (lambda (self)
     (string-append "(point "
                    (string<< ($ x self))
                    " "
                    (string<< ($ y self))
                    ")")))

($ ->string p1) ;; -> "(point 1 2)"


(define-predicate point?)
(point? 3) ;; -> #f
(point? p1) ;; -> #f
;; All "method" lambdas take a "self" argument:
($ add-method! p1 'point? (lambda (self) #t))
(point? p1) ;; -> #t

;; Use Self style cloning to get a new point
(define p2 ($ shallow-clone p1))

($ ->string p2) ; -> "(point 1 2)"

;; Setters and getters use the same syntax.
($ x p2 23) ;; -> 23
($ y p2 45) ;; -> 45
($ ->string p2) ;; -> "(point 23 45)"
($ x p2) ;; -> 23

($ x p2) ;; -> 23

;; define an object all in one place
(define (new-point x y)
  (unless (and (integer? x) (integer? y))
    (error 'new-point
           "use only integer values for points"
           x y))
  (object ((x x) (y y))
    ((point? self) #t)
    ((->string self)
     (string-append "(new-point "
                    (string<< ($ x self))
                    " "
                    (string<< ($ y self))
                    ")"))
    ((distance-between self other)
     (unless (point? other)
       (error 'point:distance-between
              "Needs two points"
              self other))
     (let ((dx (- ($ x self) ($ x other)))
           (dy (- ($ y self) ($ y other))))
       (sqrt (+ (* dx dx) (* dy dy)))))
    ((=? self other)
     (unless (point? other)
       (error 'point:=?
              "Don't know how compare point to non-point"
              self other))
     (and (= ($ x self) ($ x other)) (= ($ y self) ($ y other))))
    ;; more methods can go here
    ))

(define p3 (new-point 100 200))
($ distance-between p3 p2) ;; -> 173.07223925286226

;; Note that p2 does NOT have a distance-between method
($ distance-between p2 p3) ;; -> Unhandled exception

;; Looking at methods, you might note that in methods we only
;; access objects by calling methods.

;; Let's use a delegate to model a class based OO system.
(define new-point
  (let ((proto-point
          (object () ;; methods only
             ((point? self) #t)
             ((->string self)
              (string-append "(new-point "
                             (string<< [$ x self])
                             " "
                             (string<< [$ y self])
                             ")"))
             ((distance-between self other)
              (unless (point? other)
                (error 'point:distance-between
                       "Needs two points"
                       self other))
              (let ((dx (- ($ x self) ($ x other)))
                    (dy (- ($ y self) ($ y other))))
                (sqrt (+ (* dx dx) (* dy dy)))))
             ((=? self other)
              (unless (point? other)
                (error 'point:=?
                       "Don't know how compare point to non-point"
                       self other))
              (and (= ($ x self) ($ x other)) (= ($ y self) ($ y other)))))))
    ;; each object gets its own "state" (x y)
    (lambda (x y)
      (unless (and (integer? x) (integer? y))
        (error 'new-point "use only integer values for points" x y))
      (object ((x x) (y y))
              ;; but delegates to shared code ["delegate" is special]
              ((delegate self) proto-point)))))

(define p1 (new-point 21 30))
(define p2 (new-point 300 400))

(string<< p1) ;; -> "(new-point 21 30)"

($ distance-between p1 p2) ;; -> 463.401553730671
 
;; Now, we can add behavior to the "class" [prototype] of points
($ add-method! ($ delegate p1) 'color (lambda (self) 'green))

;; Now p1 and p2 share the new method
($ color p2) ;; -> green
($ color p1) ;; -> green

;; But adding a method to p1 does not add it to p2
($ add-method! p1 'name (lambda (self) 'p1))

($ name p1) ;; -> p1
($ name p2) ;; -> Unhandled exception

;; So what is this deep-clone thing?
;; deep-clone is for delegation based inheritance.
;; I.e. one makes delegates for objects one inherits from.

;; In this case one does NOT want to share ancestors
(define p3 ($ deep-clone p2))

($ x p3 45) ;; -> 45
($ ->string p3) ;; -> "(new-point 45 400)"

($ add-method! ($ delegate p3) 'color (lambda (self) 'orange))
($ color p3) ;; -> orange
($ color p2) ;; -> green

(define p4 ($ shallow-clone p3))
($ color p4) ;; -> orange
($ color ($ shallow-clone p1)) ;; -> green

;; "Inheritance by delegation" means making an object
;; from which one inherits.

;; Here rectangle inherits from point
(define (new-rectangle x y width height)
  (let ((my-point (new-point x y)))
    (object ((width width) (height height))
      ((rectangle? self) #t)
      ((->string self)
       (string-append "(new-rectangle x: "
                      (string<< ($ x self))
                      " y: "
                      (string<< ($ y self))
                      " width: "
                      (string<< ($ width self))
                      " height: "
                      (string<< ($ height self))
                      ")"))
        ((delegate self) my-point))))

(define r (new-rectangle 10 20 300 400))
(string<< r) ;; -> "(new-rectangle x: 10 y: 20 width: 300 height: 400)"
($ x r 20) ;; -> 20
(string<< r) ;; -> "(new-rectangle x: 20 y: 20 width: 300 height: 400)"

;; Of course the point one inherits from is still there.
(string<< ($ delegate r)) ;; -> "(new-point 20 20)"

;; To properly clone a rectangle, you need to use deep-clone.
(define r2 ($ deep-clone r))
($ x r2 11) ;; -> 11
(string<< r2) ;; -> "(new-rectangle x: 11 y: 20 width: 300 height: 400)"
(string<< r) ;; -> "(new-rectangle x: 20 y: 20 width: 300 height: 400)"

;; If shallow-clone had been used, the my-point instance
;; would have been shared and the behavior would have
;; been wrong.


;; Multiple inheritance.

(define (every*? pred? . rest)
  (let loop ((list rest))
    (cond
     ((null? list) #t)
     ((pred? (car list)) (loop (cdr list)))
     (else #f))))

(define (new-color red green blue)
  (let ((color-ok?
         (lambda (c) (and (integer? c) (<= 0 c 255)))))
    (unless (every*? color-ok? red green blue)
      (error 'new-color
             "colors must be integers between 0 and 255"
             red green blue))
    (object ((r red) (g green) (b blue))
       ((color? self) #t)
       ((->string self)
        (string-append "(new-color r: "
          (string<< ($ r self)) " g: "
          (string<< ($ g self)) " b: "
          (string<< ($ b self)) ")")))))

(define (new-color-rect x y width height red green blue)
  (let* ((color (new-color red green blue))
         (rect (new-rectangle x y width height))
         (delegates (list color rect)))
    (object ()
       ((color-rect? self) #t)
       ((->string self)
        (string-append "(new-color-rect "
                       (number->string ($ x self)) " "
                       (number->string ($ y self)) " "
                       (number->string ($ width self)) " "
                       (number->string ($ height self)) " "
                       (string<< color)
                       ")"))
       ((delegate self) delegates))))

(define cr (new-color-rect 1 2 20 30 0 200 0))
($ x cr 5) ;; -> 5
($ g cr 100) ;; -> 100
(string<< cr) ;; -> "(new-color-rect 5 2 20 30 (new-color r: 0 g: 100 b: 0))"

(define-predicate color?)
(define-predicate rectangle?)

(and (color? cr) (rectangle? cr) (point? cr)) ;; -> #t
~~~



