cmake_minimum_required(VERSION 2.8)
project(tmxlite)
SET(PROJECT_NAME tmxlite)


SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/")

if(NOT TMXLITE_STATIC_LIB)
  SET(TMXLITE_STATIC_LIB FALSE CACHE BOOL "Should tmxlite be built as a static or shared lib?")
endif()

SET(PROJECT_STATIC_RUNTIME FALSE CACHE BOOL "Use statically linked standard/runtime libraries?")

if(CMAKE_COMPILER_IS_GNUCXX OR APPLE)
  if(PROJECT_STATIC_RUNTIME)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++14 -static")
  else()
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++14")
  endif()
endif()

SET_PROPERTY(
	DIRECTORY
	APPEND PROPERTY COMPILE_DEFINITIONS
	$<$<CONFIG:Debug>:_DEBUG_>
	$<$<CONFIG:Release>:NDEBUG>
	$<$<BOOL:TMXLITE_STATIC_LIB>:TMXLITE_STATIC>
	)

SET_PROPERTY(
	DIRECTORY
	PROPERTY DEBUG_POSTFIX
	$<$<BOOL:TMXLITE_STATIC_LIB>:-s>-d
	)

SET_PROPERTY(
	DIRECTORY
	PROPERTY RELEASE_POSTFIX
	$<$<BOOL:TMXLITE_STATIC_LIB>:-s>
	)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

file(GLOB PROJECT_SRC "src/*.cpp" "src/*.c")

if(TMXLITE_STATIC_LIB)
  add_library(${PROJECT_NAME} STATIC ${PROJECT_SRC})
else()
  add_library(${PROJECT_NAME} SHARED ${PROJECT_SRC})
endif()   

target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/tmxlite DESTINATION include)
if(TMXLITE_STATIC_LIB)
  install(TARGETS ${PROJECT_NAME} EXPORT tmxlite-targets DESTINATION lib)
else()
  install(TARGETS ${PROJECT_NAME} EXPORT tmxlite-targets 
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin)
endif()
