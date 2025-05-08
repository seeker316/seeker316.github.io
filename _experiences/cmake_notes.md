---
layout: post
header-style: text
title: cmake notes
img: img/experience/cmake_notes.png
---

# CMAKE TUTS
Make basically execute commands that you give to it on linux
Cmake is a build automation tool, and it is platform independent

cmake relies on a top level file CMakelists.txt
To run cmake u have to tell it where the source and the build folder is
```shell
  cmake [options] <path-to-source>
  cmake [options] <path-to-existing-build>
  cmake [options] -S <path-to-source> -B <path-to-build>
```

Any project's top most CMakeLists.txt must start by specifying a minimum CMake version using the cmake_minimum_required() command. This establishes policy settings and ensures that the following CMake functions are run with a compatible version of CMake.

```Cmake
cmake_minimum_required(VERSION 3.10)
```

To start a project, we use the project() command to set the project name. 
```Cmake
    project(projectName)
```

Finally, the add_executable() command tells CMake to create an executable using the specified source code files.
```Cmake
    add_exectable(projectName file.cpp)
```


## Building a project
In CMake, a target is an abstract representation of something you want to build or manage — like an executable, library, or custom command.
Transitive properties refer to the properties of a target (like include directories, compile definitions, link libraries) that are automatically propagated to other targets that depend on it.
This only happens if those properties are marked INTERFACE or PUBLIC.

- create a build directory, and navigate to it
- run **cmake** to configure the project  
```shell
 cmake [<options>] <path-to-source>
              Uses  the  current working directory as the build tree, and
              <path-to-source> as the source tree.   The  specified  path
              may  be  absolute or relative to the current working direc‐
              tory.  The source tree must contain a  CMakeLists.txt  file
              and must not contain a CMakeCache.txt file because the lat‐
              ter identifies an existing build tree.  For example:

                 $ mkdir build ; cd build
                 $ cmake ../src
```

- call **build** to compile and link the project in that directory.
```shell
cmake --build
```

CMake has some special variables that are either created behind the scenes or have meaning to CMake when set by project code. Many of these variables start with CMAKE_.Two of these special user settable variables are **CMAKE_CXX_STANDARD** and **CMAKE_CXX_STANDARD_REQUIRED**. These may be used together to specify the C++ standard needed to build the project.

> Avoid this naming convention when creating variables for your projects. 

set() : set a normal, cache, or environment variable to a given value.
``` cmake
set(CMAKE_CXX_STANDARD 11)
```

One can also use variables defined in cmakelist in the source code, one way to do this is with configure header file, which is an input file, which contains variables to replace written using a special syntax, ```@var@```.

> an input file is a used as a template used to generate another file during the build process.

we use ```configure_file()``` to copy a input file to a given output file, which then replaces these variables with the current value specified in the CMakelists.txt file.
```cmake
configure_file( TutorialConfig.h.in TutorialConfig.h)
```

## Public,Private and INTERFACE keywords
```PUBLIC```, ```PRIVATE```, and ```INTERFACE``` are used to control how dependencies and include directories are propagated between targets. 

- The PUBLIC keyword means that the dependency is used both in the current target and in any target that depends on the current target. 

- The PRIVATE keyword means that the dependency is used only in the current target and not in any target that depends on it.

For example, if a library A uses a dependency B in its implementation but does not expose B in its public API, B should be linked PRIVATEly to A. This ensures that B is not required by any target that depends on A.
Conversely, if a dependency is used in the public API of a library, it should be linked PUBLICly. This means that any target that depends on the library must also link to the dependency.

- The INTERFACE keyword is used for dependencies that are not used in the implementation of the current target but are required by targets that depend on it. This is common for header-only libraries or when a library provides a public API that requires certain dependencies.

## Types of libraries

- A STATIC Library: an archive of object files for use when linking other targets.

- A SHARED Library: a dynamic library that may be linked by other targets and loaded at runtime.

- A MODULE Library: a plugin that may not be linked by other targets, but may be dynamically loaded at runtime using dlopen-like functionality.



## Add a Library 

**PROJECT_SOURCE_DIR**
This is the source directory of the last call to the project() command made in the current directory scope or one of its parents. Note, it is not affected by calls to project() made within a child directory scope (i.e. from within a call to add_subdirectory() from the current scope).

