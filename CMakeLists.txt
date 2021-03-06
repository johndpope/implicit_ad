#
# preamble
CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
PROJECT(implicit_ad)
SET(implicit_ad_version "20171020")
#
# print_variable
MACRO(print_variable variable_name)
	MESSAGE(STATUS "${variable_name} = ${${variable_name}}")
ENDMACRO(print_variable)
#
# cmake variables that affect build
print_variable(CMAKE_VERBOSE_MAKEFILE)
print_variable(CMAKE_BUILD_TYPE)
#
SET(extra_cxx_flags "" CACHE STRING
	"Additional C++ compile flags, besides debug and release flags"
)
print_variable(extra_cxx_flags)
#
# find pkg-config
FIND_PACKAGE(PkgConfig)
#
# use pkg-config to find eigen include directories
pkg_check_modules(eigen3 REQUIRED eigen3)
print_variable(eigen3_INCLUDE_DIRS)
INCLUDE_DIRECTORIES( SYSTEM ${eigen3_INCLUDE_DIRS} )
#
# use pack-config to find ipopt libraries
pkg_check_modules(ipopt ipopt)
IF( NOT ipopt_FOUND )
	MESSAGE(STATUS
"If you get the message 'ipopt' required by 'cppad',
	you must fix the seeting of ipopt_pkg_config_path."
	)
ENDIF( NOT ipopt_FOUND )
#
# use pkg-config to find cppad include directories
pkg_check_modules(cppad REQUIRED cppad)
print_variable(cppad_INCLUDE_DIRS)
INCLUDE_DIRECTORIES( ${cppad_INCLUDE_DIRS} )
#
# run_check
ADD_EXECUTABLE(run_check
	src/run_check.cpp
	src/control.cpp
	src/utility.cpp
	src/implicit_kedem.cpp
	src/implicit_newton.cpp
)
SET_TARGET_PROPERTIES(run_check PROPERTIES COMPILE_FLAGS "${extra_cxx_flags}")
#
# check
ADD_CUSTOM_TARGET(check run_check DEPENDS run_check)
#
# time
ADD_EXECUTABLE(time
	src/time.cpp
)
SET_TARGET_PROPERTIES(time PROPERTIES COMPILE_FLAGS "${extra_cxx_flags}")
