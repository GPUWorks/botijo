cmake_minimum_required(VERSION 3.4)
project(botijo-project)

# Enable C++11:
set(CMAKE_CXX_STANDARD 11)

# Detect Clang:
if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(CMAKE_COMPILER_IS_CLANGXX 1)
endif()

# Set compiler warnings
# (https://github.com/lefticus/cppbestpractices/blob/master/02-Use_the_Tools_Available.md#compilers)
# (https://stackoverflow.com/questions/3375697/useful-gcc-flags-for-c)
# (https://stackoverflow.com/questions/5088460/flags-to-enable-thorough-and-verbose-g-warnings):
if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_CLANGXX)
    add_definitions("-Wall -Wextra -Wshadow -Wnon-virtual-dtor -Wcast-align -Wcast-qual -Wunused
	-Woverloaded-virtual -Wpedantic -Wdouble-promotion -Wformat=2 -Wfloat-equal -Wpointer-arith
	-Wwrite-strings -Wswitch-enum -Wunreachable-code -Winit-self -Wuninitialized
	-Wno-unused-result -Wctor-dtor-privacy -Wdisabled-optimization -Wmissing-declarations
        -Wredundant-decls -Wno-unused")
endif()

# Enable fast math support
# (https://stackoverflow.com/questions/7420665/what-does-gccs-ffast-math-actually-do):
if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_CLANGXX)
    add_definitions("-ffast-math")
elseif(MSVC)
    add_definitions("/fp:fast")
endif()

# Enable native CPU features support:
if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_CLANGXX)
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES "arm")
        add_definitions("-march=armv7-a -mtune=cortex-a8 -mfpu=neon")
    else()
        add_definitions("-mavx")
    endif()
elseif(MSVC)
    add_definitions("/arch:AVX")
endif()

# Add library subdirectory:
add_subdirectory(lib)

# Define build all artefacts option:
option(BTJ_BUILD_ALL "Build all botijo artefacts" OFF)

# Add tests subdirectory:
option(BTJ_BUILD_TESTS "Build botijo tests" OFF)
if(BTJ_BUILD_TESTS OR BTJ_BUILD_ALL)
    add_subdirectory(tests)
endif()
