# Overview

# Main
## 1. <a href="#1"> How to build with CMake from the command line</a>
## 2. <a href="#2">Basic CMakeLists.txt file</a>
## 3. <a href="#3">Building Libraries</a>
## 4. <a href="#4">Using Libraries</a>
## 5. <a href="#5">Variables</a>
## 6. <a href="#6">Print</a>
## 7. <a href="#7">Conditions</a>
## 8. <a href="#8">Compiler Options</a>
## 9. <a href="#9">Configure Source Files</a>
## 10. <a href="#10">Loops</a>
## 11. <a href="#11">Platform Dependency</a>
## 12. <a href="#12">Hardware Dependency</a>
## 13. <a href="#13">Find Libraries</a>
## 14. <a href="#14">Execute Terminal/Console Commands</a>
## 15. <a href="#15">File Operations</a>
## 16. <a href="#16">Try to Compile</a>
## 17. <a href="#17">Functions and Macros</a>
## 18. <a href="#18">CMake Modules</a>
## 19. <a href="#19">Multiple CMakeLists.txt files</a>
## 20. <a href="#20">Installing</a>
## 21. <a href="#21">Hiding Symbols</a>
## 22. <a href="#22">Cross Compiling</a>
## 23. <a href="#23">Packaging Libraries using CPack</a>
## 24. <a href="#24">Test Software</a>
## 25. <a href="#25">Relevant Variables</a>
## 26. <a href="#26">Useful Examples and Additional Info</a>

# Other

<div id="1"></div>

# **Command line**
### configure project at current path with CMakeLists.txt also at the current path. Use cmake with two following dots if you are in a build directory but the CMakeLists.txt file is in the parent directory
	
	cmake .
	
### builds the project in the current directory (Debug or Release Folder)
**Build options can be seen here: https://cmake.org/cmake/help/v3.14/manual/cmake.1.html#build-tool-mode**
	
	cmake --build .
	
### build is not only for building, it can also be used to perform different kinds of operations with different targetnames
	
	cmake --build . --target <targetname>

**default build target names are all, clean, depend, rebuild_cache, edit_cache**

### Sets variable called USE_LIBRARY to true, which is defined in the CMakeLists.txt

	cmake -D USE_LIBRARY=TRUE
	
### changes build generator to Visual Studio 2017 x64

	cmake -G "Visual Studio 15 2017 Win64"

or

    cmake -DCMAKE_GENERATOR_PLATFORM=x64
	
### changes compiler to clang++

	cmake -D CMAKE_CXX_COMPILER=clang++
### check current system-information for cmake, which includes things like the currently used compiler and write it to info.txt

	cmake --system-information info.txt
	
### Changes the build type to Debug
	
	cmake -D CMAKE_BUILD_TYPE=Debug ..

### Builds Release

	cmake --build . --config Release
	
### Shows more information regarding the build process

	cmake --build . --VERBOSE=1

<div id="2"></div>

# Basic CMakeLists.txt

### sets the minimum required cmake version to 3.10 and shows an fatal error if it is not the case

	cmake_minimum_required(VERSION 3.10 FATAL ERROR)

### sets the project name to hello-world and the default language to c++

	project(hello-world LANGUAGES CXX)

### adds a target called hello-world and hello-world.cpp as dependency
	
	add_executable(hello-world hello-world.cpp)

<div id="3"></div>

# Building Libraries

## Static or Shared Libraries

**To build a shared/dynamic library, use SHARED as second argument.**</br>
**with OBJECT as library type it is possible to make a static and shared library at the same time**
	
	add_library(message STATIC Message.hpp Message.cpp)

Creates a static library called message from Message.hpp and Message.cpp (libmessage.lib on Windows)

## Object Files

### Creates object files from the source files to be able to create STATIC and SHARED libraries at the same time

	add_library(message-objs OBJECT Message.hpp Message.cpp)


### Gets the target objects from the message-objs (in this case: Message.hpp Message.cpp)

	$<TARGET_OBJECTS:message-objs>

<div id="4"></div>

# Using Libraries

### links against the message library to create the hello-world executable

	target_link_libraries(hello-world message)

### adds include directory to search for (for the compiler)

	target_include_directories(hello-world PUBLIC E:/SDL2/include)

### Sets property  POSITION_INDEPENDENT_CODE to true for the target message-objs

	set_target_properties(message-objs PROPERTIES POSITION_INDEPENDENT_CODE TRUE)

### Sets the output library name of message_shared to message

	set_target_properties(message_shared PROPERTIES OUTPUT_NAME "message")

### Add sources to target outside of add_executable
**Adds source file test.cpp to target < targetname >**

	target_sources(< targetname > PRIVATE test.cpp)

<div id="5"></div>

# Variables

### Create a variable called USE_LIBRARY and set its value to FALSE
	
	set(USE_LIBRARY FALSE)

### Unset a variable

	unset(USE_LIBRARY)

### Set the BUILD_SHARED_LIBS variable, which is defined in CMake to FALSE, add_library without a second parameter now creates a static library instead of a shared one as default

	set(BUILD_SHARED_LIBS FALSE)

### Create a variable list called _sources with the two source files as values

	list(APPEND _sources Message.hpp Message.cpp)

In CMake, lists are stored in this format: Message.hpp;Message.cpp
every entry is devided with a semicolon
lists can also be created with set

### Creates a variable USE_LIBRARY and sets its standard value to FALSE, but the user can change the value from the cmake command (cmake -D USE_LIBRARY=TRUE ..)
	
	option(USE_LIBRARY "Compile sources into a library" FALSE)

<div id="6"></div>

# Print

### Print a message with a string and variable in the string

	message(STATUS "Compile sources into a library? ${USE_LIBRARY}")


### Print variable with CMakePrintHelpers

	include(CMakePrintHelpers)
	cmake_print_variables( < variablename1 > < variablename 2 > ...)

<div id="7"></div>

# Conditions

### If USE_LIBRARY == true, add a library called message with the content of  the _sources list as source files
**THERE HAS TO BE A endif() at the end of each if/else statement

	if(USE_LIBRARY)
		add_library(message $(_sources))

### Else statement

	else()	
		message("this is the else statement")
	
**Has to be called at the end of each if/else statement**
	
	endif()

**Includes the CMakeDependentOption module to be able to use its capabilities**
	
	include(CMakeDependentOption)

### Creates a new option called MAKE_STATIC_LIB which is FALSE if the USE_LIBRARY is TRUE
	
	cmake_dependent_option(MAKE_STATIC_LIB FALSE "USE_LIBRARY" TRUE)

<div id="8"></div>

# Compiler options

### Set compiler

	set(CMAKE_CXX_COMPILER g++)


