# SDL2 and OpenGL(/ES) #


This guide collects the knowledge gathered around the use of these two technologies for building applications.


## Compilation and execution

* In order to function properly, OpenGL module must be loaded __after__ SDL2. Otherwise, you might encounter strange issues. OSX Mavericks seems to have problems creating the OpenGL context, Linux doesn't.


