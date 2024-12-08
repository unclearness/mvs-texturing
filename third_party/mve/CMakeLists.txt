cmake_minimum_required(VERSION 3.1)

project(MVE)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED yes)

# Compiler flags
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    add_compile_options(/W3)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    add_compile_options(-Wall -Wextra -Wundef -pedantic)
    add_compile_options(-mpopcnt)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    add_compile_options(-Weverything -pedantic)
endif()

if(WIN32)
    add_definitions(-DWIN32_LEAN_AND_MEAN -DVC_EXTRALEAN -DNOMINMAX)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    if(MSVC)
        add_compile_options(/MP)
        option(MVE_BUILD_WITH_SSE2 "Build MVE with SSE2 support" ON)
        option(MVE_BUILD_WITH_SSE3 "Build MVE with SSE3 support" ON)
        if(MVE_BUILD_WITH_SSE2)
            add_definitions(-D__SSE2__)
        endif()
        if(MVE_BUILD_WITH_SSE3)
            add_definitions(-D__SSE3__)
        endif()
    endif()

    set(CMAKE_PREFIX_PATH "${CMAKE_CURRENT_SOURCE_DIR}/3rdparty")
    set(JPEG_NAMES "libjpeg")
    set(CPACK_GENERATOR "ZIP")
else()
    set(CPACK_GENERATOR "TGZ")
endif()
include(CPack)

if(NOT APPLE)
find_package(OpenMP REQUIRED)
add_compile_options(${OpenMP_CXX_FLAGS})
endif()

include_directories(libs)

find_package(PNG)

if (NOT PNG_FOUND)
  # If not found, build it from source

  # zlib for libpng
  add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib)
  set(ZLIB_ROOT ${CMAKE_CURRENT_BINARY_DIR}/third_party/zlib) # zlibのルートディレクトリを設定
  set(ZLIB_LIBRARY ${CMAKE_CURRENT_BINARY_DIR}/zlib/) # Static library for zlib
  if(WIN32)
    #set(ZLIB_LIBRARY ${ZLIB_ROOT}/Release/zlibstatic.lib)
    set(ZLIB_LIBRARY ${ZLIB_ROOT}/$<CONFIG>/zlibstatic$<IF:$<CONFIG:Debug>,d,>.lib)
  else()
    # TODO: Really?
    set(ZLIB_LIBRARY ${ZLIB_ROOT}//libz.a)
  endif()
  set(ZLIB_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib ${ZLIB_ROOT})

  set(PNG_STATIC ON)  # Static build of libpng
  set(PNG_SHARED OFF) # No shared build of libpng
  set(ZLIB_FOUND TRUE) # TO pass the check in libpng

  set(LIBPNG_ROOT ${CMAKE_CURRENT_BINARY_DIR}/third_party/libpng)
  add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/third_party/libpng)
  set(PNG_FOUND TRUE)

  set(PNG_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third_party/libpng ${LIBPNG_ROOT})

  # If add_subdirectory() by parent project
  # TODO: Fix
  cmake_policy(SET CMP0079 NEW)

  # Need explcitly link zlib
  target_link_libraries(png_static PRIVATE ${ZLIB_LIBRARY})

  message(STATUS "ZLIB_INCLUDE_DIR:" ${ZLIB_INCLUDE_DIR})
  message(STATUS "ZLIB_LIBRARIES:" ${ZLIB_LIBRARIES})

  if(WIN32)
    set(PNG_LIBRARY ${LIBPNG_ROOT}/$<CONFIG>/libpng16_static$<IF:$<CONFIG:Debug>,d,>.lib)
  else()
    # TODO: Really?
    set(PNG_LIBRARY ${LIBPNG_ROOT}/libpng.a)
  endif()
  
  set(PNG_LIBRARIES ${PNG_LIBRARY} ${ZLIB_LIBRARY})
endif()

find_package(JPEG)
if (NOT JPEG_FOUND)
    add_definitions(-DMVE_NO_JPEG_SUPPORT)
    set(JPEG_LIBRARIES)
endif()

find_package(TIFF)
if (NOT TIFF_FOUND)
    add_definitions(-DMVE_NO_TIFF_SUPPORT)
    set(TIFF_LIBRARIES)
endif()

add_subdirectory(libs)
