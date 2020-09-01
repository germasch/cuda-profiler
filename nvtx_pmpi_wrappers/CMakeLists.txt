
cmake_minimum_required(VERSION 3.17)

project(nvtx_pmpi_wrappers)

enable_language(C)

set(BUILD_SHARED_LIBS "ON")

find_package(MPI REQUIRED)
find_package(Python2 REQUIRED COMPONENTS Interpreter)
find_package(CUDAToolkit REQUIRED)

add_library(nvtx_pmpi nvtx_pmpi.c)
target_link_libraries(nvtx_pmpi PRIVATE CUDA::nvToolsExt)

set(_output "${CMAKE_CURRENT_BINARY_DIR}/nvtx_pmpi.c")
set(_input "${CMAKE_CURRENT_SOURCE_DIR}/nvtx.w")
set(_wrap "${CMAKE_CURRENT_SOURCE_DIR}/wrap/wrap.py")
add_custom_command(
  OUTPUT ${_output}
  COMMAND ${Python_EXECUTABLE} ${_wrap} -f -o ${_output} ${_input}
  MAIN_DEPENDENCY ${_input}
  DEPENDS ${_wrap}
  COMMENT "Wrapping ${_input}"
  VERBATIM
  )

install(TARGETS nvtx_pmpi)
