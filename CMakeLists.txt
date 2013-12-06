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

set(PADREND_VERSION_MAJOR 0)
set(PADREND_VERSION_MINOR 7)
set(PADREND_VERSION_PATCH 0)
set(PADREND_VERSION ${PADREND_VERSION_MAJOR}.${PADREND_VERSION_MINOR}.${PADREND_VERSION_PATCH})
set(PADREND_DESCRIPTION "Platform for Algorithm Development and Rendering (PADrend)")
set(PADREND_AUTHORS "Benjamin Eikel; Claudius Jaehn; Ralf Petring")

option(BUILD_SHARED_LIBS "Build shared instead of static libraries." ON)

# Make sure the libraries are in the same directory as the executable.
if(WIN32)
	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR})
endif()

if(NOT DEFINED CMAKE_PREFIX_PATH)
	# Tell CMake to search in the "libs" directory
	execute_process(
		COMMAND ${CMAKE_CXX_COMPILER} -dumpmachine
		OUTPUT_VARIABLE ARCH_TRIPLET
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
	set(EXTERNAL_LIBS_PATH "${CMAKE_CURRENT_SOURCE_DIR}/libs/${ARCH_TRIPLET}")
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

if(WIN32)
	configure_file("${CMAKE_CURRENT_SOURCE_DIR}/resources/Windows/Resource.rc.in" "${CMAKE_CURRENT_BINARY_DIR}/Resource.rc" @ONLY)
	set(PADREND_RESOURCE_FILES "${CMAKE_CURRENT_BINARY_DIR}/Resource.rc")
endif()

include_directories(EScript)
include_directories(src)

add_subdirectory(src/Geometry)
add_subdirectory(src/Util)
add_subdirectory(src/Rendering)
add_subdirectory(src/GUI)
add_subdirectory(src/Sound)
add_subdirectory(src/MinSG)
add_subdirectory(EScript)
add_subdirectory(src/E_Geometry)
add_subdirectory(src/E_Util)
add_subdirectory(src/E_Rendering)
add_subdirectory(src/E_GUI)
add_subdirectory(src/E_Sound)
add_subdirectory(src/E_MinSG)
add_subdirectory(src/PADrend)

if(WIN32)
	set_property(TARGET PADrend APPEND PROPERTY INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/resources/Windows")
endif()

install(DIRECTORY plugins
	DESTINATION bin COMPONENT plugins
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)
install(DIRECTORY extPlugins
	DESTINATION bin COMPONENT extplugins
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)
install(DIRECTORY resources
	DESTINATION bin COMPONENT resources
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)
install(DIRECTORY EScript
	DESTINATION src/EScript COMPONENT sources
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)
install(DIRECTORY src
	DESTINATION src COMPONENT sources
	PATTERN ".svn" EXCLUDE
	PATTERN ".git" EXCLUDE
)

if(CMAKE_GENERATOR MATCHES "Xcode")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD "c++11")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
endif()

# Packaging
set(CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS_SKIP ON)
include(InstallRequiredSystemLibraries)
if(WIN32)
	install(FILES ${CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS} DESTINATION bin COMPONENT runtimelibraries)
	# Search the "libgcc_s_sjlj-1.dll" file
	execute_process(
		COMMAND ${CMAKE_CXX_COMPILER} -print-file-name=libgcc_s_sjlj-1.dll
		OUTPUT_VARIABLE GCC_S_SJLJ_LIBRARY
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
	if(EXISTS ${GCC_S_SJLJ_LIBRARY})
		install(FILES ${GCC_S_SJLJ_LIBRARY} DESTINATION bin COMPONENT runtimelibraries)
	endif()
	# Search the "libstdc++-6.dll" file
	execute_process(
		COMMAND ${CMAKE_CXX_COMPILER} -print-file-name=libstdc++-6.dll
		OUTPUT_VARIABLE STDCPP_LIBRARY
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
	if(EXISTS ${STDCPP_LIBRARY})
		install(FILES ${STDCPP_LIBRARY} DESTINATION bin COMPONENT runtimelibraries)
	endif()
else()
	install(FILES ${CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS} DESTINATION lib COMPONENT runtimelibraries)
endif()

set(CPACK_PACKAGE_NAME "PADrend")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY ${PADREND_DESCRIPTION})
set(CPACK_PACKAGE_VENDOR ${PADREND_AUTHORS})
set(CPACK_PACKAGE_CONTACT "Benjamin Eikel <benjamin@eikel.org>")
set(CPACK_PACKAGE_VERSION_MAJOR ${PADREND_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PADREND_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PADREND_VERSION_PATCH})
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/NSIS_windows/Notice.txt")

set(CPACK_DEBIAN_PACKAGE_HOMEPAGE "http://www.padrend.de/")

set(CPACK_NSIS_MUI_ICON "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/NSIS_windows/PADrendIcon.ico")
set(CPACK_NSIS_MUI_UNIICON "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/NSIS_windows/PADrendIcon.ico")
set(CPACK_NSIS_INSTALLED_ICON_NAME "bin/PADrend.exe")
set(CPACK_NSIS_URL_INFO_ABOUT "http://www.padrend.de/")
set(CPACK_NSIS_MODIFY_PATH ON)
set(CPACK_PACKAGE_INSTALL_REGISTRY_KEY ${CPACK_PACKAGE_NAME})

if(APPLE)
	set(CPACK_BUNDLE_NAME            "PADrend")
	set(CPACK_BUNDLE_ICON            "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/Bundle_OSX/PADrendIcon.icns")
	set(CPACK_BUNDLE_PLIST           "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/Bundle_OSX/Info.plist")
	set(CPACK_BUNDLE_STARTUP_COMMAND "${CMAKE_CURRENT_SOURCE_DIR}/resources/Installer/Bundle_OSX/start_padrend.sh")
endif()

set(CPACK_CREATE_DESKTOP_LINKS PADrend)
set(CPACK_PACKAGE_EXECUTABLES "PADrend" "PADrend")

set(CPACK_STRIP_FILES ON)

set(CPACK_COMPONENTS_ALL applications developmentlibraries extplugins headers plugins resources runtimelibraries sources)
set(CPACK_COMPONENT_APPLICATIONS_DISPLAY_NAME "Applications")
set(CPACK_COMPONENT_APPLICATIONS_DEPENDS runtimelibraries plugins resources)
set(CPACK_COMPONENT_APPLICATIONS_GROUP "Runtime")
set(CPACK_COMPONENT_DEVELOPMENTLIBRARIES_DISPLAY_NAME "Development Libraries")
set(CPACK_COMPONENT_DEVELOPMENTLIBRARIES_DEPENDS headers)
set(CPACK_COMPONENT_DEVELOPMENTLIBRARIES_GROUP "Development")
set(CPACK_COMPONENT_EXTPLUGINS_DISPLAY_NAME "External Plug-ins")
set(CPACK_COMPONENT_EXTPLUGINS_DEPENDS plugins)
set(CPACK_COMPONENT_EXTPLUGINS_GROUP "Runtime")
set(CPACK_COMPONENT_HEADERS_DISPLAY_NAME "C++ header files")
set(CPACK_COMPONENT_HEADERS_GROUP "Development")
set(CPACK_COMPONENT_PLUGINS_DISPLAY_NAME "Core Plug-ins")
set(CPACK_COMPONENT_PLUGINS_GROUP "Runtime")
set(CPACK_COMPONENT_RESOURCES_DISPLAY_NAME "Resources")
set(CPACK_COMPONENT_RESOURCES_GROUP "Runtime")
set(CPACK_COMPONENT_RUNTIMELIBRARIES_DISPLAY_NAME "Runtime Libraries")
set(CPACK_COMPONENT_RUNTIMELIBRARIES_GROUP "Runtime")
set(CPACK_COMPONENT_SOURCES_DISPLAY_NAME "Source Files")
set(CPACK_COMPONENT_SOURCES_GROUP "Development")

set(CPACK_ALL_INSTALL_TYPES User Developer Full)
set(CPACK_COMPONENT_APPLICATIONS_INSTALL_TYPES User Full)
set(CPACK_COMPONENT_DEVELOPMENTLIBRARIES_INSTALL_TYPES Developer Full)
set(CPACK_COMPONENT_EXTPLUGINS_INSTALL_TYPES User Developer Full)
set(CPACK_COMPONENT_HEADERS_INSTALL_TYPES Developer Full)
set(CPACK_COMPONENT_PLUGINS_INSTALL_TYPES User Developer Full)
set(CPACK_COMPONENT_RESOURCES_INSTALL_TYPES User Developer Full)
set(CPACK_COMPONENT_RUNTIMELIBRARIES_INSTALL_TYPES User Full)
set(CPACK_COMPONENT_SOURCES_INSTALL_TYPES Developer Full)

include(CPack)
