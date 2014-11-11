# Quickstart #


## 0. Prerequisites

SchemeSpheres has been tested in __GNU/Linux__ and __OS X__. You will need the following software installed:

*   A basic development environment (Git, a C/C++ compiler). We recommend a code editor such as [Emacs with Paredit](http://emacswiki.org/emacs/ParEdit)).
*   The [Gambit Scheme Compiler](http://gambitscheme.org). You can get the latest version from [Github](https://github.com/feeley/gambit), or just use your distribution's package. We recommend using the latest version.
*   [cURL](http://curl.haxx.se/) (probably installed in your system already).

### Installing Gambit on Linux

Downloading the latest version and running the following commands should do:

~~~
cd gambit
./configure --enable-single-host --enable-c-opt
make -j4 from-scratch
make check
sudo make install
~~~

### Installing Gambit on OSX

Gambit recommends using GCC instead of LLVM for compiling its source. We need to install it with [Homebrew](http://brew.sh/):

~~~
brew install gcc49
~~~

Now we can compile Gambit explicitly using GCC. Substitute with your GCC version.

~~~
cd gambit
CC=/usr/local/bin/gcc-4.9 ./configure --enable-single-host --enable-c-opt
make -j4 from-scratch
make check
sudo make install
~~~

___Note:___ Even though Homebrew is a very convenient way of installing Gambit, unfortunately it changes the way Gambit's directories are organized. It also removes the ability to tune Gambit's configuration, so SchemeSpheres doesn't support this way of installing Gambit at the moment. We will use Homebrew just for GCC.

___Note for Linux and OSX:___ If your system can't find Gambit after installing it, make sure to link the __gsi__ and __gsc__ binaries to _/usr/bin_. Another option is to have Gambit's bin directory (_/usr/local/Gambit-C/bin_ by default) in your system's path (both for the user and root).


## 1. Core Sphere installation

First, get the [latest release](https://github.com/alvatar/sphere-core/releases/latest) or the development version from the [Github repository](https://github.com/alvatar/sphere-core).

In the cloned or uncompressed folder, run:

    ./configure
    sudo ./bootstrap

Note for Gambit users: ./configure will create a .gambcini initialization file for you, and overwrite any previous one.


### Try it out in the REPL

Now you can start using Spheres for loading code and dependencies in the Gambit REPL. Run __gsi__ and type this in the REPL to see if it works.

~~~ scheme
(##spheres-load core: testing)
~~~

You should see:

~~~
-- including -- (core: testing-macros version: ())
-- loading -- (core: testing)
~~~

Which means that the module has been loaded, and its syntax macros are ready in the REPL. Now you can use the _lightweight testing_ module directly in the REPL.


## 2. Installing spheres

If you are going to install OpenGL and SDL2 spheres, you need to have these libraries along with their C headers installed (all SDL libraries, the OpenGL implementation + GLEW). In OSX this can be achieved easily with Homebrew:

~~~
brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf glew
~~~

In Linux, you need to use your distribution's package manager (_emerge_, _pacman_, _apt-get_...).

Spheres are installed using the __sspheres__ program. These are the spheres in the latest release:

~~~
sudo sspheres install cairo codec crypto energy fabric fusion math object opengl sdl2 strings
~~~


## 3. Using SchemeSpheres

### Interactive use

When coding interactively in the Gambit REPL, you have access to Spheres using the form:

~~~ scheme
(##spheres-load sphere: module)
~~~

This will take care of bringing and evaluating a module and its dependencies, for its immediate use in the REPL. Try running __gsi__ and writing this code:

~~~ scheme
(##spheres-load fabric: algorithm/list)
(fold + 0 '(1 2 3 4 5))
~~~

The result should be _15_ which is the sum of the list of numbers from 1 to 5.

If you really need to run a Scheme program without creating a _managed project_, you can run it from the command line like this:

~~~
gsi -e '(expander:include "test.scm")'
~~~

Compiling unmanaged projects is not supported. You need to create a container project for your code to properly compile code with SchemeSpheres. It is highly recommended to work with managed projects.


### Managed project

Creating a project gives you access to the full functionality in SchemeSpheres, allowing multiple source files and properly handling all dependencies. In this example, we will use the OpenGL 2d generator to create a project template that you can use to try out the OpenGL and SDL bindings.

~~~
sfusion new -g sdl-opengl my-new-program
~~~

Now you can compile and run it on the __Host (Linux/OSX)__, and __Android/iOS__ device. To see which tasks are available, run inside the root folder of the you just created:

~~~
cd my-new-program
ssake
~~~

__Important:__ For the Android backend to work, you need both Android SDK and NDK, plus the ANDROID\_SDK\_PATH and ANDROID\_NDK\_PATH environment variables set up pointing at these directories.

You can check for more generators with _Sfusion_:

~~~
sfusion generators
~~~

The key to working with projects is the configuration of the _sakefile.scm_ and _config.scm_ files. Currently, the best source of information is found at:

* The default, generated _config.scm_ and _sakefile.scm_ files.
* The ones found in current release Spheres.
* The [How do Spheres Work guide](http://www.schemespheres.org/guides/en/how-do-spheres-work).


## 4. Where to go from here

Soon, there will be more tutorials and guides. Meanwhile, you can start from the program skeletons provided with _Sfusion_, and check the spheres for lots of functionality ready to use.

If you want to __create a new Sphere__, and nicely integrate with SchemeSpheres, the best way to start would be knowing more about [how spheres work](http://www.schemespheres.org/guides/en/how-do-spheres-work).


### Troubleshooting

Please visit the [Troubleshooting guide](http://www.schemespheres.org/guides/en/troubleshooting).

