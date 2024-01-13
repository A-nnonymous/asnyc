cmake_minimum_required(VERSION 3.27)

project(async LANGUAGES CXX C)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#------------------- CPU Compiler settings-------------------
set(CMAKE_CXX_COMPILER /usr/bin/g++-12 )
# DPU related compile flags generating
execute_process(
  COMMAND dpu-pkg-config --libs --cflags dpu
  OUTPUT_VARIABLE CXX_DPU_FLAGS
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${CXX_DPU_FLAGS} -gdwarf-4")

#------------------- DPU Compiler settings-------------------
set(CMAKE_C_COMPILER "dpu-upmem-dpurte-clang" )  
set(CMAKE_C_FLAGS " -O2")  

#------------------- External library -----------------------
# Adding nvidia stdexec directory, but disabling all tests to save compiling time.
option(STDEXEC_BUILD_EXAMPLES "Build stdexec examples" OFF)
option(STDEXEC_BUILD_TESTS "Build stdexec tests" OFF)
add_subdirectory(ext/stdexec)

#------------------- Path variables -------------------------
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/cpu_bin)
set(DPU_EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/dpu_bin)

include_directories(include)
add_subdirectory(src_cpu)
add_subdirectory(src_dpu)