### Set compile options for specific library or target called < targetname > to the flags specified in the < flaglist > list
	
	target_compile_options(<targetname> PRIVATE ${<flaglist>}

different visibility levels are possible:

 - PRIVATE - only use the flags on the specified target
 - PUBLIC - flags will be applied to given and all other targets consuming it
 - INTERFACE flags will be applied to given targets consuming it

### Set different compiler flags for different compiler
	
	if(CMAKE_CXX_COMPILER_ID MATCHES GNU)
		list(APPEND CMAKE_CXX_FLAGS "-fno-rtti" "-fno-exceptions")
		list(APPEND CMAKE_CXX_FLAGS_DEBUG "-Wsuggest-final-types" "-Wsuggest-final-methods" "-Wsuggest-override")
		list(APPEND CMAKE_CXX_FLAGS_RELEASE "-O3" "-Wno-unused")
	endif()
	if(CMAKE_CXX_COMPILER_ID MATCHES Clang)  
		list(APPEND CMAKE_CXX_FLAGS "-fno-rtti" "-fno-exceptions" "-Qunusedarguments" "-fcolor-diagnostics")  
		list(APPEND CMAKE_CXX_FLAGS_DEBUG "-Wdocumentation")  list(APPEND CMAKE_CXX_FLAGS_RELEASE "-O3" "-Wno-unused") 
	endif()
	
an even better approach is to use own variables for the flags and not the cmake provided ones
for example instead of CMAKE_CXX_FLAGS use CXX_FLAGS or whatever name you want and then use target_compile_options

### Set C++ Language specific options of target. C++ Version to 14, extensions off and standard required in this case if version 14 is required or not

	set_target_properties(< targetname >  PROPERTIES    CXX_STANDARD 14    CXX_EXTENSIONS OFF    CXX_STANDARD_REQUIRED ON    POSITION_INDEPENDENT_CODE 1  )

### Set Compile feature requirement for < targetname >. In this case the constexpr feature of c++ has to be available for the target

	target_compile_features(< targetname > PRIVATE cxx_constexpr)

### Check for cxx compiler flags
**include standard CheckCXXCompilerFlags.cmake module, which is needed for the check_cxx_compiler_flag command**
**If the compiler flag works, _march_native_works will be set to true**

	include (CheckCXXCompilerFlags)
	check_cxx_compiler_flag("-march=native" _march_native_works)

<div id="9"></div>

# Configure Source Files

### Add compiler flag O2 to main.cpp

	set_source_files_properties(main.cpp PROPERTIES COMPILE_FLAGS O2)

all properties that can be modified can be found here: https://cmake.org/cmake/help/v3.5/manual/cmake-properties.7.html#source-file-properties

### Get compiler flags of main.cpp and save it in the variable _flags

	get_source_file_property(_flags main.cpp COMPILE_FLAGS) 

### Sets preprocessor definition BUILD_USING_CMAKE in all files regarding the target ( C/C++: #define BUILD_USING_CMAKE )

	target_compile_definitions(< target > PUBLIC "BUILD_USING_CMAKE")

### Check if compiler flags work with the current buildsystem/compiler

	include(CheckCXXCompilerFlag)
	check_cxx_compiler_flag( < flags > result)

### Configure source code with system information obtained from CMake

with the execute_process and cmake_host_system_information commands, we can obtain information about the system/processor/architecture etc.
This information can be used to manipulate source code.

	#get information
	execute_process( COMMAND whoami TIMEOUT 1 OUTPUT_VARIABLE inf_username OUTPUT_STRIP_TRAILING_WHITESPACE)
	
	cmake_host_system_information(RESULT inf_hostname QUERY HOSTNAME)
	cmake_host_system_information(RESULT inf_fqdn QUERY FQDN)
	cmake_host_system_information(RESULT inf_processor_name QUERY PROCESSOR_NAME)
	cmake_host_system_information(RESULT inf_processor_description QUERY PROCESSOR_DESCRIPTION)
	cmake_host_system_information(RESULT inf_os_name QUERY OS_NAME)
	cmake_host_system_information(RESULT inf_os_release QUERY OS_RELEASE)
	cmake_host_system_information(RESULT inf_os_version QUERY OS_VERSION)
	cmake_host_system_information(RESULT inf_os_platform QUERY OS_PLATFORM)

	#get time in format Year-Month-Day Hour:Minute:Second UTC
	string(TIMESTAMP inf_current_time "%Y-%m-%d %H:%M:%S [UTC]" UTC)
	
	configure_file( sys_info.cpp.in sys_info.cpp @ONLY)

configure_file replaces all strings in sys_info.cpp.in that match the same name as defined CMake variables if they have an @ at the beginning and end and replaces them with the values. After that, the changes are saved in a file called sys_info.cpp.

for example: 
In sys_info.cpp there is a line with the following code: std::cout<<"OS NAME: @inf_os_name@"<<std::endl;
if there is a variable called inf_os_name defined in CMake,  configure_file( sys_info.cpp.in sys_info.cpp @ONLY) will replace @inf_os_name@ with the value of the CMake variable and save it in sys_info_cpp

### Distribute CMake project version to source files

This can be done with the same method as above, we use the configure_file command to replace strings which are in this case preprocessor definitions.

example:

version_pregenerated.h
	
	#deinfe VERSION_MAJOR @PROJECT_VERSION_MAJOR@
	#define VERSION_MINOR @PROJECT_VERSION_MINOR@
	#define VERSION_PATCH @PROJECT_VERSION_PATCH@

CMakeLists.txt

	cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

	project(version_test VERSION 2.1.15 LANGUAGES CXX)

	configure_file(version_pregenerated.h version.h @ONLY)
	
This replaces the 3 defines, because the PROJECT_VERSION_MAJOR, PROJECT_VERSION_MINOR and  PROJECT_VERSION_PATCH are CMake variables that are defined by the CMAKE project command. 

it is possible to ensure non-broken defines with #cmakeinclude instead of #include.
CMake then handles replacing #cmakeinclude < varname > with #include < varname >if the variables have usable values or with #undef < varname > if not.

<div id="10"></div>

# Loops

### Foreach loop
**!TAG! Set propertie of specific source file**
**Use Foreach loop to set compile flags of specific source files. In this case loop through every source file in the list sources_with_lower_optimization and set the compiler flag O2**
	
	foreach(_source IN LISTS sources_with_lower_optimization)  
		set_source_file_properties(${_source} PROPERTIES COMPILE_FLAGS O2)
	endforeach()

the foreach statement can also be written like this:

	foreach(_source ${sources_with_lower_optimization}
	
### Get property of specific source file
**Use foreach loop to get the compile flags of every source file in the list sources_with_lower_optimization**

	foreach(_source ${sources_with_lower_optimization})  
		get_source_file_property(_flags ${_source} COMPILE_FLAGS)  
		message(STATUS "Source ${_source} has the following extra COMPILE_FLAGS: ${_flags}") 		
	endforeach()
get_source_file_property stores the type of property specified in arg3 from arg2 in arg1

### Different ways to create a for loop

	#boiler code
	list(APPEND _files file1.cpp file2.cpp file3.cpp)	

	#1 loop through every file in files
	foreach(file ${files})
	
	#2 3 loops
	foreach(file RANGE 3)
	#2.1 from 1 to 3 in steps incremented by 1
	foreach(file RANGE 1 3 1)
	
	#3 same as #1
	foreach(file IN LISTS files)
	
	#4 I dont understand, read doc if you want to know
	foreach(file IN ITEMS ...)

<div id="11"></div>

# Platform Dependency

### Detect operting system

	#true if Linux
	if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
	endif()
	#true if Windows
	if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
	endif()
	#true if macOS
	if(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
	endif()

### Platform dependend code/operations
**Set Preprocessor definition from CMake**
**Sets preprocessor definition called IS_LINUX in all files regarding the target < targetname >**

	if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
		target_compile_definitions(< targetname > PUBLIC "IS_LINUX")
	endif()
	
visibilities are the same as in target_compile_options

### Detect Compiler
**If the compiler is Intel, set IS_INTEL_CXX_COMPILER as preprocessor definition**

	if(CMAKE_CXX_COMPILER_ID MATCHES Intel)
		target_compile_definitions(< targetname > PUBLIC "IS_INTEL_CXX_COMPILER")
	endif()
	
<div id="12"></div>

# Hardware Dependency

### Detect instruction set length
**Set preprocessor definition  IS_64_BIT_ARCHITECTRUE in the target < targetname > if the instruction set has a length of 8 byte**

	#check the size of a void pointer to determine the instruction length thus 32 or 64bit
	if(CMAKE_SIZEOF_VOID_P EQUAL 8)
		target_compile_definitions(< targetname > PUBLIC "IS_64_BIT_ARCHITECTURE")
	else()
		target_compile_definitions(< targetname > PUBLIC "IS_32_BIT_ARCHITECTURE")
	endif()

### Detect CPU Architecture
### Set preprocessor definition called ARCHITECTURE to the architecture detected by CMake in < targetname >**

	target_compile_definitions( < targetname > PUBLIC "ARCHITECTURE=${CMAKE_HOST_SYSTEM_PROCESSOR}" )

example values cmake uses are "i386", "i686", "x86_64"
	
### Detect additional system/processor information
**cmake_host_system_information fills the RESULT variable called _key with information gotten from a QUERY (key). key has to be a name of a specific information to gather, for example NUMER_OF_PHYSICAL_CORES**
**configure_file changes preprocessor definitions with the same name as CMake used variables to the corresponding value. In this case only, if the preprocessor definitions has a @ before and after the name in the .h file**

	foreach(key IN ITEMS NUMBER_OF_LOGICAL_CORES NUMBER_OF_PHYSICAL_CORES TOTAL_VIRTUAL_MEMORY AVAILABLE_VIRTUAL_MEMORY TOTAL_PHYSICAL_MEMORY AVAILABLE_PHYSICAL_MEMORY IS_64BIT HAS_FPU HAS_MMX HAS_MMX_PLUS HAS_SSE HAS_SSE2 HAS_SSE_FP HAS_SSE_MMX HAS_AMD_3DNOW HAS_AMD_3DNOW_PLUS HAS_IA64 OS_NAME OS_RELEASE OS_VERSION OS_PLATFORM)
		cmake_host_system_information(RESULT _${key} QUERY ${key})
	endforeach()
	
	configure_file(config.h.in config.h @ONLY)

<div id="13"></div>

# Find Libraries

### Find library on the system

**Look for package/library named < libraryname > with the < libraryversion> and set it as requirement of the project. CONFIG disables the module mode and uses CONFIG mode instead**
**After this command, some variables are usually defined, check variables section**

	find_package(< libraryname > < libraryversion > REQUIRED CONFIG)

additional information:

MODULE mode searches a file named Find< libraryname >.cmake and gets all required information about the library. Often the library path is obtained from an environment variable.
CMake finds the Find< libraryname >.cmake file either through CMake's own defined .cmake files for many standard libraries which are located in the CMake/share/cmake/Modules path or through additional paths which can be provided by the CMAKE_MODULE_PATH variable.

Read Find< libraryname >.cmake comment to see, which environment variable has to be set

CONFIG mode searches for a file name < libraryname >Config.cmake additional paths to search in can be provided by the < libraryname >_DIR variable.

### Find package and check for valid variables/paths
**the package will only be considered found if the REQUIRED_VARS and VERSION_VAR is valid**
	
	find_package_handle_standard_args(< libraryname > FOUND_VAR found_vars REQUIRED_VARS path VERSION_VAR version)

### Use IMPORTED type in target_link_libraries to use library defined compiler options/include directories etc.
**Use the OpenMP::OpenMP_CXX library target to use the library defined C++ settings ( has to be available from the lib )**
	find_package(OpenMP REQUIRED)
	target_link_libraries( < targetname > PUBLIC OpenMP::OpenMP_CXX)

### Print target properties
**Print the OpenMP library defined properties such as compile options, include dirs and link libraries**
	
	include(CMakePrintHelpers)	
	cmake_print_properties(TARGETS OpenMP::OpenMP_CXX PROPERTIES INTERFACE_COMPILE_OPTIONS INTERFACE_INCLUDE_DIRECTORIES INTERFACE_LINK_LIBRARIES )

### Use only a COMPONENT from a library
**Find the subcomponent filesystem in boost and link the target against it**

	find_package(Boost 1.54 REQUIRED COMPONENTS filesystem)
	target_link_libraries( < targetname > PUBLIC Boost::filesystem)

### Use pgk-config to find libraries
**Use the PkgConfig package to search for the ZeroMQ library with pkg_search_module**

	find_package(PkgConfig REQUIRED QUIET)
	pkg_search_module(ZeroMQ REQUIRED libzeromq libzmq lib0mq IMPORTED_TARGET)

<div id="14"></div>

# Execute Terminal/Console Commands

### Execute cmd/terminal command
**executes command < command > and saves the result in _rv and the output of the command in _ov**

	execute_process( COMMAND < command > RESULT_VARIABLE _rv OUTPUT_VARIABLE _ov ERROR_QUIET OUTPUT_STRIP_TRAILING_WHITESPACE)
example
	
	execute_process( COMMAND ${PYTHON_EXECUTABLE} "-c" "print('Hello, world!')"  RESULT_VARIABLE _status  OUTPUT_VARIABLE _hello_world  ERROR_QUIET  OUTPUT_STRIP_TRAILING_WHITESPACE )

There are more useful parameters to this command. 
For example:
INPUT_FILE, OUTPUT_FILE used to redirect the standard input/output for the last command

### Custom command to produce an output file(s)

	add_custom_command(OUTPUT < filename > COMMAND < command >)	
	
example:

	add_custom_command(OUTPUT ${BIN_DIR}/readme.md COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_CURRENT_SOURCE_DIR}/readme.md ${BIN_DIR}/readme.md DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/readme.md)

copies readme.md from root dir to bin dir if it has been changed.

**IMPORTANT**: Every time a target uses the output as a dependency, the add_custom_command will be invoked, so dont use the output of this command in multiple targets.

A better approach is to use add_custom_target to invoke add_custom_command and let the targets depend on the add_custom_target created target!

### Custom commands at specific time before build or linkage

	#pre link command
	add_custom_command(TARGET < targetname > PRE_LINK COMMAND < command > VERBATIM)

	#post build command
	add_custom_command(TARGET < targetname > POST_BUILD COMMAND < command > VERBATIM)

<div id="15"></div>

# File operations

### Platform independent file operations
**Create a new target which unpacks a .tar.gz and uses it as dependency for another target** 
	
	add_custom_target(unpack ALL COMMAND ${CMAKE_COMMAND} -E tar xzf ${CMAKE_CURRENT_SOURCE_DIR}/eigen-eigen-5a0156e40feb.tar.gz COMMAND ${CMAKE_COMMAND} -E rename eigen-eigen-5a0156e40feb eigen-3.3.4 WORKING DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} COMMENT "Unpacking Eigen3 in ${CMAKE_CURRENT_BINARY_DIR}/eigen-3.3.4")

	# < targetname > can only be built after the unpack happened
	add_dependency( < targetname > unpack )
	
### Get Python CFFI Lib version with execute_process

	set(_module_name "cffi")
	
	execute_process(COMMAND ${PYTHON_EXECUTABLE} "-c" "import ${_module_name}; print(${_module_name}.__version__)"  OUTPUT_VARIABLE _stdout  ERROR_VARIABLE _stderr  OUTPUT_STRIP_TRAILING_WHITESPACE  ERROR_STRIP_TRAILING_WHITESPACE  


### Use outputs from add_custom_command as source files with add_custom_target

	#set needed source files
	set(MATH_SRCS ...)

	#extract sources from .tar.gz file and mark them as output
	add_custom_command(OUTPUT ${MATH_SRCS} COMMAND ${CMAKE_COMMAND} -E tar xzf ${CMAKE_CURRENT_SOURCE_DIR}/wrap_BLAS_LAPACK.tar.gz WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/wrap_BLAS_LAPACK.tar.gz COMMENT "Unpacking C++ wrappers for BLAS/LAPACK")

	#use the output files from add_custom_command as dependency
	add_custom_target(BLAS_LAPACK_wrappers WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} DEPENDS ${MATH_SRCS} COMMENT "Intermediate BLAS_LAPACK_wrappers target" VERBATIM)
	
another time to execute a command is PRE_BUILD. But this option only works with Visual Studio

<div id="16"></div>

# Try to Compile

### Check if source files can successfully compile

	try_compile(< return var > < path > SOURCES < sources > LINK_LIBRARIES < libs >)

example:

	try_compile(omp_taskloop_test_1 ${_scratch_dir} SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/taskloop.cpp LINK_LIBRARIES OpenMP::OpenMP_CXX  

This can also be done with the command check_< lang >_source_compiles
	
	include(CheckCXXSourceCompiles)

	file(READ < source > source_in_var)
	check_cxx_source_compiles(${source_in_var} resultvar)#

check_< lang >_source_compiles is the "high level approach" to try_compile.
It uses the CMake variables CMAKE_REQUIRED_FLAGS, CMAKE_REQUIRED_DEFINITIONS, CMAKE_REQUIRED_INCLUDES and CMAKE_REQUIRED_LIBRARIES as settings rather than obtaining this information from the arguments like try_compile does. Also check_< lang >_source_compiles requires the source code to be in a variable.

It is possible to debug the try_compile command if you run cmake with the --debug-trycompile flag

<div id="17"></div>

# Functions and Macros

### CMake functions, macros

Like in a programming language, you can create functions and macros in CMake.

	macro( < macroname > < parameter1 > < parameter2 > ...)
		message(STATUS "macro called with ${ARGC} parameter: ${ARGV}")
	endmacro()
same for functions:

	function( < functionname > < parameter1 > < parameter2 > ...)
		message(STATUS "function called with ${ARGC} parameter: ${ARGV}")
	endmacro()

the difference between functions and macros in CMake is, that a macro can use all variables defined outside of the macro and a function can not.

However, you can define variables in functions, that have a global scope using PARENT_SCOPE as additional argument.
example:

	set( testvar TRUE PARENT_SCOPE )

### Parse specific function arguments

It is possible to create a funciton with no defined arguments, but parse specific parameters inside the function with cmake_parce_arguments

	function(add_catch_test) 
	set(options) 
	set(oneValueArgs NAME COST) 
	set(multiValueArgs LABELS DEPENDS REFERENCE_FILES)  
	cmake_parse_arguments(add_catch_test "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN} )

### Deprecating functions

It is useful to create a custom function for this

	function(deprecate_variable _variable _access)
		if(_access STREQUAL "READ_ACCESS")
			message(DEPRECATION "variable ${_variable} is deprecated")
		endif() 
	endfunction()

Every variable that is given as an argument to this function and has the READ_ACCESS, generates a DEPRECATION message.

Then redefine the function and call variable_watch, which prints every change on a variable or calls a function if a function is also given as the second parameter.


	if( CMAKE_VERSION VERSION_GREATER "3.9" )
		macro(custom_include_guard)
			message(DEPRECATION "custom_include_guard is deprecated - use built-in include_guard instead"
			#function to deprecate
			_custom_include_guard(${ARGV})
		endmacro()
		
		variable_watch(included_modules deprecate_variable)
	endif()
This if statement defines a function called custom_include_guard and generates a DEPRECATION message. It also calls the _custom_include_guard function, which is to be deprecated for CMake 3.10 or higher. Finally the variable_watch(included_modules deprecate_variable) command calls the function deprecate_variable with the included_modules variable as an argument, every time included_modules is modified.

<div id="18"></div>

# CMake Modules

### Working with CMake modules ( .cmake files)

If you have multiple cmake files which work together, you can include them with the include command
for example:

	list( APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake}
	#includes colors.cmake from the cmake subfolder
	include(colors)

### CMake include guard to prevent multiple includes for a module

since CMake 3.10 there is a simple command for it.

	_include_guard(GLOBAL)

The parameter GLOBAL ensures a global include guard.

before then, it had to be done manually by setting a variable to its file and checking if the variable is set to its file already.
here is an example of a include_guard macro, that works with version 3.10 and lower

	macro(include_guard)
		if(CMAKE_VERSION VERSION_LESS 3.10)
			message(STATUS "calling our custom include_guard")
			if(NOT  DEFINED included_modules)
				set(included_modules)
			endif()
			if ("${CMAKE_CURRENT_LIST_FILE}"  IN_LIST included_modules)
				message(WARNING "module ${CMAKE_CURRENT_LIST_FILE} processed more than once")
			endif()
			list(APPEND included_modules ${CMAKE_CURRENT_LIST_FILE})
		else()
			message(STATUS "calling the built-in include_guard")
			_include_guard(${ARGV})
		endif()
	endmacro()

<div id="19"></div>

# Multiple CMakeLists.txt files

### Using multiple CMakeLists.txt in different subdirectories

The command add_subdirectory( < subdirectory > ) processes the CMakeLists.txt in the given < subdirectory >. Every target and source file that is defined in that CMakeLists.txt is now also defined in the CMakeLists.txt, that called the add_subdirectory command.

example

src/CMakeLists.txt

	add_library(evolution "")
	target_sorces(evolution PRIVATE evolution.cpp PUBLIC evolution.h)
	#needed, so the other CMakeLists.txt also has this include directory
	target_include_directories(evolution PUBLIC ${CMAKE_CURRENT_LIST_DIR})

./CMakeLists.txt

	add_subdirectory(evolution)
	add_executable(test test.cpp)
	target_link_libraries(test evolution)
**Prevent in source build ( force build folder )

	if(${PROJECT_SOURCE_DIR} STREQUAL ${PROJECT_BINARY_DIR})
		message(FATAL_ERROR "In-source builds are not allowed. Please make a build directory and run CMake from there")
	endif()

### Configure target sources/include_directories/link_libraries from another CMakeLists.txt

When another CMakeLists.txt is used via add_subdirectory, it is not possible to configure the target_sources for a target created in the added CMakeLists.txt. This can be done with include(src/CMakeLists.txt) instead of add_subdirectory(src
)

<div id="20"></div>

# Installing

### CMake Installer

The install command handles the installation of files in specified locations.

	install(
		TARGETS

		message-shared

		hello-world_wDSO

		ARCHIVE DESTINATION ${INSTALL_LIBDIR} COMPONENT lib

		RUNTIME DESTINATION ${INSTALL_BINDIR} COMPONENT bin

		LIBRARY DESTINATION ${INSTALL_LIBDIR} COMPONENT lib
		
		PUBLIC_HEADER DESTINATION ${INSTALL_INCLUDEDIR}/message COMPONENT dev
	)
	
installs the target message-shared and hello-world-wDSO. Static lib files to ${INSTALL_LIBDIR}, binary files to ${INSTALL_BINDIR}, Shared lib files to ${INSTALL_LIBDIR} and public header files to ${INSTALL_INCLUDEDIR}/message.

For libraries some properties should be set before installing.

	set_target_properties(message-shared

	PROPERTIES

	POSITION_INDEPENDENT_CODE 1

	SOVERSION ${PROJECT_VERSION_MAJOR}

	OUTPUT_NAME "message"

	DEBUG_POSTFIX "_d"

	PUBLIC_HEADER "Message.hpp"

	MACOSX_RPATH ON

	WINDOWS_EXPORT_ALL_SYMBOLS ON

	)

Sets properties for the message-shared target, which is a library that is installed.
Sets the api version ( SOVERSION ) to the same version as the project version.
OUTPUT_NAME "messsage" is the name the library will have. In this case it will be libmessage.dll or libmessage.so . DEBUG_POSTFIX "_d" generates a postfix on the librarynames, when the debug mode is used instead of release. This will generate libmessage_d.so/dll. PUBLIC_HEADER "Message.hpp" says, that Message.hpp will be in the public list for MacOSX builds ( other platforms dont need this ). MACOSX_RPATH ON specifies, that a relative path for the shared libs will be used on macosx. WINDOWS_EXPORT_ALL_SYMBOLS 

For executables, some properties should be set likewise.

	set_target_properties(hello-world_wDSO

	PROPERTIES

	MACOSX_RPATH ON

	SKIP_BUILD_RPATH OFF

	BUILD_WITH_INSTALL_RPATH OFF

	INSTALL_RPATH "${message_RPATH}"

	INSTALL_RPATH_USE_LINK_PATH ON

	)

- MACOSX_RPATH ON - activate usage of rpath to look for shared libs
- SKIP_BUILD_RPATH OFF - disables skipping building the RPATH
- BUILD_WITH_INSTALL_RPATH OFF - disables setting the RPATH on the executable only to the install path
- INSTALL_RPATH "${message_RPATH}" sets the rpath to message_RPATH
- INSTALL_RPATH_USE_LINK_PATH ON appends linker search path with RPATH

the install command has many more optional arguments, which can be found on the official CMake documentation

<div id="21"></div>

# Hiding Symbols

### Hide shared library symbols

	#hide all symbols by default
	set_target_properties( message-shared PROPERTIES CXX_VISIBILITY_PRESET hidden VISIBILITY_INLINES_HIDDEN 1)

	include(GenerateExportHeader)
	generate_export_header( message-shared BASE_NAME "message" EXPORT_MACRO_NAME "message_EXPORT" EXPORT_FILE_NAME ""{CMAKE_BINARY_DIR}/${INSTALL_INCLUDEDIR}/message_export.h" DEPRECATED_MACRO_NAME "message_DEPRECATED" NO_EXPORT_MACRO_NAME "message_NO_EXPORT" STATIC_DEFINE "message_STATIC_DEFINE" NO_DEPRECATED_MACRO_NAME "message_NO_DEPRECATED" DEFINE_NO_DEPRECATED)
	
	target_include_directories(message-shared PUBLIC ${CMAKE_BINARY_DIR}/${INSTALL_INCLUDEDIR}  )

 - BASE_NAME: This sets the base name of the generated header and macros to the passed value.
 - EXPORT_MACRO_NAME: This sets the name of the export macro.
 - EXPORT_FILE_NAME: This sets the name for the export header file generated.
 - DEPRECATED_MACRO_NAME: This sets the name for the deprecation macro. This is used to mark deprecated code, the compiler will emit a deprecation warning if clients use it.
 - NO_EXPORT_MACRO_NAME: This sets the name of the no-export macro. 
 - STATIC_DEFINE: This is for the name of the macro to use when also compiling a static library out of the same sources.
 - NO_DEPRECATED_MACRO_NAME: This sets the name of the macro to be used to exclude deprecated code from being compiled.
 - DEFINE_NO_DEPRECATED: This instructs CMake to generate preprocessor code to exclude deprecated code from compilation.

The command generate_export_header generates an export header (.h) which defines the macros used to define which symbols are exposed and which are not.

In the library header, every class/function, that has to be exposed as a symbol, has to be defined using the macros defined in that export header.

example:

message.h

	#include "message_export.h"

	class message_EXPORT message
	{
		...
	}
Now, the message class is defines as message_EXPORT and its symbol is exposed.

### Exporting target (Creating < library >Config.cmake )

This can be done by using the install command with the EXPORT argument

	install( TARGETS message-shared message-static hello-world_wDSO hello-world_wAR EXPORT messageTargets ARCHIVE DESTINATION ${INSTALL_LIBDIR} COMPONENT lib RUNTIME DESTINATION ${INSTALL_BINDIR} LIBRARY DESTINATION ${INSTALL_LIBDIR} COMPONENT lib PUBLIC_HEADER DESTINATION ${INSTALL_INCLUDEDIR}/message COMPONENT dev  )

This creates a messageTargets.cmake file. To install it in the INSTALL_CMAKEDIR direction we use install again

	install( EXPORT messageTargets NAMESPACE "message::" DESTINATION ${INSTALL_CMAKEDIR} COMPONENT dev)

NAMESPACE "message::" is just used to prevent name clashing.
Now we create the CMake configuration files.

	include(CMakePackageConfigHelpers)
	write_basic_package_version_file( ${CMAKE_CURRENT_BINARY_DIR}/messageConfigVersion.cmake VERSIN ${PROJECT_VERSION} COMPABILITY SameMajorVersion)

	configure_package_config_file( ${PROJECT_SOURCE_DIR}/cmake/messageConfig.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/messageConfig.cmake INSTALL_DESTINATION ${INSTALL_CMAKEDIR} )

This requires a messageConfig.cmake.in file

Now we install the 2 generated files in INSTALL_CMAKEDIR

	install( FILES ${CMAKE_CURRENT_BINARY_DIR}/messageConfig.cmake ${CMAKE_CURRENT_BINARY_DIR}/messageConfigVersion.cmake DESTINATION ${INSTALL_CMAKEDIR})

messageConfig.cmake.in
	
	#will be replaced by configure_package_config_file
	@PACKAGE_INIT@
	
	#include the previous created messageTargets.cmake
	include("${CMAKE_CURRENT_LIST_DIR}/messageTargets.cmake")

	check_required_components( "message-static" "message-shared")


more details:
https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-10/recipe-03

<div id="22"></div>

# Cross compiling

### Compiling a Windows executable on Linux

Cross compiling is pretty simple. 

We need a compiler, that can compile for the target os/platform and a toolchain file to tell CMake which OS is the target and which compiler to use.

for example:

**main.cpp**

    cmake_minimum_required(VERSION 3.5 FATAL_ERROR_)
    project(hello-world LANGUAGES CXX)


    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_EXTENSIONS OFF)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)


    add_executable(hello_world main.cpp_)
    
    #install hello_world.exe in the CMAKE_INSTALL_BINDIR
    install(TARGETS hello_world DESTINATION ${CMAKE_INSTALL_BINDIR})

**toolchain.cmake**

    #set os target
    set(CMAKE_SYSTEM_NAME Windows)

    #set used compiler ( mingw-g++ for windows targets )
    set(CMAKE_CXX_COMPILER i686-w64-mingw32-g++)

    #use CMAKE_FIND_ROOT_PATH_MODE to search for includes in CMAKE_FIND_ROOT_PATH
    set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

    set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)

    set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)


CMAKE_FIND_ROOT_PATH is useful for cross-compiling to set the include/lib search root to the os environment path, which contains all needed libs/source files needed for the compilation for the target system.

**terminal**

    cd build
    #configure
    cmake -D CMAKE_TOOLCHAIN_FILE=cmake/toolchain.cmake
    #build
    cmake --build .

for more information about CMake cross-compiling see: https:/​/​cmake.​org/​cmake/​help/​latest/​manual/​cmaketoolchains.​7.​html

<div id="23"></div>

# Packaging Libraries using CPack

To pack a library into src and bin packets (zip/tar.gz etc.) CPack can be used.
Therefore a CMakeCPack.cmake file is needed.

example:

	set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")

	set(CPACK_PACKAGE_VENDOR "CMake Cookbook")

	set(CPACK_PACKAGE_DESCRIPTION_FILE "${PROJECT_SOURCE_DIR}/INSTALL.md")

	set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "message: a small messaging library")

	set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE")

	set(CPACK_PACKAGING_INSTALL_PREFIX "/opt/${PROJECT_NAME}")

	set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")

	set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")

	set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

	set(CPACK_SOURCE_IGNORE_FILES "${PROJECT_BINARY_DIR};/.git/;.gitignore;menu.yml")

	set(CPACK_SOURCE_GENERATOR "ZIP;TGZ")

	set(CPACK_GENERATOR "ZIP;TGZ")

	if(UNIX)

		if(CMAKE_SYSTEM_NAME MATCHES Linux)

			list(APPEND CPACK_GENERATOR "DEB")

			set(CPACK_DEBIAN_PACKAGE_MAINTAINER "robertodr")

			set(CPACK_DEBIAN_PACKAGE_SECTION "devel")

			set(CPACK_DEBIAN_PACKAGE_DEPENDS "uuid-dev")

			list(APPEND CPACK_GENERATOR "RPM")

			set(CPACK_RPM_PACKAGE_RELEASE "1")
			
			set(CPACK_RPM_PACKAGE_LICENSE "MIT")

			set(CPACK_RPM_PACKAGE_REQUIRES "uuid-devel")

		endif()

	endif()

	if(WIN32 OR MINGW)

		list(APPEND CPACK_GENERATOR "NSIS")

		set(CPACK_NSIS_PACKAGE_NAME "message")

		set(CPACK_NSIS_CONTACT "robertdr")

		set(CPACK_NSIS_ENABLE_UNINSTALL_BEFORE_INSTALL ON)

	endif()

	if(APPLE)
	
		list(APPEND CPACK_GENERATOR "Bundle")

		set(CPACK_BUNDLE_NAME "message")

		configure_file(${PROJECT_SOURCE_DIR}/cmake/Info.plist.in Info.plist @ONLY)

		set(CPACK_BUNDLE_PLIST ${CMAKE_CURRENT_BINARY_DIR}/Info.plist)

		set(CPACK_BUNDLE_ICON ${PROJECT_SOURCE_DIR}/cmake/coffee.icns)

	endif()

	message(STATUS "CPack generators: ${CPACK_GENERATOR}")

	include(CPack)

