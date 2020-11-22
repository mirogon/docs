# **g++ and gcc compiler guide**

## **IMPORTANT**

**All g++ CLI commands should work with gcc also**

Filetypes:

1. source code files
	- .cpp
    - .c
2. object files (compiled sources)
	- .o (UNIX)
    - .obj (WINDOWS)
3. static library files
	- .a (UNIX)
    - .lib (WINDOWS)
4. shared/dynamic library files
	- .so (UNIX)
    - .dll (WINDOWS)
5. executables
	- .* (UNIX, mostly none, elf, elif)
	- .exe (WINDOWS)

## Basic compiling

	g++ main.cpp
	
compiles main.cpp to an executable

	g++ main.cpp other.cpp

compile main.cpp to main.o and other.cpp to other.o and automatically link them together

### Set output name

	g++ -o program.elf main.cpp
	
compiles main.cpp to executable called program.elf

### Don't link automatically

	g++ -o program.elf -c main.cpp
	
The -c option disables automatic linking

### Link static library

	g++ main.cpp hello_world.a
	
compile main.cpp which uses the static library hello_world.a

or

	g++ main.cpp -static -lhello_world

### Link shared library

	g++ main.cpp -lhello_world
	
links libhello_world.so together with main.o to an executable

### Add linker search path

	g++ main.cpp -L../lib -static -lhello_world
	
searches for libraries in the ../lib directory

## Create libraries

Create library from hello\_world.h and hello\_world.cpp in the lib directory

### Create static library

	g++ -c -fpic hello_world.cpp
	
create hello\_world.o object file

	ar rsv hello_world.a hello_world.o
	
Use ar command to crerate hello\_world.a from hello\_world.o

	r = insert file into archive
	s = write object-file index into archive
	v = vervose output
	
### Create shared library

	g++ -c -fpic hello_world.cpp
	
create hello\_world.o object file

	g++ -o libhello_world.so -shared hello_world.o
	
create libhello\_world.so shared library from .o file