# How do Spheres work?

This document explains how Spheres are built and their internal structure. This will allow you to customize or create your own spheres, so they could be included in SchemeSpheres releases.


## The philosophy

SchemeSpheres is born with one premise: the project must be useful to the Scheme Community even if it eventually gets stalled. The code compiled and created in this project should be highly usable by any other Scheme implementation or similar project for Gambit. For this reason, a very important aspect of SchemeSpheres is that it aims at being __non-intrusive__ to R5RS code. Modules in SchemeSpheres should work seamlessly in Gambit (with macro expansion and provided you supply the necessary dependencies).


## Features and Parts of SchemeSpheres

SchemeSpheres, as a project, is comprised of three complementary parts:

__Hygienic macros expansion, modules and conditional compilation__. These features must be usually solved together, as they all transform the code at compile-time. Many compilers, as is the case with Gambit, leave hygienic macros and related syntax expansion to the user implementation.

__Workflow tool-chain__. This is perhaps the most distinctive feature of SchemeSpheres in comparison with package systems. Essentially, the framework provides a set of tools for creating workflows, instead of forcing the user through a generic one. The workflow is divided into three parts: project skeleton generation, task running, and dependency management. These are handled currently by three tools:

* _Sfusion_ scaffolds out a new application, writing the Ssake configuration and pulling in relevant Ssake tasks and dependencies that you might need for your build.
* _Ssake_ is used to build, preview and test your project, thanks to help from tasks provided by the SchemeSpheres project for automating the work- flow.
* _Sspheres_ is used for installing dependencies from a curated set of modules and packages (called spheres, so the user can easily bring compatible code into an existing project.

__Ecosystem__. The set of aspects that help SchemeSpheres build community and collaboration around the project. Such a project benefits from the contribution of any Scheme developer that may desire to, so it must be prepared to integrate modules, tests, generators and tasks. The main points that fall under this category are:

* An structuration and set of curated modules into Spheres, with common and uniform functionality (such as FFI, ...)
* Documentation of the internal workings, plus ex- tensions capabilities.
* A central repository.

In summary, Spheres is a set of tools/scripts and libraries on top of Gambit that will take care of the most tedious work of developing software with Gambit. Some examples are:

* Install/uninstall modules easily.
* Handle dependencies automatically.
* Homogeneous and easy FFI across all the modules that need it.
* Provide libraries for general use.
* Provide application generators (templates for rapid prototyping).
* Easily run common tasks for building, debugging, testing and deploying software to different platforms, including mobile.
* Facilitate automation, such as: compilation, cross-compilation, remote debugging, asset processing...


## Hygienic Macros and Modules

SchemeSpheres uses a custom version of Psyntax, developed by _Matt Hastie_. It loads fast and expands code fast, and can be used when remotely debugging mobile devices. It supports _syntax-rules_ and _syntax-case_ (_define-macro_ is defined in terms of _syntax-case_, thus is supported as well). This opens us to full R5RS compatibility, and much of the SRFIs and amazing community code that relies on syntax-rules.

There is no module system currently in SchemeSpheres, so __namespaces__ can be used instead. That will be improved in the near future.


### Importing modules in the interactive REPL

The macros __##spheres-load__ and __##spheres-include__ take care of loading and including the necessary files to make the dependencies work:

* __(##spheres-load sphere: module)__: brings modules and all its dependencies. This is the main interface for importing modules, and takes care of importing all the code and the macros to be used in the REPL.

* __(##spheres-include sphere: module)__: brings in a macro-only file. This will include only macros to the current environment. You won't generally use this, because _##spheres-load_ will generally do this for you. But it can be used to force re-including already included files.

Alternative syntax: for modules local to the current spheres, you can use the syntax __(##spheres-load module)__.


### Importing for compilation and cross-compilation

Dependencies in the process of (cross-)compilation are handled by __ssake__, via the tasks defined in __sakefile.scm__. Details regarding the implementation of this file are given bellow. Basically, it works by expanding all the code before compilation, and setting module files in order, so headers, FFI, macro expansion and code are handled in order for every platform.


## Structure of a Sphere

A sphere is Git repository, named starting with __sphere-__. This allows the remote fetching and installation of the spheres, along all its versions. The versioning system will eventually be based on the tags functionality given by Git.

This repository consists of the following __mandatory__ items:

* __src/__: This directory contains the module files that will be built. It needs to be named like this for consistency.

* __config.scm__: This file lists the dependencies.

* __sakefile.scm__: This file implements the default or custom procedures for building the sphere, and any other kind of automation that it requires.

Also, you will generally find the following items:

* __build (generated)__: This shouldn't be in the sphere's repository, but it is generated by the Sake scripts. It contains the temporary build files. It keeps intermediate files for exploration and debugging.

* __doc/ (optional)__: Ideally, the project will contain in-depth documentation, either generated from comments, or externally specified.

* __lib/ (generated)__: This shouldn't be in the sphere's repository, but it is generated by the Sake scripts. It contains the compiled modules, ready to use.
 
* __test/ (optional)__: Optional, but recommended. Testing the code brings the sphere to the next level of reliability and maturity.

* __LICENSE__: This file contains the license applied to all files in the sphere, unless stated otherwise in the file itself or the documentation.

* __README__: This file contains instructions or links to instructions and basic usage of the sphere.


## Module files

Modules can be comprised of up to four kinds of files:

* __base files__, which have any kind of code, but any defined macros are local to this file. Example: _my-lib.scm_.
* __# files__, which are named as the module, but appending __#__ at the end. These define the names to export to the global namespace. Example: _my-lib#.scm_.
* __-macros files__, used for macros that need to be used by the code importing this module. Example: _my-lib-macros.scm_.
* __-prelude files__, used for code that needs to be used as a C header file, for instance in FFI code, where the REPL won't be able to run code that declares FFI types. It must be used only when compiling, so this code is isolated. Unless you are making code using FFI, this won't be necessary. Example: _my-lib-header.scm_.


## Inside config.scm

This file holds the basic information about the sphere. It includes:

* Name
* Dependencies

It's probably better to look at the code:

~~~ scheme
;; Name of the Sphere
(sphere: "my-sphere")
;; Dependencies, per module
(dependencies:
  ;; The module in the current sphere
  (my-module
    ;; 'Macros' file from module1 (in sphere1) needed by my-module
    (include
      (sphere2: module1-macros))
    ;; 'Prelude' file from module1 (in sphere1) needed by my-module
    (prelude
      (sphere1: module1-prelude))
    ;; 'Base' file from module1 (in sphere1) needed by my-module
    (load
      (sphere1: module1)))
~~~

Only the files declared as dependencies in a stage (include, prelude, load) will become available. You probably will use the _prelude_ file for including declarations concerning the FFI (necessary only at compile-time), the _macros_ file for including hygienic macros, and the _base_ file for the standard code (R5RS Scheme code, which can in turn use any of the functionality or structures defined in the previous two files).

You may wonder why the dependencies are defined in this file, instead of inside each module, what can lead to a long config.scm file. The main reason is to make the modules seamlessly portable among plain Gambit and SchemeSpheres. If R7RS modules get implemented in Gambit, this will change.

## Inside sakefile.scm

This file contains a series of tasks that can be invoked from the command-line via the command __ssake__.

Let's take a look inside an example:

~~~ scheme
(define modules
  '(base64
    util))

;; Compile the modules (both debug and optimized versions)
(define-task compile ()
  (for-each (lambda (m)
              (sake#compile-module m cond-expand-features: '(debug) version: '(debug))
              (sake#compile-module m cond-expand-features: '(optimize)))
            modules))

;; This will copy the built files to the /lib directory
(define-task post-compile ()
  (for-each (lambda (m) (sake#make-module-available m versions: '(() (debug)))) modules)
  (for-each (lambda (m) (sake#make-module-available m)) modules))

;; This will install the files in /lib to the system, making them available to other spheres
(define-task install ()
  (sake#install-sphere-to-system))

;; Run tests
(define-task test ()
  (sake#test-all))

;; Remove all generated files
(define-task clean ()
  (sake#default-clean))

;; Default task (if none is given through the command line)
(define-task all (compile post-compile)
  'all)
~~~

Each of these tasks can be invoked like this:

~~~
ssake <task>
~~~

Ssake comes with predefined tasks, sub-tasks and useful functions. All ssake extension modules are installed in _sake-extensions_ directory in Spheres' installation path.


## How to create a new Sphere

Basically, it all boils downs to these steps:

* Create a basic Sphere structure, and place all the modules in _/src_.
* Define the Sphere's name and dependencies in _config.scm_.
* Create the basic tasks in _sakefile.scm_.

This is probably best achieved by copying the structure from a simple Sphere. You may also want to take a look at [how to document spheres](guides/en/documenting-spheres), as an uniform and consistent documentation is one of the targets of this project.