Instead of listing the CPack configuration settings in CMakeCPack.cmake as we have done for simplicity, we could have placed the per-generator settings of CPACK_* variables in a separate file, such as CMakeCPackOptions.cmake, and included these settings into CMakeCPack.cmake using set(CPACK_PROJECT_CONFIG_FILE "${PROJECT_SOURCE_DIR}/CMakeCPackOptions.cmake").  This file can also be configured at CMake time and then included at CPack time, providing a clean way to configure multi-format package generators.

resources:

https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-11/recipe-01

CMake Cookbook page 466+

<div id="24"></div>

# Test Software

**CTest checks, if the return value of a program is zero or not. Zero = successful test Non-Zero = unsuccessful test**

**To enable testing in CMake, call enable_testing()**
**$<TARGET_FILE::<targetname>> is equal to the executable file for example test.exe**

	enable_testing()
	add_test(NAME < targetname > COMMAND $<TARGET_FILE::<targetname>>)
example:

	add_test(NAME test COMMAND $<TARGET_FILE::test>)

the name of the test can contain / as a character useful for organization**

After that, instead of using cmake in the console/terminal, use ctest

The file Testing/Temporary/LastTestsFailed.log shows additional information on failed tests
ctest -output-on-failure prints everything the test program produces on a failure
ctest -v and ctest -vv is used for verbose and even more verbose output (-vv)


