# **Basic compiling**

	g++ main.cpp
	
compiles main.cpp to an executable

## Set output name

	g++ -o program.elf main.cpp
	
compiles main.cpp to executable calles program.elf

### Don't link automatically

	g++ -o program.elf -c main.cpp
	
The -c option disables automatic linking

### Link static library

	g++ main.cpp lib/hello_world.a
	
compile main.cpp which uses the static library hello_world.a

or

	g++ main.cpp -static -lhello_world

### Link shared library

	g++ main.cpp -lhello_world
	
links libhello_world.so together with main.o to an executable

### Add linker search path

	g++ main.cpp -L../lib -static -lhello_world
	
searches for hello_world.a in the ../lib directory

# Create libraries

Create library from hello\_world.h and hello\_world.cpp in the lib directory

### Create static library

	g++ -c -fpic lib/hello_world.cpp
	
create hello\_world.o object file

	ar rsv hello_world.a hello_world.o
	
Use ar command to crerate hello\_world.a from hello\_world.o

	r = insert file into archive
	s = write object-file index into archive
	v = vervose output
	
### Create shared library

	g++ -c -fpic lib/hello_world.cpp
	
create hello\_world.o object file

	g++ -o libhello_world.so -shared hello_world.o
	
create libhello\_world.so shared library from .o file