**PROJECT_BINARY_DIR**
Full path to build directory for project. This is the binary directory of the most recent project() command.

**BEFORE & AFTER**
BEFORE and AFTER are used in specific contexts to control order of operations, especially when modifying search paths or command dependencies.

BEFORE, puts this path first, so it has higher priority. This tells the compiler: "Look in /my/headers before any other places when searching for header files."
```cmake
include_directories(BEFORE /custom/include)
```

AFTER, appends the specified directories to the existing list, giving them lower priority. This is the default behavior if neither BEFORE nor AFTER is specified.
```cmake
target_include_directories(my_app AFTER PUBLIC ${CMAKE_SOURCE_DIR}/include_old)
```


To add a library in CMake, use the **add_library()** command and specify the library name along with which source files should make up the library. If no type is given the default is STATIC or SHARED based on the value of the BUILD_SHARED_LIBS variable.
```cmake
add_library(<name> [<type>] [EXCLUDE_FROM_ALL] <sources>...)
e.g.
add_library(MathFunctions MathFunctions.cxx mysqrt.cxx)
```

**Interface Library** an Interface Library target that may specify usage requirements for dependents but does not compile sources and does not produce a library artifact on disk.
```cmake
add_library(<name> INTERFACE)
e.g.
add_library(tutorial_compiler_flags INTERFACE)
```

An interface library with no source files is not included as a target in the generated buildsystem. However, it may have properties set on it and it may be installed and exported. 

We use **add_subdirectory()** to add a subdirectory to the build. The source_dir specifies the directory in which the source CMakeLists.txt and code files are located. If it is a relative path, it will be evaluated with respect to the current directory (the typical usage), but it may also be an absolute path. The binary_dir specifies the directory in which to place the output files.
```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL] [SYSTEM])
e.g.
add_subdirectory(MathFunctions)
```

We use **target_link_libraries** once the library is created to connect it to the executable target. It specifies libraries or flags to use when linking a given target and/or its dependents. It appends items to the link line for the specified target, and usage requirements from linked library targets are propagated to the target's dependencies.
```cmake
target_link_libraries(<target> ... <item>... ...)
e.g.
target_link_libraries(Tutorial MathFunctions)
```

We use **target_include_directories()** to specify where the executable target should look for include files. By using AFTER or BEFORE explicitly, you can select between appending and prepending, independent of the default. If SYSTEM is specified, the compiler will be told the directories are meant as system include directories on some platforms. 
```cmake
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE] <INTERFACE|PUBLIC|PRIVATE> [items1...])
e.g.
target_include_directories(Tutorial PUBLIC "${PROJECT_BINARY_DIR}")
```


**option** Provide a boolean option (a variable) that the user can optionally select and can change while configuring their cmake build. 
```cmake
option(<variable> "<help_text>" [value])
e.g.
option(USE_MYMATH "use tutorial provided math funcs." ON)
```

**if** Evaluates the condition argument of the if clause according to the Condition syntax described below. If the result is true, then the commands in the if block are executed. Otherwise, optional elseif blocks are processed in the same way. Finally, if no condition is true, commands in the optional else block are executed.
```cmake
if(<condition>)
  <commands>
elseif(<condition>) # optional block, can be repeated
  <commands>
else()              # optional block
  <commands>
endif()
e.g.
if(USE_MYMATH)
    target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
endif()
```

**target_compile_defintions** Add compile definitions to a target.
```cmake
target_compile_definitions(<target> <INTERFACE|PUBLIC|PRIVATE> [items1...])
e.g.
 target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
```

## Usage Requirements
Usage requirements refer to properties of a target that specify how other targets should compile and link when they use that target, via target_link_libraries.

The primary commands that leverage usage requirements are:

- **target_compile_definitions()**: Specifies compile definitions to use when compiling a given target. 
```cmake
target_compile_definitions(<target> <INTERFACE|PUBLIC|PRIVATE> [items1...])
e.g.
  target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
```

- **target_compile_options**: Adds options to the COMPILE_OPTIONS or INTERFACE_COMPILE_OPTIONS target properties. These options are used when compiling the given target,
```cmake
target_compile_options(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...])
```

- **target_include_directories**: Specifies include directories to use when  compiling a given target.
```cmake
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...])
e.g.
target_include_directories(MathFunctions INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})
```