**Set test properties**
**In this case 3 environmental variables are set**
**could not be robust on all platforms**

	set_tests_properties( < targetname > PROPERTIES ENVIRONMENT ACCOUNT_MODULE_PATH=${CMAKE_CURRENT_SOURCE_DIR} ACCOUNT_HEADER_FILE=${CMAKE_CURRENT_SOURCE_DIR}/account/account.h ACCOUNT_LIBRARY_FILE=$<TARGET_FILE:account>  

**Set environmental variables guaranteed before the execution**
**Same as above**
	
	add_test(NAME python_test COMMAND ${CMAKE_COMMAND} -E env ACCOUNT_MODULE_PATH=${CMAKE_CURRENT_SOURCE_DIR} ACCOUNT_HEADER_FILE=${CMAKE_CURRENT_SOURCE_DIR}/account/account.h ACCOUNT_LIBRARY_FILE=$<TARGET_FILE:account> ...)

**Run test with CMake command instead of the CTest command**

	#same as ctest
	cmake --build . --target test

	#use this on a visual studio compiler ( or rather just use ctest )
	cmake --build . --target RUN_TESTS
	
**Use Boost::Test with CMake/CTest**

Learn Boost::Test first!
But just for a short demo:
In the test.cpp add:

	#include "sum_integers.h"
	#include <vector>
	#define BOOST_TEST_MODULE example_test_suite
	
	BOOST_AUTO_TEST_CASE(add_example)
	{
		auto integers = {1,2,3,4,5};
		auto result = sum_integers(integers);
		BOOST_REQUIRE(result == 15);
	}
	
CMakeLists.txt

	find_package(Boost 1.54 REQUIRED COMPONENTS unit_test_framework)
	target_link_libraries( < targetname > PRIVATE Boost::unit_test_framework)
	#avoid undefined reference to main
	target_compile_definitions( < targetname > PRIVATE BOOST_TEST_DYN_LINK)

	enable_testing()
	add_test( NAME boost_test COMMAND $<TARGET_FILE::< targetname >>)

**Detect memory leakage/defects with CMake/CTest and Valgrind**

	#find valgrind and set the command for the memorycheck to valgrind's executable path
	find_program(MEMORYCHECK_COMMAND NAMES valgrind)
	#set valgrind options
	set(MEMORYCHECK_COMMAND_OPTIONS "--trace-children=yes --lead-check=full")
	
	#needed to use the memcheck command later
	include(CTest)

	enable_testing()
	add_test(NAME test COMMAND $<TARGET_FILE:< targetname >>)
	
now, with ctest -T memcheck, you can check for memory defects

**Testing expected failures**

Sometimes, you want to replicate a bug or a problem to identify it or for whatever reason it may be.
In this case the test would be successful, when the return code is not zero. For these cases, you can set the WILL_FAIL property of a test to true.

	set_tests_properties( < testname > PROPERTIES WILL_FAIL true)

there are many more properties to test  a failed test. check the official documentation.

**Set maximum time (TIMEOUT) for a test**

Sometimes tests can take a long time or actually unlimited amount time ( stuck mutex )
In such cases, it can be useful to set a maximum amount of time a test can take.

	#timeout time in sec
	set_test_properties(< testname > PROPERTIES TIMEOUT 10)

**Running multiple tests at the same time**

If there are multiple tests in one test suite, it is useful to run these parallel to save time.
This can be done with the -parallel < num > command of ctest
example:

	#runs 4 tests at a time
	ctest -parallel 4

The number of parallel tested tests should not exceed the number of cores in the system
There is a environment variable called CTEST_PARALLEL_LEVEL which, when defined, will be the used number of parallel tests in ctest

**Setting cost ( expected time ) for a test to reduce parallel test time**

	set_tests_properties( fast_test PROPERTIES COST 0.1)
	set_tests_properties( long_test_1 long_test_2 PROPERTIES COST 20)

tests with a higher cost will have a higher priority, thus will be executed first to reduce the total testing time

**Run only specific tests of a test suite**

	set_test_properties( long_test1 long_test2 long_test3 PROPERTIES LABELS "long")
	set_test_properties( short_test1 short_test2 PROPERTIES LABELS "short")

Running tests by their name:

	ctest -R long_test
only tests the tests which have long_test in their name

Running tests by their labels:

	ctest -L short

Running tests by their numbers:

	ctest -I 2,4
	
only runs test 2-4

**Making Test fixtures with CTest/CMake**

	add_test(NAME setup1 ...)
	add_test(NAME setup2 ...)
	add_test(NAME test1 ...)
	add_test(NAME test2 ...)
	add_test(NAME cleanup ...)
	
	set_tests_properties(setup1 setup2 PROPERTIES FIXTURE_SETUP fixture)
	set_tests_properties(test1 test2 PROPERTIES FIXTURE_REQUIRED fixture)
	set_tests_properties(cleanup PROPERTIES FIXTURE_CLEANUP fixture)
	
this ensures, that test1 and test2 cannot be executed without the setup and cleanup

<div id="25"></div>

# Relevant Variables

**Top level source directory for the current project**

    PROJECT_SOURCE_DIR

**!READ! This is true, if the compiler language specified in < LANG > is currently used**
	
	CMAKE_< LANG >_COMPILER_LOADED

**!READ! Compiler id string**
	
	CMAKE_< LANG >_COMPILER_ID
	
**!READ! Is TRUE, if the compiler for the < LANG > is part of GCC**
	
	CMAKE_COMPILER_IS_GNU< LANG >

**!READ! Compiler version**
	
	CMAKE_< LANG >_COMPILER_VERSION

**!READ! Operating System name**
	
	CMAKE_SYSTEM_NAME
	
**!READ! Host System processor architecture**

	CMAKE_HOST_SYSTEM_PROCESSOR
	
**!READ! Target System processor architecture**

	CMAKE_SYSTEM_PROCESSOR

**!READ! Find module variables**
**These variables are provided by python and are called different for every library ( not only the python at the start )**
**They are initialized after find_package(PythonInterpr REQUIRED) was called**

	#python was found
	PYTHONINTERP_FOUND
	#path for the executable
	PYTHON_EXECUTABLE
	#full version string
	PYTHON_VERSION_STRING
	#major version
	PYTHON_VERSION_MAJOR
	#minor version
	PYTHON_VERSION_MINOR
	#patch number
	PYTHON_VERSION_PATCH

**Build TYPE**
**usable values: Release, Debug, RelWithDebInfo, MinSizeRel**
	
	CMAKE_BUILD_TYPE
	
**Used flags for the different build types (C++)**
	
	CMAKE_CXX_FLAGS_DEBUG
	CMAKE_CXX_FLAGS_RELEASE
	CMAKE_CXX_FLAGS_RELWITHDEBINFO
	CMAKE_CXX_FLAGS_MINSIZEREL
	
**Specifies what build types will be available/used with the current build generator, compiler, etc.**
	
	CMAKE_CONFIGURATION_TYPES

**Whether all symbols are exported on Windows or not (Used in Visual C++)**

	CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS
	
**Used to specify the used C++ standard etc.**

	CMAKE_CXX_STANDARD
	CMAKE_CXX_EXTENSIONS
	CMAKE_CXX_STANDARD_REQUIRED

**From CMake Doc: [Semicolon-separated list](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#cmake-language-lists) of directories specifying installation _prefixes_ to be searched by the [`find_package()`](https://cmake.org/cmake/help/latest/command/find_package.html#command:find_package "find_package"), [`find_program()`](https://cmake.org/cmake/help/latest/command/find_program.html#command:find_program "find_program"), [`find_library()`](https://cmake.org/cmake/help/latest/command/find_library.html#command:find_library "find_library"), [`find_file()`](https://cmake.org/cmake/help/latest/command/find_file.html#command:find_file "find_file"), and [`find_path()`](https://cmake.org/cmake/help/latest/command/find_path.html#command:find_path "find_path") commands. Each command will add appropriate subdirectories (like `bin`, `lib`, or `include`) as specified in its own documentation.**

	CMAKE_PREFIX_PATH

**!ENV! Used to define how many parallel tests will be run by CTest**
	
	CTEST_PARALLEL_LEVEL


**Add current directory to the INTERFACE_INCLUDE_DIRECTORIES property for all targets**

	CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE

<div id="26"></div>

# Useful Examples and Additional Info

**Paths**
Only use forward slashes / for paths, CMake handles all OS-specific paths

# Write own Find< libraryname >.cmake file 
**Find< libname >.cmake file to handle ZeroMQ library**

	if(NOT ZeroMQ_ROOT)

		set(ZeroMQ_ROOT "$ENV{ZeroMQ_ROOT}")

	endif()

	if(NOT ZeroMQ_ROOT)

		find_path(_ZeroMQ_ROOT NAMES include/zmq.h)

	else()

		set(_ZeroMQ_ROOT "${ZeroMQ_ROOT}")

	endif()

	find_path(ZeroMQ_INCLUDE_DIRS NAMES zmq.h HINTS ${_ZeroMQ_ROOT}/include)

	if(ZeroMQ_INCLUDE_DIRS)

		set(_ZeroMQ_H ${ZeroMQ_INCLUDE_DIRS}/zmq.h)

	function(_zmqver_EXTRACT _ZeroMQ_VER_COMPONENT _ZeroMQ_VER_OUTPUT)

		set(CMAKE_MATCH_1 "0")

		set(_ZeroMQ_expr "^[ \\t]*#define[ \\t]+${_ZeroMQ_VER_COMPONENT}[ \\t]+([0-9]+)$")

		file(STRINGS "${_ZeroMQ_H}" _ZeroMQ_ver REGEX "${_ZeroMQ_expr}")

		string(REGEX MATCH "${_ZeroMQ_expr}" ZeroMQ_ver "${_ZeroMQ_ver}")

		set(${_ZeroMQ_VER_OUTPUT} "${CMAKE_MATCH_1}" PARENT_SCOPE)

	endfunction()

	_zmqver_EXTRACT("ZMQ_VERSION_MAJOR" ZeroMQ_VERSION_MAJOR)

	_zmqver_EXTRACT("ZMQ_VERSION_MINOR" ZeroMQ_VERSION_MINOR)

	_zmqver_EXTRACT("ZMQ_VERSION_PATCH" ZeroMQ_VERSION_PATCH)

	#We should provide version to find_package_handle_standard_args in the same format as it was requested,

	#otherwise it can't check whether version matches exactly.


	if(ZeroMQ_FIND_VERSION_COUNT GREATER 2)

		set(ZeroMQ_VERSION "${ZeroMQ_VERSION_MAJOR}.${ZeroMQ_VERSION_MINOR}.${ZeroMQ_VERSION_PATCH}")

		else()

			#User has requested ZeroMQ version without patch part => user is not interested in specific patch =>

			#any patch should be an exact match.

			set(ZeroMQ_VERSION "${ZeroMQ_VERSION_MAJOR}.${ZeroMQ_VERSION_MINOR}")

		endif()

		if(NOT ${CMAKE_C_PLATFORM_ID} STREQUAL "Windows")

			find_library(ZeroMQ_LIBRARIES

			NAMES

			zmq

			HINTS

			${_ZeroMQ_ROOT}/lib

			${_ZeroMQ_ROOT}/lib/x86_64-linux-gnu

			)

		else()

			find_library(ZeroMQ_LIBRARIES

			NAMES

			libzmq

			"libzmq-mt-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"

			"libzmq-${CMAKE_VS_PLATFORM_TOOLSET}-mt-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"

			libzmq_d

			"libzmq-mt-gd-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"

			"libzmq-${CMAKE_VS_PLATFORM_TOOLSET}-mt-gd-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"

			HINTS

			${_ZeroMQ_ROOT}/lib

			)

		endif()

	endif()

	include(FindPackageHandleStandardArgs)

	find_package_handle_standard_args(ZeroMQ

	FOUND_VAR

	ZeroMQ_FOUND

	REQUIRED_VARS

	ZeroMQ_INCLUDE_DIRS

	ZeroMQ_LIBRARIES

	VERSION_VAR

	ZeroMQ_VERSION

	)

# Visualizing dependencies with Graphviz

by calling 
	cmake --graphviz=visualized_dep.dot .
	dot -T png visualized_dep.dot -o visualized_dep.png

Graphviz creates a png picture with a visualized dependency diagram

### Use Sanitizer for error detection

	#check santizer flags
	set(ASAN_FLAGS "-fsanitize=address -fno-omit-frame-pointer")
	set(CMAKE_REQUIRED_FLAGS ${ASAN_FLAGS})
	
	#warning, ASAN_FLAGS is a string of flags, it can be used with check_cxx_compiler_flags but not with commands that use lists as parameter like target_link_libraries or target_compile_options to fix this, replace the spaces in the string with semicolons

	#check if the sainizer with its flags is supported by the current compiler
	check_cxx_compiler_flag(${ASAN_FLAGS} asan_works)
	
	#replace spaces with semicolons and save it in a list
	string(REPLACE " " ";" list_asan_flags ${ASAN_FLAGS})
	
### Check if a executable can be run on the system

	#get lib PkgConfig::UUID etc... could be anything else

	#include(CheckCXXSourceRuns)

	set(_test_program " #include <uuid/uuid.h> int main(int argc, char * argv[]) { uuid_t uuid; uuid_generate(uuid); return 0; }" )
	set(CMAKE_REQUIRED_LIBRARIES PkgConfig::UUID)
	
	#check if it runs and return true or false in does_run
	check_cxx_source_runs("${test_program)" does_run)

	unset(CMAKE_REQUIRED_LIBRARIES)

check_cxx_source_runs also needs the CMAKE variables to be set just like check_cxx_source_compiles

### Use Generator expressions to configure at generation time

	find_package(MPI REQUIRED)
	
	target_link_libraries( < targetname > PUBLIC $<$<BOOL:${MPI_FOUND}>:MPI::MPI_CXX)

explaining: a generator expression can have the one of the following 3 structures **$< condition : input >, $< information : output >, $< operation : input >** . Above is a generator expression in a generator expression it says get MPI::MPI_CXX flags if the bool MPI_FOUND is true otherwise get nothing.

this is the same as this:

	if(MPI_FOUND)  
		target_link_libraries(< targetname > PUBLIC MPI::MPI_CXX)
	endif()    
	
### Read Version from VERSION file

Sometimes its better to document the current version in a version file, which can be read from all other programs/libs/sources etc. that need it.

VERSION (file)

	5.5.81

CMakeLists.txt

	CMake setup...
	if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/VERSION")
		file(READ "$CMAKE_CURRENT_SOURCE_DIR}/VERSION" inf_version)
		string(STRIP "${inf_version} string_inf_version)
	endif()

now the version is read from the VERSION file and can be used for reference or to insert it in source files like shown above. 

### Get git hash from git project (at configure time)

	CMake setup...
	set(GIT_HASH "unknown")
	find_package(Git QUIET)
	if(GIT_FOUND)
		execure_process(COMMAND ${GIT_EXECUTABLE} log -1 --pretty=format:%h OUTPUT_VARIABLE GIT_HASH OUTPUT_STRIP_TRAILING_WHITESPACE ERROR_QUIET WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
	endif()
	message(STATUS "Git hash is ${GIT_HASH}")
	
### Get git hash string at build time
For this to work, it should only perform the execute_process if a build process has started. In this example we use a seperate .cmake file to obtain the git hash and execute the seperate .cmake file from the main CMakeLists.txt every time it builds.

git-hash.cmake

	# in case Git is not available, we default to "unknown" set(GIT_HASH "unknown")
	#find Git and if available 
	set GIT_HASH variable 
	find_package(Git QUIET) 
	if(GIT_FOUND)  
		execute_process(COMMAND ${GIT_EXECUTABLE} log -1 --pretty=format:%h OUTPUT_VARIABLE GIT_HASH OUTPUT_STRIP_TRAILING_WHITESPACE ERROR_QUIET) 
	endif()
	message(STATUS "Git hash is ${GIT_HASH}")
	#generate file version.hpp based on version.hpp.in 
	configure_file(${CMAKE_CURRENT_LIST_DIR}/version.hpp.in ${TARGET_DIR}/generated/version.hpp @ONLY)

CMakeLists.txt
	
	cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
	
	project(recipe-07 LANGUAGES CXX)

	set(CMAKE_CXX_STANDARD 11) 
	set(CMAKE_CXX_EXTENSIONS OFF) 
	set(CMAKE_CXX_STANDARD_REQUIRED ON
	
	add_executable(example example.cpp)	
	
	target_include_directories(example PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/generated)
	add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/generated/version.hpp ALL COMMAND ${CMAKE_COMMAND} -D TARGET_DIR=${CMAKE_CURRENT_BINARY_DIR} -P ${CMAKE_CURRENT_SOURCE_DIR}/git-hash.cmake WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} )

	add_custom_target(get_git_hash ALL DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/generated/version.hpp)

	add_dependencies(example get_git_hash)

### Superbuild: Download and install library if not found

This is a complex process and different for every library!
Some examples are provided in the CMake Cookbook ebook.

The C++ Boost example can be found here: https://github.com/dev-cafe/cmake-cookbook/tree/master/chapter-08/recipe-02/cxx-example

The 2 commands/modules needed for superbuilds are ExternalProject and FetchContent

### Mixing C++ and Python

Mixing C++ and Python is achieved via Cython or Boost::Python

Cython example: https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-09/recipe-03
Boost::Python example: https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-09/recipe-04

### Installing a superbuild

https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-10/recipe-04

### Porting a project to CMake

CMake Cookbook Chapter 15 ( p. 567+ )

### set Include directories/Compile definitions etc. only after installation or build

	target_include_directories( < targetname > PUBLIC $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}/${INSTALL_INCLUDEDIR}> $<INSTALL_INTERFACE:${INSTALL_INCLUDEDIR}>)

includes directory  CMAKE_BINARY_DIR}/INSTALL_INCLUDEDIR to target if the target is used and INSTALL_INCLUDEDIR only if the target is used as an exported target within another build tree.

### Building Documentation with Cmake and Doxygen

prerequisite for this is source code suitable for doxygen.

example:
https://github.com/dev-cafe/cmake-cookbook/tree/v1.0/chapter-12/recipe-01