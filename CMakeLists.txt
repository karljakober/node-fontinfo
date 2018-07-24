cmake_minimum_required(VERSION 3.0)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

set(PROJECT_NAME "node-fontinfo")
project(${PROJECT_NAME})

include(ucm)

set(fontinfo_SOURCES
	src/fontinfo/fontinfo.c)

set(fontinfo_HEADERS
	src/fontinfo/fontinfo.h
	src/fontinfo/endian.h)

add_library(fontinfo SHARED ${fontinfo_SOURCES})

find_package(FreeType)
if(FREETYPE_FOUND)
    include_directories(${FREETYPE_INCLUDE_DIR})
    set(STATUS_FREETYPE "OK")
else()
    set(STATUS_FREETYPE "not found")
endif()


message(
    "System Dependencies\n"
    "-------------------\n"
    "* freetype                     : ${STATUS_FREETYPE}\n"
)

add_library(${PROJECT_NAME} SHARED "src/module.cpp")
target_link_libraries(${PROJECT_NAME} fontinfo ${CMAKE_JS_LIB})
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_JS_INC})

if(FONTINFO_BUILD_TESTS)
	add_executable(simple_test "test/simple_test.c")
	target_link_libraries(simple_test fontinfo)
	target_include_directories(simple_test PUBLIC "src")
endif()