- **target_link_directories**: Specifies the paths in which the linker should search for libraries when linking a given target.
```cmake
target_link_directories(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...])
```

- **target_link_options**: Add options to the link step for an executable, shared library or module library target.
```cmake
target_link_options(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...])
```

- **target_precompile_headers**: The command adds header files to the PRECOMPILE_HEADERS and/or INTERFACE_PRECOMPILE_HEADERS target properties.
> Precompiling header files can speed up compilation by creating a partially processed version of some header files, and then using that version during compilations rather than repeatedly parsing the original headers.
```cmake
target_precompile_headers(<target>
  <INTERFACE|PUBLIC|PRIVATE> [header1...])
```

- **target_sources**: Specifies sources to use when building a target and/or its dependents. 
```cmake
target_sources(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...])
```

- **target_compile_features**: Specifies compiler features required when compiling a given target. If the feature is not listed in the CMAKE_C_COMPILE_FEATURES CMAKE_CUDA_COMPILE_FEATURES, or CMAKE_CXX_COMPILE_FEATURES variables, then an error will be reported by CMake.


## Generator expressions
Generator expressions in CMake are special expressions that are evaluated during the generation step of CMake. They have the form ```$<...>``` and can be nested.
```cmake
target_include_directories(tgt PRIVATE /opt/include/$<CXX_COMPILER_ID>)
```
Generator expressions are allowed in the context of many target properties, such as LINK_LIBRARIES, INCLUDE_DIRECTORIES, COMPILE_DEFINITIONS and others. They may also be used when using commands to populate those properties, such as target_link_libraries(), target_include_directories(), target_compile_definitions() and others.

There are different types of generator expressions including Logical, Informational, and Output expressions. A ```$<0:...>``` results in the empty string, and ```$<1:...>``` results in the content of ```...```. Some examgples,

| Type               | Example                                | What it Does                                                 |
|--------------------|-----------------------------------------|--------------------------------------------------------------|
| **Conditionals**   | `$<IF:$<CONFIG:Debug>,ON,OFF>`          | If config is Debug → ON, else OFF                           |
| **Configurations** | `$<CONFIG:Debug>`                       | True if config is Debug                                     |
| **Platform/Tool**  | `$<CXX_COMPILER_ID:GNU>`                | True if using GNU compiler                                  |
| **Target Info**    | `$<TARGET_PROPERTY:target,property>`    | Get a property from another target                          |
| **Path**           | `$<TARGET_FILE:target>`                 | Get full path to built target                               |
| **Boolean**        | `$<BOOL:expression>`                    | True if expression is not empty or 0                        |
| **Language Check** | `$<COMPILE_LANGUAGE:CXX>`               | True if language is C++                                     |
| **Usage Context**  | `$<BUILD_INTERFACE:...>`                | Applies during build only                                   |
|                    | `$<INSTALL_INTERFACE:...>`              | Applies during install only                                 |


## Installing & Testing
### Installing
**build** compiles your code, links binaries and outputs them to a local build directory. here everything stays in the build tree and can be runned and tested directly. Wheras **install** copies compiled binaries, headers, CMake config files, etc. to a proper install location, Prepares the project for reuse,packaging or distribution.

the **install()** command generates installation rules for a project. **install rules** define what gets installed and where when you run ```cmake -install .```. They tell CMkae how to copy your compiled binaries, libraries and headers, config files and other resources to a destination directory.

| Purpose                | Command Example                                                                 |
|------------------------|----------------------------------------------------------------------------------|
| Install executables    | `install(TARGETS my_app DESTINATION bin)`                                       |
| Install libraries      | `install(TARGETS my_lib DESTINATION lib)`                                       |
| Install headers        | `install(FILES my_header.h DESTINATION include)`                                |
| Install directory      | `install(DIRECTORY include/ DESTINATION include)`                               |
| Install export config  | `install(EXPORT MyTargets DESTINATION lib/cmake/MyProject)`                     |
| Install config files   | `install(FILES MyConfig.cmake DESTINATION lib/cmake/MyProject)`                 |
| Install scripts/docs   | `install(FILES README.md DESTINATION share/doc/MyProject)`                      |

