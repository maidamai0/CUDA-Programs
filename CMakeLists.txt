cmake_minimum_required(VERSION 3.24)

get_filename_component(PROJECT_NAME ${CMAKE_CURRENT_LIST_FILE} NAME_WLE)
project(${PROJECT_NAME} LANGUAGES CXX CUDA)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_CUDA_STANDARD 20)
set(CMAKE_CUDA_EXTENSIONS OFF)
set(CMAKE_CUDA_ARCHITECTURES native)

if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
  set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
  add_compile_options("$<$<COMPILE_LANGUAGE:CXX>:/EHsc>")
  add_compile_options("$<$<COMPILE_LANGUAGE:CXX>:/Zc:__cplusplus>")
endif()
add_compile_options("$<$<COMPILE_LANGUAGE:CCUDA>:--use_fast_math>")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
include_directories(${CMAKE_CURRENT_LIST_DIR}/include)

find_package(CUDAToolkit REQUIRED)
link_libraries(CUDA::toolkit CUDA::cublas)


find_package(OpenCV REQUIRED)
include_directories( ${OpenCV_INCLUDE_DIRS})
link_libraries(${OpenCV_LIBS})

file(GLOB src_dirs CONFIGURE_DEPENDS Chapter* Appendicies)
foreach(src_dir ${src_dirs})
    get_filename_component(dir_name ${src_dir} NAME)
    file(GLOB_RECURSE srcs CONFIGURE_DEPENDS ${dir_name}/*.cpp ${dir_name}/*.cu)
    foreach(src ${srcs})
        get_filename_component(file_name ${src} NAME_WLE)
        string(TOLOWER ${file_name} file_name)
        if(src MATCHES ".cpp$")
            set(file_name ${file_name}_cpu)
        endif()
        add_executable(${file_name} ${src})
    endforeach(src ${srcs})
endforeach()

