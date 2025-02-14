# Copyright 2020, 2021 Peter Dimov
# Distributed under the Boost Software License, Version 1.0.
# https://www.boost.org/LICENSE_1_0.txt

if(NOT DEFINED IDF_TARGET)

cmake_minimum_required(VERSION 3.5...3.16)

project(boost_container VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES C CXX)

add_library(boost_container
  src/alloc_lib.c
  src/dlmalloc.cpp
  src/global_resource.cpp
  src/monotonic_buffer_resource.cpp
  src/pool_resource.cpp
  src/synchronized_pool_resource.cpp
  src/unsynchronized_pool_resource.cpp
)

add_library(Boost::container ALIAS boost_container)

target_include_directories(boost_container PUBLIC include)

target_link_libraries(boost_container
  PUBLIC
    Boost::assert
    Boost::config
    Boost::core
    Boost::intrusive
    Boost::move
    Boost::static_assert
    Boost::type_traits
    Boost::winapi
)

target_compile_definitions(boost_container
  PUBLIC BOOST_CONTAINER_NO_LIB
  # Source files already define BOOST_CONTAINER_SOURCE
  # PRIVATE BOOST_CONTAINER_SOURCE
)

if(BUILD_SHARED_LIBS)
  target_compile_definitions(boost_container PUBLIC BOOST_CONTAINER_DYN_LINK)
else()
  target_compile_definitions(boost_container PUBLIC BOOST_CONTAINER_STATIC_LINK)
endif()

if(BUILD_TESTING AND EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/test/CMakeLists.txt")

  add_subdirectory(test)

endif()

else()

FILE(GLOB_RECURSE headers include/*.h include/*.hpp)

idf_component_register(
    SRCS
        ${headers}
#        src/alloc_lib.c
#        src/dlmalloc.cpp
        src/global_resource.cpp
        src/monotonic_buffer_resource.cpp
        src/pool_resource.cpp
#        src/synchronized_pool_resource.cpp
#        src/unsynchronized_pool_resource.cpp
    INCLUDE_DIRS
        include
    REQUIRES
        boost_assert
        boost_config
        boost_core
        boost_intrusive
        boost_move
        boost_static_assert
        boost_type_traits
#        boost_winapi
)

endif()
