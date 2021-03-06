cmake_minimum_required(VERSION 2.8)
project(unabto-multi-tunnel C ASM)

set(UNABTO_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/../..)

option(LOG_ALL "Print all unabto log statements" OFF)

include(${UNABTO_ROOT}/build/cmake/unabto_project.cmake)

if(EXTERNAL_PREFIX) 
  include_directories(${EXTERNAL_PREFIX}/include)
  link_directories(${EXTERNAL_PREFIX}/lib)
endif()

set(unabto_server_unabto_multi_tunneling_src ${CMAKE_CURRENT_SOURCE_DIR}/src)

include_directories(
  # make it possible to override unabto_config.h with a supplied file
  # to the build tree.
  ${CMAKE_CURRENT_BINARY_DIR}

  ${unabto_include_directories}
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  )

SET(ENABLE_TUNNEL_TCP 1)
SET(ENABLE_TUNNEL_UART 1)
SET(ENABLE_TUNNEL_ECHO 1)
SET(TUNNEL_LIBRARY_NAME unabto_multi_tunnel_library)

# build the tunnelling library with the unabto_config.h defined in this project.
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../../src/modules/tunnel ${CMAKE_CURRENT_BINARY_DIR}/multi_tunnel_library)


source_group(tunnel\\src FILES ${unabto_tunnel_src})

set(tunnel_src
  ${unabto_server_unabto_multi_tunneling_src}/platform_checks.c
  )

add_definitions(${unabto_definitions})

if (UNABTO_HAVE_EPOLL)
  add_definitions("-DNABTO_ENABLE_EPOLL=1")
endif()

# Called by developer - build and install

add_executable(unabto_multi_tunnel ${tunnel_src} ${unabto_server_unabto_multi_tunneling_src}/main.c)

target_link_libraries(unabto_multi_tunnel unabto_multi_tunnel_library ${unabto_link_libraries})

install( TARGETS unabto_multi_tunnel
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib)
