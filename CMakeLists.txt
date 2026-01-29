cmake_minimum_required(VERSION 3.10)
project(gflip3d LANGUAGES CXX CUDA)
find_package(CUDAToolkit REQUIRED)
set(CMAKE_BUILD_TYPE Release)

# Use a modern GPU architecture; override via -DGDEL3D_CUDA_ARCH=xx if needed.
set(GDEL3D_CUDA_ARCH "${CMAKE_CUDA_ARCHITECTURES}")
if(NOT GDEL3D_CUDA_ARCH)
  set(GDEL3D_CUDA_ARCH "89")
endif()
set(CMAKE_CUDA_ARCHITECTURES ${GDEL3D_CUDA_ARCH})
add_library(gdel3d STATIC
  GDelFlipping/src/DelaunayChecker.cpp
  GDelFlipping/src/InputCreator.cpp
  GDelFlipping/src/RandGen.cpp
  GDelFlipping/src/gDel3D/GpuDelaunay.cu
  GDelFlipping/src/gDel3D/CPU/predicates.cpp
  GDelFlipping/src/gDel3D/CPU/PredWrapper.cpp
  GDelFlipping/src/gDel3D/CPU/Splaying.cpp
  GDelFlipping/src/gDel3D/CPU/Star.cpp
  GDelFlipping/src/gDel3D/GPU/KerDivision.cu
  GDelFlipping/src/gDel3D/GPU/KerPredicates.cu
  GDelFlipping/src/gDel3D/GPU/ThrustWrapper.cu
)

target_include_directories(gdel3d PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}/GDelFlipping/Main
)

set_target_properties(gdel3d PROPERTIES
  CUDA_SEPARABLE_COMPILATION ON
  CUDA_STANDARD 11
  CUDA_STANDARD_REQUIRED ON
  POSITION_INDEPENDENT_CODE ON
)

target_link_libraries(gdel3d PUBLIC CUDA::cudart)

add_executable(gflip3d GDelFlipping/src/Demo.cpp)
target_link_libraries(gflip3d PRIVATE gdel3d)
