#
# This file is part of the open source part of the
# Platform for Algorithm Development and Rendering (PADrend).
# Web page: http://www.padrend.de/
# Copyright (C) 2009-2013 Benjamin Eikel <benjamin@eikel.org>
# 
# PADrend consists of an open source part and a proprietary part.
# The open source part of PADrend is subject to the terms of the Mozilla
# Public License, v. 2.0. You should have received a copy of the MPL along
# with this library; see the file LICENSE. If not, you can obtain one at
# http://mozilla.org/MPL/2.0/.
#
cmake_minimum_required(VERSION 2.8.8)

project(PADrendComplete)

option(BUILD_SHARED_LIBS "Build shared instead of static libraries." ON)

# Make sure the libraries are in the same directory as the executable.
if(WIN32)
	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR})
endif()

if(NOT DEFINED CMAKE_PREFIX_PATH)
	# Tell CMake to search in the "ThirdParty" directory
	execute_process(
		COMMAND ${CMAKE_CXX_COMPILER} -dumpmachine
		OUTPUT_VARIABLE ARCH_TRIPLET
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
	set(EXTERNAL_LIBS_PATH "${CMAKE_CURRENT_SOURCE_DIR}/ThirdParty/${ARCH_TRIPLET}")
	if(IS_DIRECTORY ${EXTERNAL_LIBS_PATH})
		set(CMAKE_PREFIX_PATH ${EXTERNAL_LIBS_PATH})
		if(WIN32)
			file(GLOB EXTERNAL_DLLS "${EXTERNAL_LIBS_PATH}/bin/*.dll")
			install(FILES ${EXTERNAL_DLLS} DESTINATION bin COMPONENT runtimelibraries)
		else()
			if(APPLE)
				file(GLOB EXTERNAL_DYLIBS "${EXTERNAL_LIBS_PATH}/lib/*.dylib")
				install(FILES ${EXTERNAL_DYLIBS} DESTINATION lib COMPONENT runtimelibraries)
			endif()
		endif()
	endif()
endif()

add_subdirectory(modules/Geometry)
add_subdirectory(modules/Util)
add_subdirectory(modules/Rendering)
add_subdirectory(modules/GUI)
add_subdirectory(modules/Sound)
add_subdirectory(modules/MinSG)
add_subdirectory(modules/EScript)
add_subdirectory(modules/E_Geometry)
add_subdirectory(modules/E_Util)
add_subdirectory(modules/E_Rendering)
add_subdirectory(modules/E_GUI)
add_subdirectory(modules/E_Sound)
add_subdirectory(modules/E_MinSG)
add_subdirectory(PADrend)

install(DIRECTORY plugins
	DESTINATION bin COMPONENT plugins
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)
if(IS_DIRECTORY extPlugins)
	install(DIRECTORY extPlugins
		DESTINATION bin COMPONENT extplugins
		PATTERN ".svn" EXCLUDE
		PATTERN ".git" EXCLUDE
	)
endif()

if(CMAKE_GENERATOR MATCHES "Xcode")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD "c++11")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
endif()
