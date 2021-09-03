cmake_minimum_required(VERSION 3.12)

project(BOLT)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 14)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

enable_language(ASM)

message(STATUS ">>>>>>>>>>>>>")
message(STATUS "BOLT BUILD INFO:")
message(STATUS "\tSystem: ${CMAKE_SYSTEM_NAME}")
message(STATUS "\tProcessor: ${CMAKE_SYSTEM_PROCESSOR}")
message(STATUS "\tBuild Path: ${CMAKE_CURRENT_BINARY_DIR}")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=native -mavx -ffast-math -std=c++14 -fno-omit-frame-pointer -DNDEBUG -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native -mavx -ffast-math -std=c++14 -fno-omit-frame-pointer -DNDEBUG -O3")

if(CMAKE_SYSTEM_PROCESSOR MATCHES "(x86_64)|(X86_64)|(x64)|(X64)|(amd64)|(AMD64)|(i686)")

  file(GLOB test_src test/quantize/test*.cpp)
  add_executable(main test/main.cpp ${test_src})
  target_include_directories(main PRIVATE test/external test/quantize test/testing_utils)
  target_link_libraries(main PUBLIC bolt mithral)

  add_library(bolt STATIC src/quantize/bolt.cpp)
  target_include_directories(bolt PUBLIC src/ src/external src/external/eigen/ src/external/eigen/Eigen src/include src/quantize src/utils)
  set_target_properties(bolt PROPERTIES LINK_FLAGS "-static-libstdc++ -static-libgcc")

  add_library(mithral STATIC src/quantize/mithral.cpp)
  target_include_directories(mithral PUBLIC src/ src/external src/external/eigen/ src/external/eigen/Eigen src/include src/quantize src/utils)
  set_target_properties(mithral PROPERTIES LINK_FLAGS "-static-libstdc++ -static-libgcc")

  endif()
