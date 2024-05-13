cmake_minimum_required(VERSION 3.8)

project(SOEM C CXX)

include(ExternalProject)

ExternalProject_Add(soem_origin
  PREFIX soem
  GIT_REPOSITORY  https://github.com/OpenEtherCATsociety/SOEM.git
  GIT_TAG v1.4.0
  CMAKE_ARGS
   -DBUILD_TESTS=OFF
   -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/soem_install
  INSTALL_DIR "${CMAKE_CURRENT_BINARY_DIR}/soem_install"
)

install (
  DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/soem_install/
  DESTINATION ${CMAKE_INSTALL_PREFIX}
)

include_directories(${CMAKE_BINARY_DIR}/soem_install/include/soem)
if(WIN32)
  include_directories(${CMAKE_BINARY_DIR}/soem/src/soem_origin/oshw/win32/wpcap/Include)
  if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    link_directories(${CMAKE_BINARY_DIR}/soem/src/soem_origin/oshw/win32/wpcap/Lib/x64)
  endif()
endif()

link_directories(${CMAKE_BINARY_DIR}/soem_install/lib)

add_executable(test main.c)

target_link_libraries(test soem)
if(WIN32)
  target_link_libraries(test ws2_32.lib Packet.lib wpcap.lib Winmm.lib)
else()
  target_link_libraries(test pthread rt)
endif()

add_dependencies(test soem_origin)