```cmake
e.g.
list(APPEND installable_libs SqrtLibrary)
install(TARGETS ${installable_libs} DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
install(TARGETS Tutorial DESTINATION bin)
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  DESTINATION include )

```
To run the install steo use the ```--install``` option of the cmake command, in older versions, below cmake 3.15 one has to used ```make install``` 
```shell
cmake --install .
```

### Testing
**cTest** a test automation tool distributed as a part of CMake, is used for running and managing tests in a project's build directory. 

- **enable_testing** : Enables testing for this directory and below. The ctest expects to find a test file in the top-level build directory, so it should be in the top-level source directory. This command is automatically invoked when the CTest module is included, except if the BUILD_TESTING option is turned off.
```cmake
enable_testing()
```
- **add_test** : Adds a test to the project to be run by ctest. Tests added with the add_test(NAME) signature support using generator expressions in test properties set by set_property, test properties may only be set in the directory the test is created in.
```cmake
add_test(NAME <name> COMMAND <command> [<arg>...]
         [CONFIGURATIONS <config>...]
         [WORKING_DIRECTORY <dir>]
         [COMMAND_EXPAND_LISTS])
e.g.
add_test(NAME StandardUse COMMAND Tutorial 4)
```
- **set_tests_properties** : sets a property for tests. 
```cmake
set_tests_properties(<tests>...
                     [DIRECTORY <dir>]
                     PROPERTIES <prop1> <value1>
                     [<prop2> <value2>]...)
e.g
set_tests_properties(StandardUse PROPERTIES PASS_REGULAR_EXPRESSION "4 is 2")
# PASS_REGULAR_EXPRESSION Property : A regular expression (or plain string) that must appear in the test’s standard output for it to be considered a success.
```
- **function** : Start recording a function for later invocation as a command. Defines a function with a name that takes arguments named arg1, ... The commands in the function definition are recorded; they are not executed until the function is invoked. Per legacy, the endfunction() command admits an optional name argument. If used, it must be a verbatim repeat of the argument of the opening function command.
```cmake
function(<name> [<arg1> ...])
  <commands>
endfunction()
e.g
function(do_test target arg result)
add_test(NAME Comps${arg} COMMAND ${target} ${arg})
set_tests_properties(Comps${arg} PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endfunction()

#Function Invocation
do_test(Tutorial 4 "4 is 2")
```

## Testing Dashboard
One can also add support for submitting the test results to a dashboard, CDash. CDash is an open source, web-based software testing server that aggregates, analyzes and displays the results of software testing processes submitted from clients worldwide. So we just have to run those tests and submit them to CDash. 
Doing this is simple, first  include CTest module in the top-level directory cmake file. This will enable testing with CTest as well as dashboard submissions to CDash, so we can safely remove the call to enable_testing().
We will also need to acquire a CTestConfig.cmake file to be placed in the top-level directory. When run, the ctest executable will read this file to gather information about the testing dashboard. It contains:
- The project name
- The project "Nightly" start time
- The time when a 24 hour "day" starts for this project.
- The URL of the CDash instance where the submission's generated documents will be sent.
In the cmake tuts, a public dashboard server is used and its corresponding CTestConfig.cmake file is provided for you in this step's root directory. 
In practice, this file would be downloaded from a project's Settings page on the CDash instance intended to host the test results. Once downloaded from CDash, the file should not be modified locally.
```CTestConfig.cmake
CTestConfig.cmake
set(CTEST_PROJECT_NAME "CMakeTutorial")
set(CTEST_NIGHTLY_START_TIME "00:00:00 EST")

set(CTEST_DROP_METHOD "http")
set(CTEST_DROP_SITE "my.cdash.org")
set(CTEST_DROP_LOCATION "/submit.php?project=CMakeTutorial")
set(CTEST_DROP_SITE_CDASH TRUE)
```
To create a simple test dashboard, run the cmake executable or the cmake-gui to configure the project but do not build it yet. Instead, navigate to the build directory and run:
```shell
ctest [-VV] -D Experimental
```

## System Introspection
System introspection refers to the ability to detect and adapt to the build system environment- like checking for compilers, libraries, headerfiles etc. at configuration times.

