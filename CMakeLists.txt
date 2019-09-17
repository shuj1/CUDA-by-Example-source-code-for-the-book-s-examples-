cmake_minimum_required(VERSION 2.8)
project(CUDA_BY_EXAMPLE)

IF(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE Release)
ENDIF()

MESSAGE(STATUS "Build type: " ${CMAKE_BUILD_TYPE})

set(CMAKE_C_FLAGS_DEBUG "-O2 -g")
set(CMAKE_CXX_FLAGS_DEBUG "-O2 -g")

set(CMAKE_C_FLAGS_RELEASE "-O3")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

LIST(APPEND CMAKE_C_FLAGS "-Wall -Wextra -DUSE_NVTX")
LIST(APPEND CMAKE_CXX_FLAGS "-Wall -Wextra -DUSE_NVTX")

find_package(CUDA QUIET REQUIRED)
set(CUDA_PROPAGATE_HOST_FLAGS OFF)
SET(CUDA_HOST_COMPILER /usr/bin/g++)
LIST(APPEND CUDA_NVCC_FLAGS "--compiler-options -fno-strict-aliasing -use_fast_math")

# Check C++11 or C++0x support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
    set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11")
    add_definitions(-DCOMPILEDWITHC11)
    message(STATUS "Using flag -std=c++11.")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
    set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++0x")
    add_definitions(-DCOMPILEDWITHC0X)
    message(STATUS "Using flag -std=c++0x.")
else()
    message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
#find_package(Pangolin REQUIRED)

find_package(OpenGL REQUIRED)
find_package(GLU REQUIRED)
find_package(GLUT REQUIRED)


include_directories(
    ${PROJECT_SOURCE_DIR}
    ${PROJECT_SOURCE_DIR}/common
    ${EIGEN3_INCLUDE_DIR}
    #${Pangolin_INCLUDE_DIRS}
    ${OPENGL_INCLUDE_DIR}
    ${GLUT_DIR}
    ${GLU_DIR}
    )

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY lib)

cuda_include_directories(
    ${CUDA_TOOLKIT_ROOT_DIR}/samples/common/inc
    )

#cuda_add_library(${PROJECT_NAME} SHARED
cuda_add_library(${PROJECT_NAME}
    chapter03/hello_world.cu
    chapter03/enum_gpu.cu
    chapter03/set_gpu.cu
    chapter03/simple_device_call.cu
    chapter03/simple_kernel.cu
    chapter03/simple_kernel_params.cu

    chapter04/add_loop_cpu.cu
    chapter04/add_loop_gpu.cu
    chapter04/add_loop_long.cu
    chapter04/julia_cpu.cu
    chapter04/julia_gpu.cu

    chapter05/add_loop_blocks.cu
    chapter05/add_loop_long_blocks.cu
    chapter05/dot.cu
    chapter05/ripple.cu
    chapter05/shared_bitmap.cu

    chapter06/ray.cu
    chapter06/ray_noconst.cu

    chapter07/heat.cu
    chapter07/heat_2d.cu

    chapter08/heat.cu
    chapter08/ripple.cu
    chapter08/basic_interop.cu

    chapter09/hist_cpu.cu
    chapter09/hist_gpu_gmem_atomics.cu
    chapter09/hist_gpu_shmem_atomics.cu

    chapter10/basic_single_stream.cu
    chapter10/basic_double_stream.cu
    chapter10/basic_double_stream_correct.cu
    chapter10/copy_timed.cu

    chapter11/multidevice.cu
    chapter11/portable.cu
    chapter11/time_zero2.cu
    )

target_link_libraries(${PROJECT_NAME}
    ${OpenCV_LIBS}
    ${EIGEN3_LIBS}
    #${Pangolin_LIBRARIES}
    #nvToolsExt
    ${OPENGL_LIBRARIES}
    ${GLUT_LIBRARIES}
    #GL GLU glut glfw
    )

# Build examples
message(STATUS "Build chapter03 examples")
cuda_add_executable(hello_world chapter03/hello_world.cu)
target_link_libraries(hello_world ${PROJECT_NAME})
cuda_add_executable(enum_gpu chapter03/enum_gpu.cu)
target_link_libraries(enum_gpu ${PROJECT_NAME})
cuda_add_executable(set_gpu chapter03/set_gpu.cu)
target_link_libraries(set_gpu ${PROJECT_NAME})
cuda_add_executable(simple_device_call chapter03/simple_device_call.cu)
target_link_libraries(simple_device_call ${PROJECT_NAME})
cuda_add_executable(simple_kernel chapter03/simple_kernel.cu)
target_link_libraries(simple_kernel ${PROJECT_NAME})
cuda_add_executable(simple_kernel_params chapter03/simple_kernel_params.cu)
target_link_libraries(simple_kernel_params ${PROJECT_NAME})



