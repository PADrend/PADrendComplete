#
# This file is part of the open source part of the
# Platform for Algorithm Development and Rendering (PADrend).
# Web page: http://www.padrend.de/
# Copyright (C) 2009-2013 Benjamin Eikel <benjamin@eikel.org>
# Copyright (C) 2014-2019 Sascha Brandt <sascha@brandt.graphics>
# 
# PADrend consists of an open source part and a proprietary part.
# The open source part of PADrend is subject to the terms of the Mozilla
# Public License, v. 2.0. You should have received a copy of the MPL along
# with this library; see the file LICENSE. If not, you can obtain one at
# http://mozilla.org/MPL/2.0/.
#
cmake_minimum_required(VERSION 3.1.0)

project(PADrendComplete VERSION 1.1.0)

option(BUILD_SHARED_LIBS "Build shared instead of static libraries." OFF)

# Make sure the libraries are in the same directory as the executable.
if(WIN32)
	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR})
endif()

option(BUILD_SOUND "Build PADrend with sound support (requires OpenAL)" OFF)
option(BUILD_ALL_TESTS "Build all PADrend tests" OFF)

if(BUILD_ALL_TESTS)
	include(CTest)
	set(UTIL_BUILD_TESTS ON CACHE BOOL "" FORCE)
	#set(RENDERING_BUILD_TESTS ON CACHE BOOL "" FORCE)
	#set(MINSG_BUILD_TESTS ON CACHE BOOL "" FORCE)
	set(GEOMETRY_BUILD_TESTS ON CACHE BOOL "" FORCE)
	set(BUILD_ESCRIPT_TEST ON CACHE BOOL "" FORCE)	
endif()

add_subdirectory(modules/Geometry)
add_subdirectory(modules/Util)
add_subdirectory(modules/Rendering)
add_subdirectory(modules/GUI)
if(BUILD_SOUND)
	add_subdirectory(modules/Sound)
endif()
add_subdirectory(modules/MinSG)
add_subdirectory(modules/EScript)
add_subdirectory(modules/E_Geometry)
add_subdirectory(modules/E_Util)
add_subdirectory(modules/E_Rendering)
add_subdirectory(modules/E_GUI)
if(BUILD_SOUND)
	add_subdirectory(modules/E_Sound)
endif()
add_subdirectory(modules/E_MinSG)
add_subdirectory(PADrendMain)
add_subdirectory(plugins)
if(IS_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/extPlugins")
	add_subdirectory(extPlugins)
endif()

if(CMAKE_GENERATOR MATCHES "Xcode")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD "c++11")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
endif()