- **check_cxx_source_compiles** : Checks that if the source code supplies can be built. the result is stored in the internal cache variable, with boolean true.
```cmake
check_cxx_source_compiles(<code> <resultVar>
                          [FAIL_REGEX <regex1> [<regex2>...]])
e.g
check_cxx_source_compiles("
  #include <cmath>
  int main(){
    std::log(1.0)
    return 0;
  }
" HAVE_LOG)
```
We use if conditions and target_compile_defintions to check which sources are available and link resources for compilation accordingly.
```cmake
e.g
  if(HAVE_LOG AND HAVE_EXP)
    target_compile_definitions(SqrtLibrary
                               PRIVATE "HAVE_LOG" "HAVE_EXP"
                               )
  endif()
  target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
```
Next in the source code we have to check for the value of the result value variable, which contains the information about the availability of the resources. This is done with the help of preprocessors like #ifdef
```C
e.g
#if defined(HAVE_LOG) && defined(HAVE_EXP)
  double result = std::exp(std::log(x) * 0.5);
  std::cout << "Computing sqrt of " << x << " to be " << result
            << " using log and exp" << std::endl;
#else
  double result = x;
  for (int i = 0; i < 10; ++i) {
    if (result <= 0) {
      result = 0.1;
    }
    double delta = x - (result * result);
    result = result + 0.5 * delta / result;
    std::cout << "Computing sqrt of " << x << " to be " << result << std::endl;
  }
#endif
```
## Adding custom command

**add_custom_command** : Adds a custom build rule to the generated build system. There are two main valid ways for calling the add_custom_command.

- The First for adding a custom command to produce an output:
```cmake
add_custom_command(OUTPUT output1 [output2 ...]
                   COMMAND command1 [ARGS] [args1...]
                   [COMMAND command2 [ARGS] [args2...] ...]
                   [MAIN_DEPENDENCY depend]
                   [DEPENDS [depends...]]
                   [BYPRODUCTS [files...]]
                   [IMPLICIT_DEPENDS <lang1> depend1
                                    [<lang2> depend2] ...]
                   [WORKING_DIRECTORY dir]
                   [COMMENT comment]
                   [DEPFILE depfile]
                   [JOB_POOL job_pool]
                   [JOB_SERVER_AWARE <bool>]
                   [VERBATIM] [APPEND] [USES_TERMINAL]
                   [CODEGEN]
                   [COMMAND_EXPAND_LISTS]
                   [DEPENDS_EXPLICIT_ONLY])
e.g.
add_custom_command(
  OUTPUT ${CMAKE_BINARY_DIR}/generated.txt
  COMMAND echo "This is generated content" > ${CMAKE_BINARY_DIR}/generated.txt
  DEPENDS input.txt
  COMMENT "Generating a custom file"
)

add_custom_target(GenerateFile ALL
  DEPENDS ${CMAKE_BINARY_DIR}/generated.txt
)

```
This defines a command to generate specified output files. A target created in the same directory (CMakeLists.txt file) that specifies any output of the custom command as a source file is given a rule to generate the file using the command at build time. 

- The second adds a custom command to a target such as a library or executable. This is useful for performing an operation before or after building the target. The command becomes part of the target and will only execute when the target itself is built. If the target is already built, the command will not execute.
```cmake
add_custom_command(TARGET <target>
                   PRE_BUILD | PRE_LINK | POST_BUILD
                   COMMAND command1 [ARGS] [args1...]
                   [COMMAND command2 [ARGS] [args2...] ...]
                   [BYPRODUCTS [files...]]
                   [WORKING_DIRECTORY dir]
                   [COMMENT comment]
                   [VERBATIM]
                   [COMMAND_EXPAND_LISTS]
                   [USES_TERMINAL])

e.g.

add_executable(MyApp main.cpp)

add_custom_command(TARGET MyApp
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E echo "Build complete!"
)
```

## Packaging an installer
If we want to distribute our project, then for providing both binary and source distributions on a variety of platforms. This is a little different from the install, where we were installing the binaries that we had built from the source code.

For building installation packages that support binary installations and package management features, we will use CPack to create platform specific installers. Specifically we need to add a few lines to the bottom of our top-level CMakeLists.txt file.
```cmake
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
set(CPACK_GENERATOR "TGZ")
set(CPACK_SOURCE_GENERATOR "TGZ")
include(CPack)

```