message(STATUS "Build chapter04 examples")
cuda_add_executable(add_loop_cpu chapter04/add_loop_cpu.cu)
target_link_libraries(add_loop_cpu ${PROJECT_NAME})
cuda_add_executable(add_loop_gpu chapter04/add_loop_gpu.cu)
target_link_libraries(add_loop_gpu ${PROJECT_NAME})
cuda_add_executable(add_loop_long chapter04/add_loop_long.cu)
target_link_libraries(add_loop_long ${PROJECT_NAME})
cuda_add_executable(julia_cpu chapter04/julia_cpu.cu)
target_link_libraries(julia_cpu ${PROJECT_NAME})
cuda_add_executable(julia_gpu chapter04/julia_gpu.cu)
target_link_libraries(julia_gpu ${PROJECT_NAME})



message(STATUS "Build chapter05 examples")
cuda_add_executable(add_loop_blocks chapter05/add_loop_blocks.cu)
target_link_libraries(add_loop_blocks ${PROJECT_NAME})
cuda_add_executable(add_loop_long_blocks chapter05/add_loop_long_blocks.cu)
target_link_libraries(add_loop_long_blocks ${PROJECT_NAME})
cuda_add_executable(dot chapter05/dot.cu)
target_link_libraries(dot ${PROJECT_NAME})
cuda_add_executable(ripple chapter05/ripple.cu)
target_link_libraries(ripple ${PROJECT_NAME})
cuda_add_executable(shared_bitmap chapter05/shared_bitmap.cu)
target_link_libraries(shared_bitmap ${PROJECT_NAME})



message(STATUS "Build chapter06 examples")
cuda_add_executable(ray chapter06/ray.cu)
target_link_libraries(ray ${PROJECT_NAME})
cuda_add_executable(ray_noconst chapter06/ray_noconst.cu)
target_link_libraries(ray_noconst ${PROJECT_NAME})



message(STATUS "Build chapter07 examples")
cuda_add_executable(heat chapter07/heat.cu)
target_link_libraries(heat ${PROJECT_NAME})
cuda_add_executable(heat_2d chapter07/heat_2d.cu)
target_link_libraries(heat_2d ${PROJECT_NAME})



message(STATUS "Build chapter08 examples")
cuda_add_executable(heat_ch8 chapter08/heat.cu)
target_link_libraries(heat_ch8 ${PROJECT_NAME})
cuda_add_executable(ripple_ch8 chapter08/ripple.cu)
target_link_libraries(ripple_ch8 ${PROJECT_NAME})
cuda_add_executable(basic_interop chapter08/basic_interop.cu)
target_link_libraries(basic_interop ${PROJECT_NAME})



message(STATUS "Build chapter09 examples")
cuda_add_executable(hist_cpu chapter09/hist_cpu.cu)
target_link_libraries(hist_cpu ${PROJECT_NAME})
cuda_add_executable(hist_gpu_gmem_atomics chapter09/hist_gpu_gmem_atomics.cu)
target_link_libraries(hist_gpu_gmem_atomics ${PROJECT_NAME})
cuda_add_executable(hist_gpu_shmem_atomics chapter09/hist_gpu_shmem_atomics.cu)
target_link_libraries(hist_gpu_shmem_atomics ${PROJECT_NAME})



message(STATUS "Build chapter10 examples")
cuda_add_executable(basic_single_stream chapter10/basic_single_stream.cu)
target_link_libraries(basic_single_stream ${PROJECT_NAME})
cuda_add_executable(basic_double_stream chapter10/basic_double_stream.cu)
target_link_libraries(basic_double_stream ${PROJECT_NAME})
cuda_add_executable(basic_double_stream_correct chapter10/basic_double_stream_correct.cu)
target_link_libraries(basic_double_stream_correct ${PROJECT_NAME})
cuda_add_executable(copy_timed chapter10/copy_timed.cu)
target_link_libraries(copy_timed ${PROJECT_NAME})



message(STATUS "Build chapter11 examples")
cuda_add_executable(multidevice chapter11/multidevice.cu)
target_link_libraries(multidevice ${PROJECT_NAME})
cuda_add_executable(portable chapter11/portable.cu)
target_link_libraries(portable ${PROJECT_NAME})
cuda_add_executable(time_zero2 chapter11/time_zero2.cu)
target_link_libraries(time_zero2 ${PROJECT_NAME})


#unset(CMAKE_RUNTIME_OUTPUT_DIRECTORY)
#add_subdirectory(gpu)