We start by including InstallRequiredSystemLibraries. This module will include any runtime libraries that are needed by the project for the current platform. Next we set some CPack variables to where we have stored the license and version information for this project. The CPACK_GENERATOR and CPACK_SOURCE_GENERATOR variables select the generators used for binary and source installations, respectively.
Finally we include the CPack module which will use these variables and some other properties of the current system to setup an installer.

The next step is to build the project in the usual manner and then run the cpack executable. To build a binary distribution, from the binary directory run:
```shell
  cpack
```
To specify the binary generator, use the -G option. For multi-config builds, use -C to specify the configuration. An archive generator like ZIP creates a compressed archive of all installed files.
```shell
  cpack -G ZIP -C Debug
```
To create an archive of the full source tree you would type:
```shell
  cpack --config CPackSourceConfig.cmake
```

## Selecting Static/Shared Libraries
**BUILD_SHARED_LIBS** variable can be used to control the default behavior of add_library(), and allow control over how libraries without an explicit type (STATIC, SHARED, MODULE or OBJECT) are built.
For this if we need to add BUILD_SHARED_LIBS to the top-level CMakeLists.txt. We use the option() command as it allows users to optionally select if the value should be ON or OFF. After that we specify output directories for our static and shared libraries.
```cmake
option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")

option(BUILD_SHARED_LIBS "Build using shared libraries" ON)
```
update the library to use dll exports. When building a shared library (DLL) on Windows, you must explicitly specify which functions or symbols should be exported from the DLL and which should be imported when using the DLL from another program.
```c
#if defined(_WIN32)
#  if defined(EXPORTING_MYMATH)
#    define DECLSPEC __declspec(dllexport)
#  else
#    define DECLSPEC __declspec(dllimport)
#  endif
#else /*non windows*/
#  define DECLSPEC
#endif

namespace mathfunctions {
double DECLSPEC sqrt(double x);
}
```
In the example above the code checks if you're compiling on a Windows system. If EXPORTING_MYMATH is defined (usually in the DLL project itself), it sets DECLSPEC to __declspec(dllexport) — this tells the compiler to export the function from the DLL Otherwise, it sets DECLSPEC to __declspec(dllimport) — this tells the compiler to import the function from a DLL (used by the client using the library). On non-Windows platforms (like Linux/macOS), symbol visibility is handled differently, so it just leaves DECLSPEC empty.

You cannot combine a static library without position independent code with a library that has position independent code. The solution to this is to explicitly set the POSITION_INDEPENDENT_CODE target property of library to be True when building shared libraries.
```cmake
  set_target_properties(SqrtLibrary PROPERTIES
                        POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS}
                        )
```                        
Define EXPORTING_MYMATH stating we are using declspec(dllexport) when building on Windows.
```cmake
target_compile_definitions(MathFunctions PRIVATE "EXPORTING_MYMATH")
```

## Adding Export Configuration
The install(TARGETS) commands to not only specify a DESTINATION but also an EXPORT. The EXPORT keyword generates a CMake file containing code to import all targets listed in the install command from the installation tree. 
```cmake
e.g
install(TARGETS ${installable_libs}
        EXPORT MathFunctionsTargets
        DESTINATION lib)
```
After exporting we also need to explicitly install the generated MathFunctionsTargets.cmake file. This is done by adding the following to the bottom of the top-level CMakeLists.txt.
```cmake
CMakeLists.txt
install(EXPORT MathFunctionsTargets
FILE MathFunctionsTargets.cmake
DESTINATION lib/cmake/MathFunctions
)
```
During the generation of the export information it will export a path that is intrinsically tied to the current machine and will not be valid on other machines. The solution to this is to update the target_include_directories() to understand that it needs different INTERFACE locations when being used from within the build directory and from an install / package.
```cmake
target_include_directories(MathFunctions
                           INTERFACE
                            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
                            $<INSTALL_INTERFACE:include>
                           )
```
For the find_package() command to be able to find our project we have to generate a nameConfig.cmake file we do this by adding a new file to the top-level of the project called Config.cmake.in with the following contents:
```Config.cmake.in
@PACKAGE_INIT@

include ( "${CMAKE_CURRENT_LIST_DIR}/MathFunctionsTargets.cmake" )
```
to properly configure and install that file, add the following to the bottom of the top-level CMakeLists.txt
```cmake
install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)

include(CMakePackageConfigHelpers)
```

**configure_package_config_file** : Create a confi file for a project. should be used instead of the plain configure_file() command when creating the PackageName Config.cmake or PackageName-config.cmake file for installing a project or library. It helps make the resulting package relocatable by avoiding hardcoded paths in the installed PackageNameConfig.cmake file. To properly utilize this function, the input file should have a single line with the text @PACKAGE_INIT@ in addition to the content that is desired. That variable will be replaced with a block of code which turns set values into relative paths. These values which are new can be referenced by the same name but prepended with a PACKAGE_ prefix.
```cmake
configure_package_config_file(<input> <output>
  INSTALL_DESTINATION <path>
  [PATH_VARS <var1> <var2> ... <varN>]
  [NO_SET_AND_CHECK_MACRO]
  [NO_CHECK_REQUIRED_COMPONENTS_MACRO]
  [INSTALL_PREFIX <path>]
  )

e.g
include(CMakePackageConfigHelpers)
# generate the config file that includes the exports
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
  INSTALL_DESTINATION "lib/cmake/MathFunctions"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO
  )
```

**write_basic_packafe_version_file()** : Writes a file for use as a PackageNameConfigVersion.cmake file to filename. This command writes a file which is used by find_package(), documenting the version and compatibility of the desired package.
```cmake
write_basic_package_version_file(<filename>
  [VERSION <major.minor.patch>]
  COMPATIBILITY <AnyNewerVersion|SameMajorVersion|SameMinorVersion|ExactVersion>
  [ARCH_INDEPENDENT] )

e.g
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake"
  VERSION "${Tutorial_VERSION_MAJOR}.${Tutorial_VERSION_MINOR}"
  COMPATIBILITY AnyNewerVersion
)
```

To finally generate a relocatable CMake Configuration for our project that can be used after the project has been installed or packaged, set both of the above generated files to be installed.
```cmake
e.g
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake
  DESTINATION lib/cmake/MathFunctions
  )
```
If we want our project to also be used from a build directory we only have to add the following to the bottom of the top level CMakeLists.txt:
```cmake
export(EXPORT MathFunctionsTargets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsTargets.cmake"
)
```

## Packaging & Debug release
By default, CMake's model is that a build directory only contains a single configuration, be it Debug, Release, MinSizeRel, or RelWithDebInfo. It is possible, however, to setup CPack to bundle multiple build directories and construct a package that contains multiple configurations of the same project.
we want to ensure that the debug and release builds use different names for the libraries that will be installed. Let's use d as the postfix for the debug libraries. For this Set CMAKE_DEBUG_POSTFIX near the beginning of the top-level CMakeLists.txt file and the DEBUG_POSTFIX property on the tutorial executable:
```cmake
set(CMAKE_DEBUG_POSTFIX d)
add_library(tutorial_compiler_flags INTERFACE)

add_executable(Tutorial tutorial.cxx)
set_target_properties(Tutorial PROPERTIES DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})

target_link_libraries(Tutorial PUBLIC MathFunctions tutorial_compiler_flags)
```
Also add version numbering to the library. In Library/CMakeLists.txt, set the ```VERSION``` and ```SOVERSION``` properties
```cmake
set_property(TARGET MathFunctions PROPERTY VERSION "1.0.0")
set_property(TARGET MathFunctions PROPERTY SOVERSION "1")
```
create debug and release subdirectories. The layout will look like
```shell
- ProjSourceDir
   - debug
   - release

```
For setup we can use CMAKE_BUILD_TYPE to set the configuration type
```shell
cd debug
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake --build .
cd ../release
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```

we can use a custom configuration file to package both builds into a single release. In the Project directory, create a file called MultiCPackConfig.cmake. In this file, first include the default configuration file that was created by the cmake executable. Next, use the ```CPACK_INSTALL_CMAKE_PROJECTS``` variable to specify which projects to install. For if we want to install both debug and release.
```MultiCPackConfig.cmake
include("release/CPackConfig.cmake")

set(CPACK_INSTALL_CMAKE_PROJECTS
    "debug;Tutorial;ALL;/"
    "release;Tutorial;ALL;/"
    )
```
From the Project Source directory, run cpack specifying our custom configuration file with the config option
```shell
cpack --config MultiCPackConfig.cmake
```